# Release Notes

This topic contains release notes for the Application Service Adapter for VMware Tanzu Application Platform.

## <a id='0-2-0'></a> v0.2.0 Beta Release
This release is intended for core CF function testing. It should not be used for running production workloads.

### Features
* Platform Operator can create role-binding for users;
* Developer can target and authenticate into a space with proper authentication


### Components


### Known Issues





## <a id='0-1-0'></a> v0.1.0 Beta Release

**Release Date**: Dec 2, 2021

This release is intended for core CF function testing. It should not be used for running production workloads.

### Features
This release includes these features:

* An application developer can deploy a simple application using `cf push` to a Kubernetes cluster.

### Components

This release packages the following components:

* CF K8s API
* CF K8s Controller
* Eirini Controller
* Hierarchical Namespaces Controller

### Known issues
This release has the following issues: 

* API Authentication is not supported yet.
* Application workloads require `PORT: 8080` as an environment variable in order for ingress to work.
* Kubernetes 1.22 doesn't work with Tanzu Build Service/kpack. For now avoid v1.22.* until a fix is released.