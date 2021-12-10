# Installing Prerequisites

This topic describes the first part of the installation process for the Application Service Adapter for VMware Tanzu Application Platform.

----

> **Note**: v0.1 is a limited beta release, and is only available to invited users. If you are interested in testing this version, reach out to your account team. After you are invited to participate in the beta testing, you will have access to the [Application Service Adapter](https://network.tanzu.vmware.com/products/app-service-adapter/) from Tanzu Network.

This beta version requires an experimental version of the Cloud Foundry command-line interface (cf CLI). You receive instructions to obtain the cf CLI binary as part of the onboarding process for the limited beta.
For more information, see the [cf CLI repository](https://github.com/cloudfoundry/cli) on GitHub.

To install the Application Service Adapter, you need:

* Administrator access to a Kubernetes cluster. See [Kubernetes Cluster Requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#kubernetes-cluster-requirements-2) in _Installing Part I: Prerequisites, EULA, and CLI_.
   > **Note**: There is a known issue with Kubernetes v1.22 and Tanzu Build Service (TBS)/kpack. Avoid v1.22.x until a fix is released. To deploy a TCE workload cluster with a different Kubernetes version, see [Deploy Tanzu Kubernetes Clusters with Different Kubernetes Versions](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/1.3/vmware-tanzu-kubernetes-grid-13/GUID-tanzu-k8s-clusters-k8s-versions.html).

* A container image registry. See [Tanzu Network and Container Image Registry Requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#tanzu-network-and-container-image-registry-requirements-1) in _Installing Part I: Prerequisites, EULA, and CLI_.

The following tools must be installed in the workstation environment in which you intend to perform the installation:

* The Kubernetes CLI (kubectl) v1.19, v1.20, or v1.21.

* Tanzu CLI and its plugins. See [Install or Update the Tanzu CLI and Plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-general.html#install-or-update-the-tanzu-cli-and-plugins-5) in _Installing Part I: Prerequisites, EULA, and CLI_.

The following dependencies must be installed to the target Kubernetes cluster before installing the Application Service Adapter. If you have completed an installation of Tanzu Application Platform in your target Kubernetes cluster, then these dependencies are already present.

* Carvel kapp-controller v0.29.0 or later. See [kapp-controller installation instructions](https://carvel.dev/kapp-controller/docs/latest/install/).

* Carvel secretgen-controller v0.6.0 or later. See [secretgen-controller installation instructions](https://github.com/vmware-tanzu/carvel-secretgen-controller/blob/develop/docs/install.md).
   > **Note**: This tool is experimental.

* cert-manager. See [cert-manager installation instructions](https://cert-manager.io/docs/installation/).

* Tanzu Build Service (TBS) for building images. See with [Install Tanzu Build Service](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.3/tap-0-3/GUID-install-components.html#install-tbs) in _Installing Individual Packages_.

* Contour for ingress control. See [Contour installation instructions](https://projectcontour.io/getting-started/).

After you have installed these prerequisites, proceed to [Installing the Application Service Adapter](install.md).
