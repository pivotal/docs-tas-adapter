# Troubleshoot Application Service Adapter

This topic documents generic techniques for diagnosing the system health as well
as specific steps to be taken in known scenarios.

## <a id="generic-troubleshooting-techniques"></a>Generic troubleshooting techniques

This section contains generic techniques for gathering information about the
status of Application Service Adapter to aid in troubleshooting. VMware
recommends keeping up to date with the latest version of Application Service
Adapter to get the latest bug fixes and stability improvements.

### <a id="application-logs"></a>Application Service Adapter logs

You can query the logs of several Application Service Adapter deployments to
gather information about failures that have occurred in the system.

To fetch recent logs from all components of an Application Service Adapter
installation:

```bash
kapp logs --app tas-adapter.app -n tap-install
```

To fetch recent logs from a specific deployment:

```bash
kapp logs --app tas-adapter.app -n tap-install --pod-name DEPLOYMENT-NAME%
```

Where `DEPLOYMENT-NAME` is the name of the Kubernetes deployment. For example,
`korifi-api-deployment`).

To stream logs instead of fetching the most recent logs, add the `--follow` flag
to the above `kapp logs` commands. For additional details and options, refer to
the `kapp logs --help` help text.

#### Deployments

The following is a brief description of specific Application Service Adapter
deployments and their main responsibilities. This can help you isolate which
logs to query when troubleshooting.

1. The `korifi-api-deployment` deployment is tasked with responding to API
   requests sent to the Application Service Adapter. Logs for failures related
   to the image registry may also show up in this deployment's logs.
2. The `korifi-controllers-controller-manager` deployment is tasked with
   processing commands for Application Service Adapter. This deployment's logs
   is an excellent starting point when debugging failures. Most commands flow
   through here before being processed by more specific components, with the
   exception of API commands that fail before getting to the controller manager.
3. The `tas-adapter-telemetry-informer` deployment is tasked with handling all
   outgoing telemetry.
4. The `cartographer-builder-runner-controller-manager` deployment is tasked
   with creating Cartographer workloads for apps when using the experimental
   Cartographer builder/runner flow.

#### Dynamically changing component logging level

You can [change the logging level](install.md#component-log-level) for each
component of Application Service Adapter during an upgrade, but this results in
the component being restarted. To avoid component restarts, you can temporarily
change the logging level for a specific component by modifying the `logLevel`
setting in the corresponding ConfigMap.

Before you perform any customization to the ConfigMaps, you must prevent the
platform from reverting your changes back to their original install values. To
do this, you must pause the PackageInstall/tas-adapter object by setting the
packageinstall.spec.pause parameter to true. Run:

```bash
kubectl edit -n tap-install packageinstall tas-adapter
```

```yaml
apiVersion: packaging.carvel.dev/v1alpha1
kind: PackageInstall
metadata:
  name: tas-adapter
  namespace: tap-install
spec:
  paused: true # <- add this parameter to the resource definition
  packageRef:
    refName: application-service-adapter.tanzu.vmware.com
    versionSelection:
# ...
```

> **Note** You need to unpause the PackageInstall/tas-adapter object before
> attempting to perform a platform upgrade. If you wish to persist changes to
> the logging level for a given component, you should update your values YAML
> file to match the new logging level.

To set the logging level for a given component dynamically, you can change the
value of the `logLevel` setting in the corresponding ConfigMap. Run:

```bash
kubectl get -n tas-adapter-system configmap | grep COMPONENT-NAME
kubectl edit -n tas-adapter-system configmap CONFIGMAP-NAME
```

Where:
- `COMPONENT-NAME` is the name of the component.
- `CONFIGMAP-NAME` is the name of the ConfigMap for the specified component.

For example, to set the logging level for the `korifi-api` component:

```yaml
kind: ConfigMap
metadata:
  name: korifi-api-config-ver-1
  namespace: tas-adapter-system
apiVersion: v1
data:
  korifi_api_config.yaml: |
    # ...
    logLevel: info # <- Set the desired logging level. This must be one of error, warn, info, or debug.
    # ...
```

> **Note** After changing the logging level for a component, it can take a few
> minutes for the cluster to reconcile the change. You should see a message that
> indicates that the logging level has changed, with the old and new logging
> level displayed.

### <a id="tap-logs"></a>Tanzu Application Platform logs

While not directly part of Application Service Adapter, there are several Tanzu
Application Platform deployments utilized by Application Service Adapter. These
can also provide further debug information on failures.

To fetch recent logs from a given Tanzu Application Platform application, run:

```bash
kapp logs --app APP-NAME -n tap-install
```

Where `APP-NAME` is the name of the Tanzu Application Platform application. For
example, `buildservice.app`).

