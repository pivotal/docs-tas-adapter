# Installing Prerequisites

This topic describes the first part of the installation process for the Application Service Adapter for VMware Tanzu Application Platform.

----

To install Application Service Adapter for Tanzu Application Platform, you need:

* A container image registry. See [Tanzu Network and Container Image Registry Requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#tanzu-network-and-container-image-registry-requirements-1) in _Installing Part I: Prerequisites, EULA, and CLI_.

* Administrator access to a Kubernetes cluster. See [Kubernetes Cluster Requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#kubernetes-cluster-requirements-2) in _Installing Part I: Prerequisites, EULA, and CLI_.

* Kubernetes CLI (kubectl) v1.19, v1.20, or v1.21.

* Tanzu CLI and its plugins. See [Install or Update the Tanzu CLI and Plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#install-or-update-the-tanzu-cli-and-plugins-5) in _Installing Part I: Prerequisites, EULA, and CLI_.

* The following developer tools from the [Carvel](https://carvel.dev/) suite:
    * kapp v0.37.0 or later. See [kapp installation instructions](https://carvel.dev/kapp/docs/latest/install/).
    * kapp-controller v0.29.0 or later. See [kapp-controller installation instructions](https://carvel.dev/kapp-controller/docs/latest/install/).
    * secretgen-controller v0.6.0 or later. See [secretgen-controller installation instructions](https://github.com/vmware-tanzu/carvel-secretgen-controller/blob/develop/docs/install.md).
    **Note**: This tool is experimental.

* cert-manager. See [cert-manager installation instructions](https://cert-manager.io/docs/installation/).

In addition, as an add-on for Tanzu Application Platform, we expect Tanzu Application Platform installed. However, if you don't want to install all of its packages, we require at least these components installed in the same Kubernetes cluster:

* kpack for building images. See [kpack installation instructions](https://github.com/pivotal/kpack/blob/main/docs/install.md).

* Contour for ingress control. See [Contour installation instructions](https://projectcontour.io/getting-started/).

Use your Tanzu Network account to download [Application Service Adapter](https://network.tanzu.vmware.com/products/app-service-adapter/), and make sure to accept the EULA.

**Note**: Application Service Adapter v0.1 is a limited beta release, and is only available to invited users. If you are interested in testing this version, please reach out to emalm@vmware.com.
