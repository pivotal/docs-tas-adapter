# Release Notes

This topic contains release notes for the Application Service Adapter for VMware Tanzu Application Platform.

## <a id='0-3-0'></a> v0.3.0 Beta Release

**Release Date**: MMM DD, 2022

This release is intended for testing and evaluation only. It should not be used to run production workloads.

### Features


### Components

This release contains the following components:

* CF K8s API @ [2033067](https://github.com/cloudfoundry/cf-k8s-controllers/commit/203306701a4825584a514138bcd8cf73c877d7e4)
* CF K8s Controller @ [2033067](https://github.com/cloudfoundry/cf-k8s-controllers/commit/203306701a4825584a514138bcd8cf73c877d7e4)
* Eirini Controller @ [31dd250](https://github.com/cloudfoundry-incubator/eirini-controller/commit/31dd250190f7fef2e9ee01c7793e68d31c2f08c5)
* Hierarchical Namespaces Controller @ [v0.9.0](https://github.com/kubernetes-sigs/hierarchical-namespaces/releases/tag/v0.9.0)
* kube-rbac-proxy @ [v0.11.0](https://github.com/brancz/kube-rbac-proxy/releases/tag/v0.11.0)


### Known Issues



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
* An application containing a capital letter in its name will not receive HTTP traffic on its default route because the fully qualified domain name for that route is invalid.
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