To stream logs instead of fetching the most recent logs, add the `--follow` flag
to the earlier `kapp logs` command. For additional details and options, see the
`kapp logs --help` help text.

#### Deployments

The following is a brief description of the specific Tanzu Application Platform
deployments used by Application Service Adapter and what their main
responsibilities are to help determine which logs to query when troubleshooting.

1. The `buildservice.app` application is tasked with processing Tanzu Build
   Service (kpack) commands. If failures in the build image process occur in
   Tanzu Build Service itself, see the application logs for further information.
2. The `contour.app` application is tasked with creating an ingress into the
   system. If you fail to connect to Application Service Adapter or to an
   application, see the application logs for further information.
3. The `cartographer.app` application is tasked with processing Cartographer
   ClusterSupplyChains when using the experimental Cartographer builder/runner
   flow. If creation of an image, build, ConfigMap, or StatefulSet fails, see
   the application logs for further information.

### <a id="cfapp-logs"></a>CFApp logs

For CFApps that have been staged, the cf logs command can be used to get more
information in the event of run failures:

```bash
cf logs <CFApp name>
```

### <a id="system-events"></a>Kubernetes system events

Kubernetes system events can provide debug information in cases where system
restrictions prevent objects from being created. For example, these events may
be caused by insufficient resources or evicted pods. To query the logs for the
system, run:

```bash
kubectl get events -A
```

### <a id="system-object-types"></a>System object types

System objects created and used by Application Service Adapter can be useful in
debugging failures. Describing the objects can show error messages in their
status fields. Whether objects are present can also be useful in determining
where in the command process failures have occurred and which logs should be
queried for more information.

For example, if an object in the image build chain is missing, the component in
the "Created By" column can be queried for logs to see whether any useful error
messages are present. If no useful logs are found there, identify the previous
object in the image build chain and check the logs of the component listed in
the "Reconciled By" column to see if there are any error messages.

