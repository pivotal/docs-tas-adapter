# Disaster Recovery

This document provides an overview of the options and considerations for disaster recovery. It includes the following sections:

* [State Storage](#state-storage)
* [Backup and Restore](#backing-up-cluster-state)

Operators have a range of approaches for ensuring they can recover their Application Service Adapter deployment, apps, and data in case of a disaster. These approaches generally fall into two categories:

1. Backing up cluster and container registry state and restoring from backups.
1. Re-creating the data in the deployment by automating the creation of state. This can be achieved through scripted CI automation of the CF CLI and applying declarative Application Service Adapter resources to the cluster.

This document will focus on the first approach by outlying where Application Service Adapter stores state and suggestions for backing it up.

## <a id="state-storage"></a>State Storage

In contrast to TAS for VMs, Application Service Adapter has no dedicated databases or blobstore. Instead, state is stored in two places:

1. As [Kubernetes custom resources](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) in the Kubernetes API. These custom resources end up persisted in the cluster's [etcd](https://etcd.io/) data store.
1. As container images in an OCI compatible container registry (e.g. self-hosted Harbor, DockerHub, or an IaaS provided registry).

To help illustrate this, consider the following scenarios.

### Application Service Adapter Installation Resources

![State storage of Application Service Adapter installation resources](images/tas-adapter-install-state-storage.png)

TAS operators may be familiar with backing up their OpsManager and BOSH Director databases in order to safeguard their installation configuration. For Application Service Adapter this works a bit differently. Application Service Adapter is installed using the `tanzu package install` command. This results in the creation of a variety of [Carvel](https://carvel.dev/) package installation resources and `ConfigMaps` on the cluster which are managed by `kapp-controller`. These installation resources are stored in the Kubernetes API as custom resources which are ultimately stored in etcd.

### Application Service Adapter CF API Resources

![State storage of Application Service Adapter CF resources](images/tas-adapter-cf-resource-state-storage.png)

TAS for VMs stores CF API state and application configuration across a number of databases, with the majority of it held within the Cloud Controller API's "CCDB" database. Application Service Adapter does not maintain its own data store for this purpose, but instead represents all CF resources as Kubernetes custom resources. These custom resources are managed by the Kubernetes API and stored in etcd.

### Application Source Code

![State storage of application source code](images/tas-adapter-app-source-state-storage.png)

Application Service Adapter converts application source code into single-layer OCI images which are stored in the container registry specified at installation.

### Runnable Application Artifacts

![State storage of runnable application images](images/tas-adapter-staged-app-state-storage.png)

TAS for VMs operators may be familiar with the concept of "droplets", or tar-files representing staged apps that are ready to run on the platform. Application Service Adapter (via Tanzu Build Service) produces runnable container images instead of TAS-style droplets which are stored in the container registry specified at installation.

## <a id="backing-up-cluster-state"></a>Backup and Restore

We recommend that operators take frequent backups of both the Kubernetes cluster's etcd and the container registry using open source tools such as [Velero](https://velero.io/) or the native backup functionality provided by their infrastructure platform.
