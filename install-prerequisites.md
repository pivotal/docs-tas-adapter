# Installing prerequisites

This topic describes the first part of the installation process for the Application Service Adapter for VMware Tanzu Application Platform.

----

Use of the Application Service Adapter requires Cloud Foundry command-line interface (cf CLI) v8.1.0 or later.
For more information, see the [cf CLI repository](https://github.com/cloudfoundry/cli) on GitHub.

To install the Application Service Adapter, you need:

* Admin access to a Kubernetes cluster. See [Kubernetes cluster requirements](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-prerequisites.html#kubernetes-cluster-requirements-3) in _Prerequisites_.

* A container image registry. See [Tanzu Network and container image registry requirements](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-prerequisites.html#vmware-tanzu-network-and-container-image-registry-requirements-0) in _Prerequisites_.

The following tools must be installed in the workstation environment in which you intend to perform the installation:

* The Kubernetes CLI (kubectl) v1.22, v1.22, or v1.23.

* Tanzu CLI and its plug-ins. See [Install or update the Tanzu CLI and plugins](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-install-tanzu-cli.html#install-or-update-the-tanzu-cli-and-plugins-4) in _Installing the Tanzu CLI_.
   > **Note:** After you install the Tanzu CLI, run `tanzu plugin list` to see which plug-ins are installed. For the adapter to run, you need `package` and `secret` plug-ins. To install these plug-ins, run:
    ```bash
    tanzu plugin install --local cli package
    tanzu plugin install --local cli secret
    ```

The following dependencies must be installed to the target Kubernetes cluster before installing Application Service Adapter. If you have completed an installation of the `full` profile of Tanzu Application Platform v1.1 in your target Kubernetes cluster, then these dependencies are already present.

* Cluster Essentials. See [Install Cluster Essentials for VMware Tanzu](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-install-tanzu-cli.html#install-cluster-essentials-for-tanzu-3) in _Installing the Tanzu CLI_.
   > **Note:** If you are operating a Tanzu Kubernetes Grid or Tanzu Community Edition cluster, the Cluster Essentials are already installed.

* cert-manager v1.5.3 or later for managing internal certificates.
   * If you have installed Tanzu Application Platform v1.1 with a profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.1 without using a profile, see [Install cert-manager, Contour, and FluxCD Source Controller](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html).

* Contour v1.18.2 or later for ingress control.
   * If you have installed Tanzu Application Platform v1.1 with a profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.1 without using a profile, see [Install cert-manager, Contour, and FluxCD Source Controller](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html).
   > **Note:** You must configure Contour's ingress to provision a LoadBalancer. See [Configure LoadBalancer for Contour ingress](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-install.html#configure-loadbalancer-for-contour-ingress-7) in _Installing the Tanzu Application Platform Package and Profiles_.

* Service Bindings v0.7.1 or later for ingress control.
   * If you have installed Tanzu Application Platform v1.1 with the `full`, `iterate`, `run`, or `light` profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.1 without using a profile, see [Install Service Bindings](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-service-bindings-install-service-bindings.html).

* Tanzu Build Service v1.5.0 or later for building images.
   * If you have installed Tanzu Application Platform v1.1 with the `full`, `iterate`, `build`, or `light` profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.1 without using a profile, see [Install Tanzu Build Service](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-tanzu-build-service-install-tbs.html).

After you have installed these prerequisites, proceed to [Installing Application Service Adapter](install.md).
