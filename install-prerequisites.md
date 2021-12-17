# Installing Prerequisites

This topic describes the first part of the installation process for the Application Service Adapter for VMware Tanzu Application Platform.

----

This beta version requires the latest release of the v8 version of the Cloud Foundry command-line interface (cf CLI).
For more information, see the [cf CLI repository](https://github.com/cloudfoundry/cli) on GitHub.

To install the Application Service Adapter, you need:

* Administrator access to a Kubernetes cluster. See [Kubernetes Cluster Requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.4/tap/GUID-install-general.html#kubernetes-cluster-requirements-2) in _Installing Part I: Prerequisites, EULA, and CLI_.
   > **Note**: There is a known issue with Kubernetes v1.22 and Tanzu Build Service (TBS)/kpack. Avoid v1.22.x until a fix is released. To deploy a TCE workload cluster with a different Kubernetes version, see [Deploy Tanzu Kubernetes Clusters with Different Kubernetes Versions](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/1.3/vmware-tanzu-kubernetes-grid-13/GUID-tanzu-k8s-clusters-k8s-versions.html).

* A container image registry. See [Tanzu Network and Container Image Registry Requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.4/tap/GUID-install-general.html#tanzu-network-and-container-image-registry-requirements-1) in _Installing Part I: Prerequisites, EULA, and CLI_.

The following tools must be installed in the workstation environment in which you intend to perform the installation:

* The Kubernetes CLI (kubectl) v1.19, v1.20, or v1.21.

* Tanzu CLI and its plugins. See [Install or Update the Tanzu CLI and Plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.4/tap/GUID-install-general.html#install-or-update-the-tanzu-cli-and-plugins-5) in _Installing Part I: Prerequisites, EULA, and CLI_.
   > **Note**: Once you installed the Tanzu CLI, run `tanzu plugin list` to see which plugins are installed. For the adapter to run, you will need `package` and `secret` plugins:
    ```bash
    tanzu plugin install --local cli package
    tanzu plugin install --local cli secret
    ```

    **NOTE**: Once you installed the Tanzu CLI, run `tanzu plugin list` to see which plugins are installed. For the adapter to run, you will need `package` and `secret` plugins:
    ```bash
    tanzu plugin install --local cli package
    tanzu plugin install --local cli secret
    ```

The following dependencies must be installed to the target Kubernetes cluster before installing the Application Service Adapter. If you have completed an installation of Tanzu Application Platform in your target Kubernetes cluster, then these dependencies are already present.
   > **Note**: You will need to [configure Contour's ingress to provision a LoadBalancer](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.4/tap/GUID-install.html#configure-loadbalancer-for-contour-ingress-5).

* [Cluster Essentials for VMware Tanzu](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.4/tap/GUID-install-general.html#install-cluster-essentials-for-vmware-tanzu-5).
   > If you are operating a Tanzu Kubernetes Grid or Tanzu Community Edition cluster, the Cluster Essentials are already installed.

* Cert Manager for managing internal certificates.
   * If you are installing on a TCE workload cluster, see [the TCE documentation for installing cert-manager](https://tanzucommunityedition.io/docs/latest/package-readme-cert-manager-1.6.1/).
   * If you have installed the TAP repository, see [cert-manager installation instructions](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.4/tap/GUID-install-components.html#install-prereqs) in _Installing Individual Packages_.
      > This package is also installed as part of either the `full` or `dev` profiles.

* Tanzu Build Service (TBS) for building images. See with [Install Tanzu Build Service](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/0.4/tap/GUID-install-components.html#install-tbs) in _Installing Individual Packages_.
   > This package is also installed as part of either the `full` or `dev` profiles.

* Contour for ingress control.
   * If you are installing on a TCE workload cluster, see [the TCE documentation for installing Contour](https://tanzucommunityedition.io/docs/latest/package-readme-contour-1.19.1/)
   * If you have installed the TAP repository, this package is installed as part of either the `full` or `dev` profiles.
   * To install OSS Contour, see the [Contour installation instructions](https://projectcontour.io/getting-started/).

After you have installed these prerequisites, proceed to [Installing the Application Service Adapter](install.md).
