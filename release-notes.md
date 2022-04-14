# Release notes

This topic contains release notes for the Application Service Adapter for VMware Tanzu Application Platform.

## <a id='0-5-0'></a> v0.5.0 Beta Release

**Release Date**: MMM DD, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

### Features

* The Application Service Adapter enforces authorization rules for API resources. A user must have an admin role or a Space Developer role to push apps and map routes.
* Application developers can use `cf services` to list the user-provided service instances in the current CF space.
* Application developers can use `cf service` to describe a user-provided service instance.
* Application developers can use `cf unbind-service` to unbind a user-provided service instance from an application.
* Application developers can use `cf domains` to list the shared domains available for routes.
* Application developers can use `cf set-health-check` to set the health-check type on an application or an application process type.
* Service credentials are presented to bound applications as [filesystem projections](https://github.com/servicebinding/spec#workload-projection) under the service binding root directory.
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

**Release Date**: March 08, 2022

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
