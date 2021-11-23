# Release Notes

This topic contains release notes for the Application Service Adapter for VMware Tanzu Application Platform. Refer to [Supported CF CLI](supported-cf-cli.md) for more details.

## <a id='0-1-0'></a> v0.1.0 Beta Release

**Release Date**: TBD
This release is intended for core CF function testing. It should not be used for running production workloads.

### Features
This release includes these features:

* An application developer can deploy a simple application using `cf push` to a Kubernetes cluster.

### Components

This release packages the following components:

* CF K8s API
* CF K8s Controller
* Erini Controller
* Hierarchical Namespaces Controller

### Known issues
This release has the following issues:  
* API Authentication is not supported yet.