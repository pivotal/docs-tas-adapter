# Installing Prerequisites

This document describes the first part of the installation process for the Application Service Adapter for VMware Tanzu Application Platform.

----

**NOTE**: v0.1 is a limited beta release, and is only available to invited users. If you are interested in testing this version, please reach out to emalm@vmware.com.

Once you are invited to participate in the beta testing, you may use your Tanzu Network account to download [Application Service Adapter](https://network.tanzu.vmware.com/products/app-service-adapter/) and accept the EULA.

This beta version relies on an experimental version of CF CLI. You should receive a CF CLI binary from our team.

To install Application Service Adapter for Tanzu Application Platform, you need:

* A container image registry, see [requirements here](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#tanzu-network-and-container-image-registry-requirements-1).
* The administrator access to a Kubernetes cluster, see [requirements here](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#kubernetes-cluster-requirements-2).

* Kubernetes CLI, kubectl v1.19, v1.20 or v1.21.

* Tanzu CLI and its plugins, see [installation instructions here](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#install-or-update-the-tanzu-cli-and-plugins-5).

* Developer tools from the [Carvel](https://carvel.dev/) suite, specifically:  
    *  kapp, v0.37.0 or later, see [installation instructions here](https://carvel.dev/kapp/docs/latest/install/)
    * kapp-controller v0.29.0 or later, see [installation instructions here](https://carvel.dev/kapp-controller/docs/latest/install/)
    * secretgen-controller v0.6.0 or later, see [installation instructions here](https://github.com/vmware-tanzu/carvel-secretgen-controller/blob/develop/docs/install.md). **NOTE**: This tool is experimental.

* Cert Manager, see [installation instructions here](https://cert-manager.io/docs/installation/)

In addition, as an add-on for Tanzu Application Platform, we expect Tanzu Application Platform installed. However, if you don't want to install all of its packages, we require at least these components installed in the same Kubernetes cluster:

* Tanzu Build Service for building images, see [installation instructions here](https://docs.vmware.com/en/Tanzu-Application-Platform/0.3/tap/GUID-install-components.html?hWord=N4IghgNiBcIJYDsDOAXSFEHMAEKBGSIAvkA#install-tbs).

* Contour for ingress control, see [instructions here](https://projectcontour.io/getting-started/).


