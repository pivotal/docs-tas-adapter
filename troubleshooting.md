# Troubleshooting Application Service Adapter

This topic collects documentation related to the generic techniques for diagnosing the system health as well as specific steps to be taken in known scenarios.

* [Generic Troubleshooting Techniques](#generic-troubleshooting-techniques)
  * [Application Service Adapter Logs](#application-logs)
  * [TAP Logs](#tap-logs)
  * [CFApp Logs](#cfapp-logs)
  * [Kubernetes System Events](#system-events)
  * [System Object Types](#system-object-types)
* [Common Failure Scenarios](#commmon-failure-scenarios)
* [OpenShift Failure Scenarios](#openshift-failure-scenarios)
* [Cartographer Failure Scenarios](#cartographer-failure-scenarios)


## <a id="generic-troubleshooting-techniques"></a>Generic Troubleshooting Techniques

This section contains generic techniques that can be used to gather information about the status of the Application Service Adapter to aid in troubleshooting. We recommend keeping up to date with the latest version of the Application Service Adapter to get access to the latest bug fixes and stability improvements.

### <a id="application-logs"></a>Application Service Adapter Logs

There are several Application Service Adapter deployments whose logs can be queried to gather information on failures that occurred within the system. 

To fetch recent logs from all components of an Application Service Adapter installation:

    ```bash
    kapp logs --app tas-adapter
    ```

To fetch recent logs from a specific deployment:

    ```bash
    kapp logs --app tas-adapter --pod-name DEPLOYMENT-NAME%
    ```
    Where DEPLOYMENT-NAME is the name of the Kubernetes Deployment (e.g.
    "korifi-api-deployment").

To stream logs instead of fetching the most recent logs, add the `--follow` flag to the above `kapp logs` commands.
For additional details and options, refer to the `kapp logs --help` help text.

#### Deployments

The following is a brief description of the specific Application Service Adapter deployments and what their main responsibilities are to help isolate which logs to query when troubleshooting.

1. The `korifi-api-deployment` Deployment is tasked with responding to API requests sent to the Application Service Adapter. Logs for failures related to the image registry may also show up in this Deployment's logs.
1. The `korifi-controllers-controller-manager` Deployment is tasked with processing commands for the Application Service Adapter. This Deployment's logs is an excellent starting point when debugging failures as it is where most commands flow through before being processed by more specific components (The exception being API commands that fail before getting to the controller manager).
1. The `tas-adapter-telemetry-informer` Deployment is tasked with handling all outgoing telemetry.
1. The `korifi-kpack-build-controller-manager` Deployment is tasked with building Images when using the default builder/runner flow.
1. The `korifi-statefulset-runner-controller-manager` Deployment is tasked with generating StatefulSets for apps when using the default builder/runner flow.
1. The `korifi-job-task-runner-controller-manager` Deployment is tasked with handling all task related functionality.
1. The `cartographer-builder-runner-controller-manager` Deployment is tasked with creating Cartographer Workloads for apps when using the experimental Cartographer builder/runner flow.

### <a id="tap-logs"></a>TAP Logs

While not directly part of the Application Service Adapter, there are several TAP deployments that are utilized by the Application Service Adapter and can also provide further debug information on failures that occur. 

To fetch recent logs from a given TAP application:

    ```bash
    kapp logs --app APP-NAME -n tap-install
    ```
    Where APP-NAME is the name of the TAP application (e.g.
    "buildservice-ctrl").

To stream logs instead of fetching the most recent logs, add the `--follow` flag to the above `kapp logs` command.
For additional details and options, refer to the `kapp logs --help` help text.

#### Deployments

The following is a brief description of the specific TAP deployments used by the Application Service Adapter and what their main responsibilities are to help determine which logs to query when troubleshooting.

1. The `buildservice-ctrl` application is tasked with processing any Tanzu Build Service (kpack) commands. If any failures in the build image process occurs in Tanzu Build Service itself, this application's logs can provide further information.
1. The `contour-ctrl` application is tasked with creating an ingress into the system. If a failure to connect to the Application Service Adapter or an Application occurs, this application's logs can provide further information.
1. The `cartographer-ctrl` application is tasked with processing Cartographer ClusterSupplyChains when using the experimental Cartographer builder/runner flow. If a failure to create an Image, Build, ConfigMap, or StatefulSet occurs, this application's logs can provide further information.

### <a id="cfapp-logs"></a>CFApp Logs

For CFApps that have been staged, the cf logs command can be used to get more information in the event of run failures:

```bash
cf logs <CFApp name>
```

### <a id="system-events"></a>Kubernetes System Events

Kubernetes system events can provide debug information in cases where system restrictions prevent objects from being created. These events may be caused by insufficient resources or evicted pods, for example. The logs for the system can be queried with the following command:

```bash
kubectl get events -A
```

### <a id="system-object-types"></a>System Object Types

There are system objects created/used by Application Service Adapter that can be useful in debugging failures. Describing the objects can show error messages in their status fields. 
Whether objects are present can also be useful in determining where in the command process failures have occurred and which logs should be queried for more information. 

For example, if an object in the image build chain is missing, the component in the "Created By" column can be queried for logs to see if any useful error messages are present.
If no useful logs are found there, identify the previous object in the image build chain and check the logs of the component listed in the "Reconciled By" column to see if there are any error messages. 

Instructions for querying logs can be found for [Application Service Adapter](#application-logs) and [TAP](#tap-logs). 

Some Kubernetes objects exist within a CFOrg or CFSpace namespace. To find the corresponding namespace of a CFOrg or CFSpace, you can run:

```bash
cf org --guid <CFOrg name>
cf space --guid <CFSpace name>
```

#### Installed Object Types
| Kind                      | Component    | Namespace or Scope   | Notes                                                                                                                                                                                                                                                                                                                                                                                |
|---------------------------|--------------|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ClusterBuilder            | kpack        | Cluster              | ClusterBuilders are Tanzu Build Service (kpack) components installed by TAP that are used to build images. If they are not present, image builds may not complete successfully.                                                                                                                                                                                                      |
| Deployment                | kubernetes   | `tas-adapter-system` | All of the Application Service Adapter Deployment's `Ready` counts should be listed as `1/1` except for `korifi-api-deployment` which should be `2/2`. Should this not be the case, the system is not functioning correctly and the status/events fields of the inoperable Deployment should be analyzed. This issue can sometimes be related to RBAC.                               |
| ClusterRole               | kubernetes   | Cluster              | ClusterRoles are created to manage system privileges at the cluster scope.                                                                                                                                                                                                                                                                                                           |
| ClusterRoleBinding        | kubernetes   | Cluster              | ClusterRoleBindings are created to manage system privileges at the cluster scope.                                                                                                                                                                                                                                                                                                    |
| ServiceAccount            | kubernetes   | `cf`                 | ServiceAccounts are created to manage CF permissions. Some ServiceAccounts are designed to be propagated to CFOrg/CFSpace namespaces. These objects will be designated with the `cloudfoundry.org/propagate-service-account: "true"` annotation.                                                                                                                                     |
| RoleBinding               | kubernetes   | `cf`                 | RoleBindings are created to manage CF permissions. Some RoleBindings are designed to be propagated to CFOrg/CFSpace namespaces. These objects will be designated with the `cloudfoundry.org/propagate-cf-role: "true"` annotation.                                                                                                                                                   |
| SecurityContextConstraint | openshift    | Cluster              | SecurityContextConstraints are specific to OpenShift and are used to grant required permissions to Application Service Adapter components in that environment. These CRDs do not exist in non-OpenShift environments. An associated ClusterRole, ClusterRoleBinding, and RoleBinding will also be installed if the `kubernetes_distribution` shared setting is set to `openshift`.   |
| ClusterSupplyChain        | Cartographer | Cluster              | When using the experimental Cartographer builder/runner flow, a ClusterSupplyChain is created during Application Service Adapter installation at the cluster scope. This is what the `cartographer-ctrl` uses to creates the Image, Build, ConfigMap, and StatefulSets in the builder/runner flow.                                                                                   |

#### CFOrg/CFSpace Object Types
| Kind            | Component | Namespace or Scope | Created By                   | Reconciled By                                      | Notes                                                                                                                                                                    |
|-----------------|-----------|--------------------|------------------------------|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFOrg           | CF        | `cf`               | `korifi-api-deployment`      | `korifi-controllers-controller-manager`            | CFOrg resources are created by the `cf create-org` command. A corresponding namespace should also be created by the `korifi-controllers-controller-manager`.             |
| CFSpace         | CF        | CFOrg Namespace    | `korifi-api-deployment`      | `korifi-controllers-controller-manager`            | CFSpace resources are created by the `cf create-space` command. A corresponding namespace should also be created by the `korifi-controllers-controller-manager`.         |

#### Image Build Object Types (Default Builder/Runner)
All image build object types are located within the targeted CFSpace Namespace.

| Kind                        | Component | Created By                              | Reconciled By                                | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------|-----------|-----------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFApp                       | CF        | `korifi-api-deployment`                 | `korifi-controllers-controller-manager`      | The 1st object in the image build chain that's created when the `cf push` command is run. The CFApp resource represents the Application.                                                                                                                                                                                                                                                                                                 |
| CFPackage                   | CF        | `korifi-api-deployment`                 | `korifi-controllers-controller-manager`      | The 2nd object in the image build chain that's created when the `cf push` command is run. The CFPackage resource contains a reference to the uploaded app source code.                                                                                                                                                                                                                                                                   |
| CFBuild                     | CF        | `korifi-api-deployment`                 | `korifi-controllers-controller-manager`      | The 3rd object in the image build chain that's created when the `cf push` command is run. The CFBuild resource initiates the build and eventually contains a reference to the runnable app image.                                                                                                                                                                                                                                        |
| BuildWorkload               | CF        | `korifi-controllers-controller-manager` | `korifi-kpack-build-controller-manager`      | The 4th object in the image build chain that's created when the `cf push` command is run. The BuildWorkload is an intermediate resource that contains information about the build.                                                                                                                                                                                                                                                       |
| Image                       | kpack     | `korifi-kpack-build-controller-manager` | `buildservice-ctrl`                          | The 5th object in the image build chain that's created when the `cf push` command is run. The full object path for Image objects are kpack.io/v1alpha2/images. This may need to be specified when querying in certain environments like OpenShift since there are other resources with the same name. The Image is a kpack resource that provides configuration to build and maintain a docker image utilizing Cloud Native Buildpacks.  |
| Build                       | kpack     | `buildservice-ctrl`                     | NA                                           | The 6th object in the image build chain that's created when the `cf push` command is run. The full object path for Build objects are kpack.io/v1alpha2/builds. This may need to be specified when querying in certain environments like OpenShift since there are other resources with the same name. The Build is a kpack resource that schedules and runs a single Cloud Native Buildpacks build. The Build object spawns a build Pod. |

#### Run App Object Types (Default Builder/Runner)
All run app object types are located within the targeted CFSpace Namespace.

| Kind         | Component  | Created By                                     | Reconciled By                                  | Notes                                                                                                                                                                                                                                 |
|--------------|------------|------------------------------------------------|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFProcess    | CF         | `korifi-api-deployment`                        | `korifi-controllers-controller-manager`        | The 1st object in the app run chain that's created when the `cf push` command is run. The CFProcess resource represents a single Process.                                                                                             |
| AppWorkload  | CF         | `korifi-controllers-controller-manager`        | `korifi-statefulset-runner-controller-manager` | The 2nd object in the app run chain that's created when the `cf push` command is run. The AppWorkload is an intermediate resource that contains information about the Application/Process.                                            |
| StatefulSet  | Kubernetes | `korifi-statefulset-runner-controller-manager` | NA                                             | The 3rd object in the app run chain that's created when the `cf push` command is run. The StatefulSet is a Kubernetes resource that represents a single Process for an Application. There should be a StatefulSet for each CFProcess. |
| ReplicaSet   | Kubernetes | Kubernetes                                     | NA                                             | The 4th object in the app run chain that's created when the `cf push` command is run. The ReplicaSet is created by Kubernetes to maintain a stable set of replica Pods for the StatefulSet.                                           |
| Pod          | Kubernetes | Kubernetes                                     | NA                                             | The App Process Pods are last in the app run chain and created by Kubernetes based on the ReplicaSet. The number of Pods are based on the ReplicaSet replicas parameter.                                                              |
| CFRoute      | CF         | `korifi-api-deployment`                        | `korifi-controllers-controller-manager`        | CFRoutes are created to be able to reach running apps. They can be created as part of the `cf push` command if a default or random route is specified, defined in a manifest, or as part of the `cf create-route` command.            |
 | HTTPProxy    | contour    | `korifi-controllers-controller-manager`        | NA                                             | The HTTPProxy is a Contour resource that is created by the `korifi-controllers-controller-manager` to reach running apps.                                                                                                             |


#### Image Build Object Types (Cartographer Builder/Runner)
All image build object types are located within the targeted CFSpace Namespace.

| Kind             | Component     | Created By                                       | Reconciled By                                    | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|------------------|---------------|--------------------------------------------------|--------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFApp            | CF            | `korifi-api-deployment`                          | `korifi-controllers-controller-manager`          | The 1st object in the image build chain that's created when the `cf push` command is run.  The CFApp resource represents the Application.                                                                                                                                                                                                                                                                                                |
| CFPackage        | CF            | `korifi-api-deployment`                          | `korifi-controllers-controller-manager`          | The 2nd object in the image build chain that's created when the `cf push` command is run. The CFPackage resource contains a reference to the uploaded app source code.                                                                                                                                                                                                                                                                   |
| CFBuild          | CF            | `korifi-api-deployment`                          | `korifi-controllers-controller-manager`          | The 3rd object in the image build chain that's created when the `cf push` command is run. The CFBuild resource initiates the build and eventually contains a reference to the runnable app image.                                                                                                                                                                                                                                        |
| BuildWorkload    | CF            | `korifi-controllers-controller-manager`          | `cartographer-builder-runner-controller-manager` | The 4th object in the image build chain that's created when the `cf push` command is run. The BuildWorkload is an intermediate resource that contains information about the build.                                                                                                                                                                                                                                                       |
| Workload         | Cartographer  | `cartographer-builder-runner-controller-manager` | `cartographer-ctrl`                              | The 5th object in the image build chain that's created when the `cf push` command is run. The Workload is a Cartographer resource that contains information used by the `buildservice-ctrl` in conjunction with the SupplyChain to create an Image and later a ConfigMap containing the StatefulSet definition.                                                                                                                          |
| Image            | kpack         | `cartographer-ctrl`                              | `buildservice-ctrl`                              | The 6th object in the image build chain that's created when the `cf push` command is run. The full object path for Image objects are kpack.io/v1alpha2/images. This may need to be specified when querying in certain environments like OpenShift since there are other resources with the same name. The Image is a kpack resource that provides configuration to build and maintain a docker image utilizing Cloud Native Buildpacks.  |
| Build            | kpack         | `buildservice-ctrl`                              | NA                                               | The 7th object in the image build chain that's created when the `cf push` command is run. The full object path for Build objects are kpack.io/v1alpha2/builds. This may need to be specified when querying in certain environments like OpenShift since there are other resources with the same name. The Build is a kpack resource that schedules and runs a single Cloud Native Buildpacks build. The Build object spawns a build Pod. |

#### Run App Object Types (Cartographer Builder/Runner)
All run app object types are located within the targeted CFSpace Namespace.

| Kind        | Component  | Created By                                     | Reconciled By                                    | Notes                                                                                                                                                                                                                                                                                                                                                          |
|-------------|------------|------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFProcess   | CF         | `korifi-api-deployment`                        | `korifi-controllers-controller-manager`          | The 1st object in the app run chain that's created when the `cf push` command is run. The CFProcess resource represents a single Process.                                                                                                                                                                                                                      |
| AppWorkload | CF         | `korifi-controllers-controller-manager`        | `cartographer-builder-runner-controller-manager` | The 2nd object in the app run chain that's created when the `cf push` command is run.  The AppWorkload is an intermediate resource that contains information about the Application/Process. `cartographer-builder-runner-controller-manager` edits the Cartographer Workload created during the image build process with the information from the AppWorkload. |
| ConfigMap   | Kubernetes | `cartographer-ctrl`                            | NA                                               | The 3rd object in the app run chain that's created when the `cf push` command is run. A Kubernetes ConfigMap resource containing the StatefulSet definition is created that `cartographer-ctrl` uses to spawns a StatefulSet by means of kapp.                                                                                                                 |
| StatefulSet | Kubernetes | `cartographer-ctrl`                            | NA                                               | The 4th object in the app run chain that's created when the `cf push` command is run. The StatefulSet is a Kubernetes resource that represents a single Process for an Application. There should be a StatefulSet for each CFProcess.                                                                                                                          |
| ReplicaSet  | Kubernetes | Kubernetes                                     | NA                                               | The 5th object in the app run chain that's created when the `cf push` command is run. The ReplicaSet is created by Kubernetes to maintain a stable set of replica Pods for the StatefulSet.                                                                                                                                                                    |
| Pod         | Kubernetes | Kubernetes                                     | NA                                               | The App Process Pods are last in the app run chain and created by Kubernetes based on the ReplicaSet. The number of Pods are based on the ReplicaSet replicas parameter.                                                                                                                                                                                       |
| CFRoute     | CF         | `korifi-api-deployment`                        | `korifi-controllers-controller-manager`          | CFRoutes are created to be able to reach running apps. They can be created as part of the `cf push` command if a default or random route is specified, defined in a manifest, or as part of the `cf create-route` command.                                                                                                                                     |
| HTTPProxy   | contour    | `korifi-controllers-controller-manager`        | NA                                               | The HTTPProxy is a Contour resource that is created by the `korifi-controllers-controller-manager` to reach running apps.                                                                                                                                                                                                                                      |

#### Run Task Object Types
All run task object types are located within the targeted CFSpace Namespace.

| Kind         | Component  | Created By                                  | Reconciled By                                | Notes                                                                                                                                                |
|--------------|------------|---------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFTask       | CF         | `korifi-api-deployment`                     | `korifi-controllers-controller-manager`      | The 1st object in the run task chain that's created when the `cf run-task` command is run.                                                           |
| TaskWorkload | CF         | `korifi-controllers-controller-manager`     | `korifi-job-task-runner-controller-manager`  | The 2nd object in the run task chain that's created when the `cf run-task` command is run.                                                           |
| Job          | Kubernetes | `korifi-job-task-runner-controller-manager` | NA                                           | The 3rd object in the run task chain that's created when the `cf run-task` command is run.                                                           |
| Pod          | Kubernetes | Kubernetes                                  | NA                                           | The running Job Pods are last in the run task chain and created by Kubernetes based on the Job. The Job Pod will complete when its command is run.   |

## <a id="commmon-failure-scenarios"></a>Common Failure Scenarios

This section contains common failure scenarios and describes the appropriate troubleshooting techniques that can be used to gather further information and solve the issue.

### Organization not found when creating an Org.
#### Symptom
When I run `cf create org my-org`, the following error message is returned:
```bash
Organization 'my-org' not found
```
#### Possible Causes
The user creating the CFOrg is not bound to the `korifi-controllers-admin` ClusterRole. 
#### Troubleshooting Steps/Potential Solutions
1. Verify the user in the `cf create org` command output.
```bash
Creating org my-org as cf-admin...
```
1. Verify that there is a RoleBinding in the `cf` namespace binding the `korifi-controllers-admin` ClusterRole to the given user and that it has the `cloudfoundry.org/propagate-cf-role: "true"` annotation.
1. If that RoleBinding does not exist, either switch to a user that is bound to the `korifi-controllers-admin` ClusterRole or create a RoleBinding as a cluster admin.

### Pushing an app fails to build an image
#### Symptom
When I run `cf push`, the CF CLI does not respond with a built image.
#### Possible causes
1. ClusterBuilder is not ready.
1. A failure occurred in one of the TAP or Application Service Adapter components.
#### Troubleshooting Steps/Potential Solutions
1. Check the status of the ClusterBuilders by running `kubectl get clusterbuilder`. The `Ready` column should show `True`.
1. If the ClusterBuilder's `Ready` state is not `True`, run `kubectl describe clusterbuilder` and check the status section for more information.
1. Walk through the list of [image build object types](#system-object-types) and check if any objects are missing. For any missing objects, check the logs of the [TAP](#tap-logs) or [Application Service Adapter](#application-logs) component listed in the "Created By" column to see if there are any error messages.
1. If the component listed in the "Created By" column does not have any helpful error messages, identify the previous object in the image build chain and check the logs of the component listed in the "Reconciled By" column to see if there are any error messages.

### Pushing an app fails to upload an image to the image registry
#### Symptom
When I run `cf push`, the following output/error message is returned:
```bash
    Unexpected Response
    Response Code: 500
    Code: 0, Title: , Detail: {"errors":[{"detail":"An unknown error occurred.","title":"UnknownError","code":10001}]}
```
#### Possible Causes
The image registry is unavailable/unauthorized.
#### Troubleshooting Steps/Potential Solutions
1. Check the `korifi-api-deployment` [logs](#application-logs) for registry related failures like `failed to upload image`.
1. Determine the cause of the upload failure like connection refused, credentials failed, certificate untrusted, etc.
1. Check the image registry settings provided to the Application Service Adapter installation related to the failure and reinstall Application Service Adapter with the correct image registry settings.

### Pushing an app fails to start
#### Symptom
When I run `cf push`, my app stages correctly, but fails to start or become healthy.
#### Possible Causes
1. There is an issue with the application's code.
1. The Kubernetes cluster cannot schedule the application.
1. The Application Service Adapter cannot turn the app into a StatefulSet.
#### Troubleshooting Steps/Potential Solutions
1. To debug the application's code, check the [application logs](#cfapp-logs)
1. To debug applications that fail to be scheduled, check the [Kubernetes System Logs](#system-events) and the [StatefulSet](#system-object-types) status in the CFSpace namespace.
1. Walk through the list of [run app object types](#system-object-types) and check if any objects are missing. For any missing objects, check the logs of the [TAP](#tap-logs) or [Application Service Adapter](#application-logs) component listed in the "Created By" column to see if there are any error messages.
1. If the component listed in the "Created By" column does not have any helpful error messages, identify the previous object in the run app chain and check the logs of the component listed in the "Reconciled By" column to see if there are any error messages.

### Deployed apps fail to become routable
#### Symptom
When I run `cf push`, my app stages and runs, but fails to be routable.
#### Possible Causes
1. There is an issue with the Application Service Adapter Deployments
1. There is an issue with the cluster's routing.
#### Troubleshooting Steps/Potential Solutions
1. Check the [logs](#application-logs) for the `korifi-controllers-controller-manager` and see if there is any additional information.
1. Check the status of the [CFRoute](#system-object-types) corresponding to the app's route in the CFSpace namespace and see if there are any error messages.
2. Check the status of the [HTTPProxy](#system-object-types) corresponding to the app's route in the CFSpace namespace and see if there are any error messages.

## <a id="openshift-failure-scenarios"></a>OpenShift Failure Scenarios

This section contains common failure scenarios specific to operation on the OpenShift platform and describes the appropriate troubleshooting techniques that can be used to gather further information and solve the issue.

### OpenShift Setting
The OpenShift installation setting for Application Service Adapter can be set in the `tas-adapter-values.yml` as described [here](install.md).

### Installing the Application Service Adapter on a non-OpenShift Kubernetes distribution fails
#### Symptom
When I install the Application Service Adapter on a non-OpenShift cluster, I get the following error message:
```bash
kapp: Error: Expected to find kind 'security.openshift.io/v1/SecurityContextConstraints', but did not:
- Kubernetes API server did not have matching apiVersion + kind
- No matching CRD was found in given configuration
```
#### Possible Causes
The OpenShift setting is enabled.
#### Troubleshooting Steps/Potential Solutions
Creating the SecurityContextConstraint will fail on a non-OpenShift cluster because the SecurityContextConstraint CRD is only present on OpenShift. Reinstall Application Service Adapter with the OpenShift setting turned off.

### Installing the Application Service Adapter on an OpenShift Kubernetes distribution fails
#### Symptom
When I install the Application Service Adapter on an OpenShift cluster, I get the following error message:
```bash
1:41:32PM:  ^ Retryable error: Creating resource cfdomain/apps.openshift-aro.k8s-dev.relint.rocks (korifi.cloudfoundry.org/v1alpha1) namespace: cf: API server says: Internal error occurred: failed calling webhook "vcfdomain.korifi.cloudfoundry.org": failed to call webhook: Post "https://korifi-controllers-webhook-service.tas-adapter-system.svc:443/validate-korifi-cloudfoundry-org-v1alpha1-cfdomain?timeout=10s": no endpoints available for service "korifi-controllers-webhook-service" (reason: InternalError)
```
#### Possible Causes
The OpenShift setting is not enabled.
#### Troubleshooting Steps/Potential Solutions
Without the SecurityContextConstraint/ClusterRole/ClusterRoleBinding/RoleBinding, the Application Service Adapter deployments will not be able to deploy Pods. 
1. Query the [Application Service Adapter Deployments](#system-object-types) and check if their `Ready` counts are listed as `0/0`. 
1. Reinstall Application Service Adapter with the OpenShift setting enabled.

### Pushing an app on an OpenShift cluster fails to start
#### Symptom
When I run `cf push` on an OpenShift cluster, my app stages correctly, but fails to start or become healthy.
#### Possible Causes
The ServiceAccount being used by the StatefulSets does not have permission to create pods.
#### Troubleshooting Steps/Potential Solutions
1. Query the [StatefulSet](#system-object-types) and check if their `Ready` counts are listed as `0/0`.
1. Verify that the `ServiceAccount` field is set to `korifi-app`.
1. Verify that there is a RoleBinding in the CFSpace namespace that links `system:tas-adapter:scc:tas-adapter-scc` to `korifi-app`.
1. If any of those objects are not present, reinstall Application Service Adapter with the OpenShift setting enabled.

## <a id="cartographer-failure-scenarios"></a>Cartographer Failure Scenarios

This section contains common failure scenarios specific to the use of the experimental optional Cartographer feature and describes the appropriate troubleshooting techniques that can be used to gather further information and solve the issue. 

### Cartographer Setting
The experimental Cartographer installation setting for the Application Service Adapter can be set in the `tas-adapter-values.yml` as described [here](install.md)

### Pushing an app fails to start
#### Symptom
When I run `cf push`, my app stages correctly, but fails to start or become healthy.
#### Possible Causes
There is a failure in the `cartographer-builder-runner-controller-manager`, the SupplyChain, or the `cartographer-ctrl`.
#### Troubleshooting Steps/Potential Solutions
1. Check the status of the [Workload](#system-object-types) and see if there are any error messages.
1. Check the status of the [BuildWorkload](#system-object-types) and see if there are any error messages.
1. Check the status of the [AppWorkload](#system-object-types) and see if there are any error messages.
1. Check the [logs](#application-logs) for the `cartographer-builder-runner-controller-manager` and see if there is any additional information.
1. Verify that there is a cluster-scoped [SupplyChain](#system-object-types) present.
1. Check the [logs](#tap-logs) for the `cartographer-ctrl` and see if there is any additional information.