Instructions for querying logs can be found for [Application Service
Adapter](#application-logs) and [Tanzu Application Platform](#tap-logs).

Some Kubernetes objects exist within a CFOrg or CFSpace namespace. To find the
corresponding namespace of a CFOrg or CFSpace, run:

```bash
cf org --guid <CFOrg name>
cf space --guid <CFSpace name>
```

#### Installed object types
| Kind                      | Component    | Namespace or Scope   | Notes                                                                                                                                                                                                                                                                                                                                                                                |
|---------------------------|--------------|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ClusterBuilder            | kpack        | Cluster              | ClusterBuilders are Tanzu Build Service (kpack) components installed by Tanzu Application Platform that are used to build images. If they are not present, image builds may not complete successfully.                                                                                                                                                                                                      |
| Deployment                | kubernetes   | `tas-adapter-system` | All of the Application Service Adapter deployment's `Ready` counts should be listed as `1/1` except for `korifi-api-deployment` that should be `2/2`. If this is not the case, the system is not functioning correctly. To find the cause, analyze the status/events fields of the inoperable deployment. This issue can sometimes be related to RBAC.                               |
| ClusterRole               | kubernetes   | Cluster              | ClusterRoles are created to manage system privileges at the cluster scope.                                                                                                                                                                                                                                                                                                           |
| ClusterRoleBinding        | kubernetes   | Cluster              | ClusterRoleBindings are created to manage system privileges at the cluster scope.                                                                                                                                                                                                                                                                                                    |
| ServiceAccount            | kubernetes   | `cf`                 | ServiceAccounts are created to manage Cloud Foundry permissions. Some ServiceAccounts are designed to be propagated to CFOrg/CFSpace namespaces. These objects are designated with the `cloudfoundry.org/propagate-service-account: "true"` annotation.                                                                                                                                     |
| RoleBinding               | kubernetes   | `cf`                 | RoleBindings are created to manage Cloud Foundry permissions. Some RoleBindings are designed to be propagated to CFOrg/CFSpace namespaces. These objects are designated with the `cloudfoundry.org/propagate-cf-role: "true"` annotation.                                                                                                                                                   |
| SecurityContextConstraint | openshift    | Cluster              | SecurityContextConstraints are specific to OpenShift and are used to grant required permissions to Application Service Adapter components in that environment. These CRDs do not exist in non-OpenShift environments. An associated ClusterRole, ClusterRoleBinding, and RoleBinding is also installed if the `kubernetes_distribution` shared setting is set to `openshift`.   |
| ClusterSupplyChain        | Cartographer | Cluster              | When using the experimental Cartographer builder/runner flow, a ClusterSupplyChain is created during Application Service Adapter installation at the cluster scope. The `cartographer-ctrl` uses this to creates the image, build, ConfigMap, and StatefulSets in the builder/runner flow.                                                                                   |

#### CFOrg/CFSpace object types
| Kind            | Component | Namespace or Scope | Created By                   | Reconciled By                                      | Notes                                                                                                                                                                    |
|-----------------|-----------|--------------------|------------------------------|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFOrg           | Cloud Foundry        | `cf`               | `korifi-api-deployment`      | `korifi-controllers-controller-manager`            | CFOrg resources are created by the `cf create-org` command. A corresponding namespace should also be created by the `korifi-controllers-controller-manager`.             |
| CFSpace         | Cloud Foundry        | CFOrg Namespace    | `korifi-api-deployment`      | `korifi-controllers-controller-manager`            | CFSpace resources are created by the `cf create-space` command. A corresponding namespace should also be created by the `korifi-controllers-controller-manager`.         |

#### Image build object types (default builder/runner)
All image build object types are located within the targeted CFSpace Namespace.

| Kind                        | Component | Created By                              | Reconciled By                                | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|-----------------------------|-----------|-----------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFApp                       | Cloud Foundry        | `korifi-api-deployment`                 | `korifi-controllers-controller-manager`      | The 1st object in the image build chain that's created when the `cf push` command is run. The CFApp resource represents the application.                                                                                                                                                                                                                                                                                                 |
| CFPackage                   | Cloud Foundry        | `korifi-api-deployment`                 | `korifi-controllers-controller-manager`      | The 2nd object in the image build chain that's created when the `cf push` command is run. The CFPackage resource contains a reference to the uploaded app source code.                                                                                                                                                                                                                                                                   |
| CFBuild                     | Cloud Foundry        | `korifi-api-deployment`                 | `korifi-controllers-controller-manager`      | The 3rd object in the image build chain that's created when the `cf push` command is run. The CFBuild resource initiates the build and eventually contains a reference to the runnable app image.                                                                                                                                                                                                                                        |
| BuildWorkload               | Cloud Foundry        | `korifi-controllers-controller-manager` | `korifi-controllers-controller-manager`      | The 4th object in the image build chain that's created when the `cf push` command is run. The BuildWorkload is an intermediate resource that contains information about the build.                                                                                                                                                                                                                                                       |
| Image                       | kpack     | `korifi-controllers-controller-manager` | `buildservice-ctrl`                          | The 5th object in the image build chain that's created when the `cf push` command is run. The full object path for image objects are kpack.io/v1alpha2/images. This may need to be specified when querying in certain environments like OpenShift since there are other resources with the same name. The image is a kpack resource that provides configuration to build and maintains a docker image utilizing Cloud Native Buildpacks.  |
| Build                       | kpack     | `buildservice-ctrl`                     | NA                                           | The 6th object in the image build chain that's created when the `cf push` command is run. The full object path for build objects are kpack.io/v1alpha2/builds. This may need to be specified when querying in certain environments like OpenShift since there are other resources with the same name. The build is a kpack resource that schedules and runs a single Cloud Native Buildpacks build. The build object spawns a build pod. |

#### Run app object types (default builder/runner)
All run app object types are located within the targeted CFSpace namespace.

| Kind         | Component  | Created By                                     | Reconciled By                                  | Notes                                                                                                                                                                                                                                 |
|--------------|------------|------------------------------------------------|------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFProcess    | Cloud Foundry         | `korifi-api-deployment`                        | `korifi-controllers-controller-manager`        | The 1st object in the app run chain that's created when the `cf push` command is run. The CFProcess resource represents a single process.                                                                                             |
| AppWorkload  | Cloud Foundry         | `korifi-controllers-controller-manager`        | `korifi-controllers-controller-manager` | The 2nd object in the app run chain that's created when the `cf push` command is run. The AppWorkload is an intermediate resource that contains information about the application/process.                                            |
| StatefulSet  | Kubernetes | `korifi-controllers-controller-manager` | NA                                             | The 3rd object in the app run chain that's created when the `cf push` command is run. The StatefulSet is a Kubernetes resource that represents a single process for an application. There must be a StatefulSet for each CFProcess. |
| ReplicaSet   | Kubernetes | Kubernetes                                     | NA                                             | The 4th object in the app run chain that's created when the `cf push` command is run. The ReplicaSet is created by Kubernetes to maintain a stable set of replica pods for the StatefulSet.                                           |
| Pod          | Kubernetes | Kubernetes                                     | NA                                             | The app process pods are last in the app run chain and are created by Kubernetes based on the ReplicaSet. The number of pods are based on the ReplicaSet replicas parameter.                                                              |
| CFRoute      | Cloud Foundry         | `korifi-api-deployment`                        | `korifi-controllers-controller-manager`        | CFRoutes are created to reach running apps. They can be created as part of the `cf push` command if a default or random route is specified, defined in a manifest, or as part of the `cf create-route` command.            |
| HTTPProxy    | contour    | `korifi-controllers-controller-manager`        | NA                                             | The HTTPProxy is a Contour resource created by the `korifi-controllers-controller-manager` to reach running apps.                                                                                                             |

#### Image build object types (Cartographer builder/runner)
All image build object types are located within the targeted CFSpace namespace.

| Kind             | Component     | Created By                                       | Reconciled By                                    | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|------------------|---------------|--------------------------------------------------|--------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFApp            | Cloud Foundry            | `korifi-api-deployment`                          | `korifi-controllers-controller-manager`          | The 1st object in the image build chain that's created when the `cf push` command is run.  The CFApp resource represents the application.                                                                                                                                                                                                                                                                                                |
| CFPackage        | Cloud Foundry            | `korifi-api-deployment`                          | `korifi-controllers-controller-manager`          | The 2nd object in the image build chain that's created when the `cf push` command is run. The CFPackage resource contains a reference to the uploaded app source code.                                                                                                                                                                                                                                                                   |
| CFBuild          | Cloud Foundry            | `korifi-api-deployment`                          | `korifi-controllers-controller-manager`          | The 3rd object in the image build chain that's created when the `cf push` command is run. The CFBuild resource initiates the build and eventually contains a reference to the runnable app image.                                                                                                                                                                                                                                        |
| BuildWorkload    | Cloud Foundry            | `korifi-controllers-controller-manager`          | `cartographer-builder-runner-controller-manager` | The 4th object in the image build chain that's created when the `cf push` command is run. The BuildWorkload is an intermediate resource that contains information about the build.                                                                                                                                                                                                                                                       |
| Workload         | Cartographer  | `cartographer-builder-runner-controller-manager` | `cartographer-ctrl`                              | The 5th object in the image build chain that's created when the `cf push` command is run. The workload is a Cartographer resource that contains information used by the `buildservice-ctrl` in conjunction with the SupplyChain to create an image and later a ConfigMap containing the StatefulSet definition.                                                                                                                          |
| Image            | kpack         | `cartographer-ctrl`                              | `buildservice-ctrl`                              | The 6th object in the image build chain that's created when the `cf push` command is run. The full object path for image objects are kpack.io/v1alpha2/images. This may need to be specified when querying in certain environments like OpenShift since there are other resources with the same name. The image is a kpack resource that provides configuration to build and maintain a docker image utilizing Cloud Native Buildpacks.  |
| Build            | kpack         | `buildservice-ctrl`                              | NA                                               | The 7th object in the image build chain that's created when the `cf push` command is run. The full object path for build objects are kpack.io/v1alpha2/builds. This may need to be specified when querying in certain environments like OpenShift since there are other resources with the same name. The build is a kpack resource that schedules and runs a single Cloud Native Buildpacks build. The build object spawns a build pod. |

#### Run app object types (Cartographer builder/runner)
All run app object types are located within the targeted CFSpace namespace.

| Kind        | Component    | Created By                              | Reconciled By                                    | Notes                                                                                                                                                                                                                                                                                                                                                          |
|-------------|--------------|-----------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFProcess   | Cloud Foundry           | `korifi-api-deployment`                 | `korifi-controllers-controller-manager`          | The 1st object in the app run chain that's created when the `cf push` command is run. The CFProcess resource represents a single process.                                                                                                                                                                                                                      |
| AppWorkload | Cloud Foundry           | `korifi-controllers-controller-manager` | `cartographer-builder-runner-controller-manager` | The 2nd object in the app run chain that's created when the `cf push` command is run.  The AppWorkload is an intermediate resource that contains information about the application/process. `cartographer-builder-runner-controller-manager` edits the Cartographer workload created during the image build process with the information from the AppWorkload. |
| PodIntent   | Cartographer | App Operator                            | `conventions-controller-manager`                 | An optional object in the app run chain. Convention Service enables app operators to consistently apply desired runtime configurations to fleets of workloads.                                                                                                                                                                                                 |
| ConfigMap   | Kubernetes   | `cartographer-ctrl`                     | NA                                               | The 3rd object in the app run chain that's created when the `cf push` command is run. A Kubernetes ConfigMap resource containing the StatefulSet definition is created that `cartographer-ctrl` uses to spawns a StatefulSet by means of kapp.                                                                                                                 |
| StatefulSet | Kubernetes   | `cartographer-ctrl`                     | NA                                               | The 4th object in the app run chain that's created when the `cf push` command is run. The StatefulSet is a Kubernetes resource that represents a single process for an application. There should be a StatefulSet for each CFProcess.                                                                                                                          |
| ReplicaSet  | Kubernetes   | Kubernetes                              | NA                                               | The 5th object in the app run chain that's created when the `cf push` command is run. The ReplicaSet is created by Kubernetes to maintain a stable set of replica pods for the StatefulSet.                                                                                                                                                                    |
| Pod         | Kubernetes   | Kubernetes                              | NA                                               | The app process pods are last in the app run chain and created by Kubernetes based on the ReplicaSet. The number of pods are based on the ReplicaSet replicas parameter.                                                                                                                                                                                       |
| CFRoute     | Cloud Foundry           | `korifi-api-deployment`                 | `korifi-controllers-controller-manager`          | CFRoutes are created to be able to reach running apps. They can be created as part of the `cf push` command if a default or random route is specified, defined in a manifest, or as part of the `cf create-route` command.                                                                                                                                     |
| HTTPProxy   | contour      | `korifi-controllers-controller-manager` | NA                                               | The HTTPProxy is a Contour resource that is created by the `korifi-controllers-controller-manager` to reach running apps.                                                                                                                                                                                                                                      |

#### Run task object types
All run task object types are located within the targeted CFSpace namespace.

| Kind         | Component  | Created By                                  | Reconciled By                                | Notes                                                                                                                                                |
|--------------|------------|---------------------------------------------|----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| CFTask       | Cloud Foundry         | `korifi-api-deployment`                     | `korifi-controllers-controller-manager`      | The 1st object in the run task chain that's created when the `cf run-task` command is run.                                                           |
| TaskWorkload | Cloud Foundry         | `korifi-controllers-controller-manager`     | `korifi-controllers-controller-manager`  | The 2nd object in the run task chain that's created when the `cf run-task` command is run.                                                           |
| Job          | Kubernetes | `korifi-controllers-controller-manager` | NA                                           | The 3rd object in the run task chain that's created when the `cf run-task` command is run.                                                           |
| Pod          | Kubernetes | Kubernetes                                  | NA                                           | The running job pods are last in the run task chain and created by Kubernetes based on the job. The job pod completes when its command is run.   |

## <a id="commmon-failure-scenarios"></a>Common failure scenarios

This section contains common failure scenarios and describes the appropriate
troubleshooting techniques you can use to gather further information and solve
the issue.

### Installation failed to finish successfully

#### Symptom

When I install Application Service Adapter, the following error message is
returned:

```bash
Finished unsuccessfully (Encountered failure condition ReconcileFailed == True:  (message: No matching export/secret))
```

#### Possible causes

1. The `Secret` for the application image registry does not exist.
2. The `SecretExport` to copy the above secret to the `cf` namespace does not
   exist.

#### Troubleshooting steps and potential solutions

1. Verify that both the `Secret` and `SecretExport` exist by following steps 3
   and 4 in [Configure the installation
   settings](install.md#configure-installation-settings).

### Organization not found when creating an org

#### Symptom

When I run `cf create org my-org`, the following error message is returned:

```bash
Organization 'my-org' not found
```

#### Possible causes

The user creating the CFOrg is not bound to the `korifi-controllers-admin`
ClusterRole.

#### Troubleshooting steps and potential solutions

1. Verify the user in the `cf create org` command output.

```bash
Creating org my-org as cf-admin...
```

1. Verify that there is a RoleBinding in the `cf` namespace binding the
   `korifi-controllers-admin` ClusterRole to the given user and that it has the
   `cloudfoundry.org/propagate-cf-role: "true"` annotation.
2. If that RoleBinding does not exist, either switch to a user that is bound to
   the `korifi-controllers-admin` ClusterRole or create a RoleBinding as a
   cluster admin.

### Dev user unauthorized to target an org or space

#### Symptom

When I first log in `cf login` and/or when I run `cf target -o my-org -s
my-space`, the following error message is returned:

```bash
No org or space targeted, use 'cf target -o ORG -s SPACE'
You are not authorized to perform the requested action
FAILED
```

#### Possible causes

The user targeting the org or space is not bound to the following ClusterRoles:

- `korifi-controllers-root-namespace-user` in the root namespace (from the
  installation values file)
- `korifi-controllers-organization-user` in the cf-org namespace
  `cf-org-<ORG_GUID>`
- `korifi-controllers-space-developer` in the cf-space namespace
  `cf-space-<SPACE_GUID>`

#### Troubleshooting steps and potential solutions

1. Fetch the name of the user in the `cf curl /whoami` command output.
1. Check RoleBindings in each of the namespaces with the name of that user as a
   subject.
1. If the rolebindings do not exist, create them with cf set-role commands.

### Pushing an app fails to build an image

#### Symptom

When I run `cf push`, the Cloud Foundry CLI does not respond with a built image.

#### Possible causes

1. ClusterBuilder is not ready.
1. A failure occurred in one of the Tanzu Application Platform or Application
   Service Adapter components.

#### Troubleshooting steps and potential solutions

1. Check the status of the ClusterBuilders by running `kubectl get
   clusterbuilder`. The `Ready` column should show `True`.
2. If the ClusterBuilder's `Ready` state is not `True`, run `kubectl describe
   clusterbuilder` and check the status section for more information.
3. Walk through the list of [image build object types](#system-object-types) and
   check if any objects are missing. For any missing objects, check the logs of
   the [Tanzu Application Platform](#tap-logs) or [Application Service
   Adapter](#application-logs) component listed in the **Created By** column to
   see if there are any error messages.
4. If the component listed in the **Created By** column does not have any
   helpful error messages, identify the previous object in the image build chain
   and check the logs of the component listed in the **Reconciled By** column to
   see if there are any error messages.

### Pushing an app fails to upload an image to the image registry

#### Symptom

When I run `cf push`, the following output/error message is returned:

```bash
Unexpected Response
Response Code: 500
Code: 0, Title: , Detail: {"errors":[{"detail":"An unknown error occurred.","title":"UnknownError","code":10001}]}
```

#### Possible causes
The image registry is unavailable/unauthorized.

#### Troubleshooting steps and potential solutions

1. Check the `korifi-api-deployment` [logs](#application-logs) for
   registry-related failures like `failed to upload image`.
1. Determine the cause of the upload failure like connection refused,
   credentials failed, certificate untrusted, and so on.
1. Check the image registry settings provided to the Application Service Adapter
   installation related to the failure and reinstall Application Service Adapter
   with the correct image registry settings.

### Pushing an app fails to start

#### Symptom

When I run `cf push`, my app stages correctly, but fails to start or become
healthy.

#### Possible causes

1. There is an issue with the application's code.
1. The Kubernetes cluster cannot schedule the application.
1. Application Service Adapter cannot turn the app into a StatefulSet.

#### Troubleshooting steps and potential solutions

1. To debug the application's code, check the [application logs](#cfapp-logs).
1. To debug applications that fail to be scheduled, check the [Kubernetes System
   Logs](#system-events) and the [StatefulSet](#system-object-types) status in
   the CFSpace namespace.
1. Walk through the list of [run app object types](#system-object-types) and
   check if any objects are missing. For any missing objects, check the logs of
   the [Tanzu Application Platform](#tap-logs) or [Application Service
   Adapter](#application-logs) component listed in the **Created By** column to
   see if there are any error messages.
1. If the component listed in the **Created By** column does not have any
   helpful error messages, identify the previous object in the run app chain and
   check the logs of the component listed in the **Reconciled By** column to see
   if there are any error messages.

### Deployed apps fail to become routable

#### Symptom

When I run `cf push`, my app stages and runs, but fails to be routable.

#### Possible causes

1. There is an issue with the Application Service Adapter Deployments.
1. There is an issue with the cluster's routing.

#### Troubleshooting steps and potential solutions

1. Check the [logs](#application-logs) for the
   `korifi-controllers-controller-manager` to see if there is any additional
   information.
2. Check the status of the [CFRoute](#system-object-types) corresponding to the
   app's route in the CFSpace namespace to see if there are any error messages.
3. Check the status of the [HTTPProxy](#system-object-types) corresponding to
   the app's route in the CFSpace namespace to see if there are any error
   messages.

## <a id="openshift-failure-scenarios"></a>OpenShift failure scenarios

This section contains common failure scenarios specific to operation on the
OpenShift platform and describes the appropriate troubleshooting techniques that
can be used to gather further information and solve the issue.

### OpenShift setting

The OpenShift installation setting for Application Service Adapter can be set in
the `tas-adapter-values.yml` as described in [Install Application Service
Adapter](install.md).

### Installing Application Service Adapter on a non-OpenShift Kubernetes distribution fails

#### Symptom

When I install Application Service Adapter on a non-OpenShift cluster, I get the
following error message:

```bash
kapp: Error: Expected to find kind 'security.openshift.io/v1/SecurityContextConstraints', but did not:
- Kubernetes API server did not have matching apiVersion + kind
- No matching CRD was found in given configuration
```

#### Possible causes

The OpenShift setting is enabled.

#### Troubleshooting steps and potential solutions

Creating the SecurityContextConstraint faila on a non-OpenShift cluster because
the SecurityContextConstraint CRD is only present on OpenShift. Reinstall
Application Service Adapter with the OpenShift setting turned off.

### Installing Application Service Adapter on an OpenShift Kubernetes distribution fails

#### Symptom

When I install the Application Service Adapter on an OpenShift cluster, I get
the following error message:

```bash
1:41:32PM:  ^ Retryable error: Creating resource cfdomain/apps.openshift-aro.k8s-dev.relint.rocks (korifi.cloudfoundry.org/v1alpha1) namespace: cf: API server says: Internal error occurred: failed calling webhook "vcfdomain.korifi.cloudfoundry.org": failed to call webhook: Post "https://korifi-controllers-webhook-service.tas-adapter-system.svc:443/validate-korifi-cloudfoundry-org-v1alpha1-cfdomain?timeout=10s": no endpoints available for service "korifi-controllers-webhook-service" (reason: InternalError)
```

#### Possible causes

The OpenShift setting is not enabled.

#### Troubleshooting steps and potential solutions

Without the
SecurityContextConstraint/ClusterRole/ClusterRoleBinding/RoleBinding, the
Application Service Adapter deployments are not able to deploy pods.

1. Query the [Application Service Adapter Deployments](#system-object-types) and
   check if their `Ready` counts are listed as `0/0`.
2. Reinstall Application Service Adapter with the OpenShift setting enabled.

### Pushing an app on an OpenShift cluster fails to start

#### Symptom

When I run `cf push` on an OpenShift cluster, my app stages correctly, but fails
to start or become healthy.

#### Possible causes

The ServiceAccount being used by the StatefulSets does not have permission to
create pods.

#### Troubleshooting steps and potential solutions

1. Query the [StatefulSet](#system-object-types) to check if their `Ready`
   counts are listed as `0/0`.
1. Verify that the `ServiceAccount` field is set to `korifi-app`.
1. Verify that there is a RoleBinding in the CFSpace namespace that links
   `system:tas-adapter:scc:tas-adapter-scc` to `korifi-app`.
1. If any of those objects are not present, reinstall Application Service
   Adapter with the OpenShift setting enabled.

## <a id="cartographer-failure-scenarios"></a>Cartographer failure scenarios

This section contains common failure scenarios specific to the use of the
experimental optional Cartographer feature and describes the appropriate
troubleshooting techniques that can be used to gather further information and
solve the issue.

### Cartographer setting

The experimental Cartographer installation setting for Application Service
Adapter can be set in the `tas-adapter-values.yml` as described [Install
Application Service Adapter](install.md)

### Pushing an app fails to start

#### Symptom

When I run `cf push`, my app stages correctly, but fails to start or become
healthy.

#### Possible causes

There is a failure in the `cartographer-builder-runner-controller-manager`, the
SupplyChain, or the `cartographer-ctrl`.

#### Troubleshooting steps and potential solutions

1. Check the status of the [Workload](#system-object-types) to see if there are
   any error messages.
1. Check the status of the [BuildWorkload](#system-object-types) to see if there
   are any error messages.
1. Check the status of the [AppWorkload](#system-object-types) to see if there
   are any error messages.
1. Check the [logs](#application-logs) for the
   `cartographer-builder-runner-controller-manager` to see if there is any
   additional information.
1. Verify that there is a cluster-scoped [SupplyChain](#system-object-types)
   present.
1. Check the [logs](#tap-logs) for the `cartographer-ctrl` to see if there is
   any additional information.
