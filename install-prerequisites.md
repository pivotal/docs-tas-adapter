# Installing Prerequisites

This document describes the first part of the installation process for the Application Service Adapter for VMware Tanzu Application Platform.

----

**NOTE**: v0.1 is a limited beta release, and is only available to invited users. If you are interested in testing this version, please reach out to your account team. Once you are invited to participate in the beta testing, download the [Application Service Adapter](https://network.tanzu.vmware.com/products/app-service-adapter/) from Tanzu Network after accepting the EULA.

This beta version requires an experimental version of the [CF CLI](https://github.com/cloudfoundry/cli). You will receive instructions to obtain the CF CLI binary as part of the onboarding process for the limited beta.

To install the Application Service Adapter, you need:

* Administrator access to a Kubernetes cluster, as [specified here](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#kubernetes-cluster-requirements-2).
   * **NOTE**: There is a known issue with Kubernetes 1.22 and TBS/kpack, for now avoid v1.22.* until a fix is released. [Here](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/1.3/vmware-tanzu-kubernetes-grid-13/GUID-tanzu-k8s-clusters-k8s-versions.html) are instructions to deploy a TCE Workload cluster with a different Kuberentes version.

* A container image registry, as [specified here](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#tanzu-network-and-container-image-registry-requirements-1).

The following tools must be installed in the workstation environment in which you intend to perform the installation:

* The Kubernetes CLI (`kubectl`), v1.19, v1.20, or v1.21.

* The Tanzu CLI and its plugins, with [installation instructions here](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#install-or-update-the-tanzu-cli-and-plugins-5).


The following dependencies must be installed to the target Kubernetes cluster before installing the Application Service Adapter. Note that if you have completed an [installation of Tanzu Application Platform](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-intro.html) in your target Kubernetes cluster, then these dependencies will all be present.

* Carvel kapp-controller v0.29.0 or later, with [installation instructions here](https://carvel.dev/kapp-controller/docs/latest/install/).

* Carvel secretgen-controller v0.6.0 or later, with [installation instructions here](https://github.com/vmware-tanzu/carvel-secretgen-controller/blob/develop/docs/install.md). **NOTE**: This tool is experimental.

* Cert Manager, with [installation instructions here](https://cert-manager.io/docs/installation/).

* Tanzu Build Service for building images, with [installation instructions here](https://docs.vmware.com/en/Tanzu-Application-Platform/0.3/tap/GUID-install-components.html?hWord=N4IghgNiBcIJYDsDOAXSFEHMAEKBGSIAvkA#install-tbs).

* Contour for ingress control, with [installation instructions here](https://projectcontour.io/getting-started/).


Once you have installed these prerequisites, proceed to [install the Application Service Adapter](install.md).
