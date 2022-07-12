# Release notes

This topic contains release notes for the Application Service Adapter for VMware Tanzu Application Platform.

## <a id='0-8-0'></a> v0.8.0 Beta Release

**Release Date**: MMM DD, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

> **Note**: Platform operators should uninstall Application Service Adapter v0.6 or earlier completely before installing Application Service Adapter v0.8. Application Service Adapter v0.8 no longer correctly recognizes orgs, spaces, and apps created in an installation of a previous version.

### Features

* Application developers can obtain a randomly generated route for their app using the `--random-route` flag on `cf push` or the `random-route` key in the application manifest.
* Application processes with a route mapped to them have a port-based health-check by default.
* Platform operators configure the certificate/private key pairs for the Application Service Adapter API and app ingress TLS using Kubernetes secrets instead of certificate and key contents.


### Components

This release contains the following components:

* Korifi @ [30d68c6](https://github.com/cloudfoundry/korifi/commit/30d68c665efea34cd3e2d51ad90284760961bfa5)
* Eirini Controller @ [aeaddba](https://github.com/cloudfoundry-incubator/eirini-controller/commit/aeaddba741fa8193ad3ecdfc2ce1aaf71f8ee6c7)
* kube-rbac-proxy @ [v0.12.0](https://github.com/brancz/kube-rbac-proxy/releases/tag/v0.12.0)


### Known Issues

* The Application Service Adapter supports only a subset of Cloud Foundry command-line interface (cf CLI) commands and options. See [Supported cf CLI Commands](supported-cf-cli-commands.md) for details.
* After unbinding all services from an app, the `VCAP_SERVICES` environment variable for the app will contain the credentials of the last service to be unbound instead of being empty. As a workaround, an application developer can create a user-provided service instance with empty credentials and bind it to the app to clear out credentials from previously bound services.


## <a id='0-7-0'></a> v0.7.0 Beta Release

**Release Date**: June 15, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

> **Note**: Platform operators should uninstall Application Service Adapter v0.6 or earlier completely before installing Application Service Adapter v0.7. Application Service Adapter v0.7 no longer correctly recognizes orgs, spaces, and apps created in an installation of a previous version.


### Features

* Application developers can use `cf logs --recent` to retrieve recent build and runtime logs for applications.
* Application memory allocations match the behavior of Tanzu Application Service, with `M` interpreted consistently as `MiB` (mebibytes). 
* Platform operators can change the default shared domain for application routes.
* Application Service Adapter generates namespaces for CF orgs and spaces entirely via Korifi controllers instead of using the Hierarchical Namespace Controller.
* Application Service Adapter introduces a new `BuildWorkload` resource to represent information required to build an application droplet from a source-code package.
* **FIXED**: Apps that require more than 1024M of memory to start can now set that higher memory allocation with the `-m` flag on `cf push`.

### Components

This release contains the following components:

* Korifi @ [0f9ff9c](https://github.com/cloudfoundry/korifi/commit/0f9ff9cf4212a758e03a84d31a784e9786e5e5ab)
* Eirini Controller @ [b2865ef](https://github.com/cloudfoundry-incubator/eirini-controller/commit/b2865ef7e7be705eb9f814e03b07e400277536eb)
* kube-rbac-proxy @ [v0.12.0](https://github.com/brancz/kube-rbac-proxy/releases/tag/v0.12.0)

### Known Issues

* The Application Service Adapter supports only a subset of Cloud Foundry command-line interface (cf CLI) commands and options. See [Supported cf CLI Commands](supported-cf-cli-commands.md) for details.

## <a id='0-6-1'></a> v0.6.1 Beta Release

**Release Date**: May 16, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

> **Note**: Platform operators should uninstall Application Service Adapter v0.5 or earlier completely before installing Application Service Adapter v0.6. Application Service Adapter v0.6 no longer correctly recognizes orgs, spaces, and apps created in an installation of a previous version.

> **Note**: Application Service Adapter v0.6.0 was not released publicly.


### Features

* Application environment variables are included in the environment variables set during the application build process.
* Application developers can use `cf unmap-route` to unmap a route from an application.
* Platform operators can configure Application Service Adapter to work with Kubernetes authentication proxies such as Pinniped.
* Platform operators must acknowledge disclosure of the VMware Customer Experience Improvement Program policy before installing Application Service Adapter.
* Platform operators can opt out of sending telemetry from the Application Service Adapter installation.
* New `CFOrg` and `CFSpace` custom resources in the Kubernetes API represent orgs and spaces in the Application Service Adapter installation.
* **FIXED**: Running `cf push --no-start` to create a new app will no longer fail with a "Droplet not found. Ensure it exists and you have access to it." error message.
* **FIXED**: Creation of orgs and spaces is now more reliable.

### Components

This release contains the following components:

* Korifi @ [92b8bf8](https://github.com/cloudfoundry/korifi/commit/92b8bf8e078808422baf7de97d41048c1a0076c7)
* Eirini Controller @ [b64c578](https://github.com/cloudfoundry-incubator/eirini-controller/commit/b64c578921b58dc57b2c1a16628be533a01aa1bf)
* Hierarchical Namespaces Controller @ [v1.0.0](https://github.com/kubernetes-sigs/hierarchical-namespaces/releases/tag/v1.0.0)
* kube-rbac-proxy @ [v0.12.0](https://github.com/brancz/kube-rbac-proxy/releases/tag/v0.12.0)

### Known Issues

* The Application Service Adapter supports only a subset of Cloud Foundry command-line interface (cf CLI) commands and options. See [Supported cf CLI Commands](supported-cf-cli-commands.md) for details.
* Apps that require more than 1024M of memory to start, such as some Java apps, must be scaled up to a suitable memory limit with a separate `cf scale` command after `cf push`.


## <a id='0-5-0'></a> v0.5.0 Beta Release

**Release Date**: April 14, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

### Features

* Application developers can use `cf services` to list the user-provided service instances in the current CF space.
* Application developers can use `cf service` to describe a user-provided service instance.
* Application developers can use `cf unbind-service` to unbind a user-provided service instance from an application.
* Application developers can use `cf domains` to list the shared domains available for routes.
* Application developers can use `cf set-health-check` to set the health-check type on an application or an application process type.
* Application developers can use `cf app` to see CPU and memory usage metrics for the instances of an application.
* Service credentials are presented to bound applications as [filesystem projections](https://github.com/servicebinding/spec#workload-projection) under the service binding root directory.
* The Application Service Adapter enforces authorization rules for API resources. A user must have an admin role or a Space Developer role to push apps and map routes.
* The Application Service Adapter validates that domains do not overlap.
* The Application Service Adapter validates that each route has a non-empty hostname and has a fully qualified domain name that is a valid DNS name.
* The Application Service Adapter validates that paths of routes are well-formed and less than 128 characters in length.
* **FIXED**: `cf delete-service` now deletes any bindings of the service to applications in addition to deleting the service instance.
* **FIXED**: An application containing a capital letter in its name now receives HTTP traffic on its default route.


### Components

This release contains the following components:

* CF K8s API @ [8139c3c](https://github.com/cloudfoundry/cf-k8s-controllers/commit/8139c3c1a162a82ce7e3ee59399422670bc2d5e9)
* CF K8s Controller @ [8139c3c](https://github.com/cloudfoundry/cf-k8s-controllers/commit/8139c3c1a162a82ce7e3ee59399422670bc2d5e9)
* Eirini Controller @ [95c7b7c](https://github.com/cloudfoundry-incubator/eirini-controller/commit/95c7b7ce3abec9f24381e71c3034c0573609b95c)
* Hierarchical Namespaces Controller @ [v0.9.0](https://github.com/kubernetes-sigs/hierarchical-namespaces/releases/tag/v0.9.0)
* kube-rbac-proxy @ [v0.11.0](https://github.com/brancz/kube-rbac-proxy/releases/tag/v0.11.0)


### Known Issues

* The Application Service Adapter supports only a subset of Cloud Foundry command-line interface (cf CLI) commands and options. See [Supported cf CLI Commands](supported-cf-cli-commands.md) for details.
* Apps that require more than 1024M of memory to start, such as some Java apps, must be scaled up to a suitable memory limit with a separate `cf scale` command after `cf push`.
* The `cf create-org` command may fail with an error message stating that the user is not authorized to perform the action even when the user is an admin. As a workaround, use `cf orgs` to determine whether the operation succeeded. If it did not, try to create the org again.
* Running `cf push --no-start` to create a new app will fail with a "Droplet not found. Ensure it exists and you have access to it." error message. The app will still be created successfully, though, and it can be staged and started with `cf start`.


## <a id='0-4-0'></a> v0.4.0 Beta Release

**Release Date**: March 8, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

### Features

* Application developers can use `cf create-user-provided-service` to create a user-provided service instance.
* Application developers can use `cf bind-service` to bind a user-provided service instance to an application. After the app is restarted or restaged, the application's `VCAP_SERVICES` environment variable contains the credentials for the bound service.
* Application developers can use `cf delete-service` to delete a user-provided service instance.
* Application developers can use `cf get-health-check` to get the health-check type of an application's process types.


### Components

This release contains the following components:

* CF K8s API @ [b3687b6](https://github.com/cloudfoundry/cf-k8s-controllers/commit/489b41eecfcbc08151254da2fe9c6513300ed1fa)
* CF K8s Controller @ [b3687b6](https://github.com/cloudfoundry/cf-k8s-controllers/commit/489b41eecfcbc08151254da2fe9c6513300ed1fa)
* Eirini Controller @ [d2e8574](https://github.com/cloudfoundry-incubator/eirini-controller/commit/d2e857474b7d614dc233bc2281fce2dd1e868128)
* Hierarchical Namespaces Controller @ [v0.9.0](https://github.com/kubernetes-sigs/hierarchical-namespaces/releases/tag/v0.9.0)
* kube-rbac-proxy @ [v0.11.0](https://github.com/brancz/kube-rbac-proxy/releases/tag/v0.11.0)


### Known Issues

* The Application Service Adapter supports only a subset of Cloud Foundry command-line interface (cf CLI) commands and options. See [Supported cf CLI Commands](supported-cf-cli-commands.md) for details.
* An application containing a capital letter in its name does not receive HTTP traffic on its default route because the fully qualified domain name for that route is invalid.
* Apps that require more than 1024M of memory to start, such as some Java apps, must be scaled up to a suitable memory limit with a separate `cf scale` command after `cf push`.
* The `cf delete-service` command does not delete the service bindings associated to the service. As a workaround, use `kubectl` to delete the corresponding `CFServiceInstance` resources from the Kubernetes API.


## <a id='0-3-0'></a> v0.3.0 Beta Release

**Release Date**: February 8, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

### Features

* Application developers can use `cf delete-route` to delete a route.
* Application developers can use `cf restage` to restage an application.
* Application developers can use `cf buildpacks` to list the system buildpacks.
* Application developers can use `cf env` to list the environment variables on an application.
* Application developers can use `cf set-env` to set an environment variable on an application.
* Application developers can use `cf unset-env` to remove an environment variable on an application.
* Applications now have a default memory limit of 1024M.
* Platform operators must specify container image registry credentials in the installation package values instead of in a Kubernetes Secret after the installation.
* Platform operators can use `cf delete-space` to delete a Cloud Foundry space.
* Platform operators can use `cf delete-org` to delete a Cloud Foundry org.
* **Fixed:** The Application Service Adapter sets the `PORT` environment variable on an application to `8080` by default.
* **Fixed:** The `cf app` and `cf apps` commands no longer fail when an app in the targeted space has a process type with zero desired instances.

### Components

This release contains the following components:

* CF K8s API @ [b3687b6](https://github.com/cloudfoundry/cf-k8s-controllers/commit/b3687b62ce14104890040e51628eef91594b4396)
* CF K8s Controller @ [b3687b6](https://github.com/cloudfoundry/cf-k8s-controllers/commit/b3687b62ce14104890040e51628eef91594b4396)
* Eirini Controller @ [60b8ffd](https://github.com/cloudfoundry-incubator/eirini-controller/commit/60b8ffd2555ec4fdf6299bcf216b8b82565c7f32)
* Hierarchical Namespaces Controller @ [v0.9.0](https://github.com/kubernetes-sigs/hierarchical-namespaces/releases/tag/v0.9.0)
* kube-rbac-proxy @ [v0.11.0](https://github.com/brancz/kube-rbac-proxy/releases/tag/v0.11.0)


### Known Issues

* The Application Service Adapter supports only a subset of Cloud Foundry command-line interface (cf CLI) commands and options. See [Supported cf CLI Commands](supported-cf-cli-commands.md) for details.
* An application containing a capital letter in its name does not receive HTTP traffic on its default route because the fully qualified domain name for that route is invalid.
* Apps that require more than 1024M of memory to start, such as some Java apps, must be scaled up to a suitable memory limit with a separate `cf scale` command after `cf push`.


## <a id='0-2-0'></a> v0.2.0 Beta Release

**Release Date**: January 7, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

### Features

* The Application Service Adapter API checks that requests to protected endpoints have authentication from a user in the underlying Kubernetes cluster.
* App traffic runs over HTTPS instead of HTTP, with the terminating TLS certificate and private key specified at installation time.
* Applications receive a HTTP route on `cf push` unless opting out with the `--no-route` flag.
* Application developers can delete Cloud Foundry applications.
* Platform operators must specify a domain to use as the default shared domain for application routes.
* Platform operators can specify the name of the kpack ClusterBuilder to use to build application images, defaulting to `default`.
* Platform operators can set the replica counts and resource limits and requests for each component of the Application Service Adapter system at installation time.


### Components

This release contains the following components:

* CF K8s API @ [2033067](https://github.com/cloudfoundry/cf-k8s-controllers/commit/203306701a4825584a514138bcd8cf73c877d7e4)
* CF K8s Controller @ [2033067](https://github.com/cloudfoundry/cf-k8s-controllers/commit/203306701a4825584a514138bcd8cf73c877d7e4)
* Eirini Controller @ [31dd250](https://github.com/cloudfoundry-incubator/eirini-controller/commit/31dd250190f7fef2e9ee01c7793e68d31c2f08c5)
* Hierarchical Namespaces Controller @ [v0.9.0](https://github.com/kubernetes-sigs/hierarchical-namespaces/releases/tag/v0.9.0)
* kube-rbac-proxy @ [v0.11.0](https://github.com/brancz/kube-rbac-proxy/releases/tag/v0.11.0)


### Known Issues

* The Application Service Adapter supports only a subset of Cloud Foundry command-line interface (cf CLI) commands and options. See [Supported cf CLI Commands](supported-cf-cli-commands.md) for details.
* Application workloads require their `PORT` environment variable to be set to `8080` in order for ingress to work.
* An application containing a capital letter in its name does not receive HTTP traffic on its default route because the fully qualified domain name for that route is invalid.
* The `cf app` and `cf apps` commands fail when an app in the targeted space has a process type with zero desired instances. As a workaround, scale each such process up to at least one desired instance.
* Apps that require more than 500M of memory to start, such as many Java apps, must be scaled up to a suitable memory limit with a separate `cf scale` command after `cf push`.


## <a id='0-1-0'></a> v0.1.0 Beta Release

**Release Date**: December 2, 2021

This release is intended for testing and evaluation only. It should not be used to run production workloads.

### Features

This release includes these features:

* Application developers can deploy a simple application using `cf push` to a Kubernetes cluster.

### Components

This release packages the following components:

* CF K8s API
* CF K8s Controller
* Eirini Controller
* Hierarchical Namespaces Controller

### Known issues

This release has the following issues:

* API authentication is not supported yet.
* Application workloads require `PORT: 8080` as an environment variable in order for ingress to work.
