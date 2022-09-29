# Installing prerequisites

This topic describes the first part of the installation process for the Application Service Adapter for VMware Tanzu Application Platform.

----

Use of the Application Service Adapter requires Cloud Foundry command-line interface (cf CLI) v8.1.0 or later, and v8.4.0 or later is recommended.
For more information, see the [cf CLI repository](https://github.com/cloudfoundry/cli) on GitHub.

To install the Application Service Adapter, you need:

* Admin access to a Kubernetes cluster. See [Kubernetes cluster requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-prerequisites.html#kubernetes-cluster-requirements-3) in _Prerequisites_.

* A container image registry. See [Tanzu Network and container image registry requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-prerequisites.html#vmware-tanzu-network-and-container-image-registry-requirements-0) in _Prerequisites_.

The following tools must be installed in the workstation environment in which you intend to perform the installation:

* The Kubernetes CLI (kubectl) v1.22 or v1.23.

* Tanzu CLI and its plug-ins. See [Install or update the Tanzu CLI and plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-install-tanzu-cli.html#install-or-update-the-tanzu-cli-and-plugins-3) in _Accepting Tanzu Application Platform EULAs and installing Tanzu CLI_.
   > **Note:** After you install the Tanzu CLI, run `tanzu plugin list` to see which plug-ins are installed. For the adapter to run, you need `package` and `secret` plug-ins. To install these plug-ins, run:
    ```bash
    tanzu plugin install --local cli package
    tanzu plugin install --local cli secret
    ```

The following dependencies must be installed to the target Kubernetes cluster before installing Application Service Adapter. If you have completed an installation of the `full` profile or the `iterate` profile of Tanzu Application Platform v1.2 in your target Kubernetes cluster, then these dependencies are already present.

* Cluster Essentials. See [Deploying Cluster Essentials v1.2](https://docs.vmware.com/en/Cluster-Essentials-for-VMware-Tanzu/1.2/cluster-essentials/GUID-deploy.html).
   > **Note:** If you are operating a Tanzu Kubernetes Grid or Tanzu Community Edition cluster, the Cluster Essentials are already installed.

* cert-manager v1.5.3 or later for managing internal certificates.
   * If you have installed Tanzu Application Platform v1.2 with a profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.2 without using a profile, see [Install cert-manager](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html#install-certmanager-1) in _Install cert-manager, Contour, and FluxCD Source Controller_.

* Contour v1.18.2 or later for ingress control.
   * If you have installed Tanzu Application Platform v1.2 with a profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.2 without using a profile, see [Install Contour](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html#install-contour-2) in _Install cert-manager, Contour, and FluxCD Source Controller_.
   > **Note:** You must configure Contour's ingress to provision a LoadBalancer. See [Configure LoadBalancer for Contour ingress](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-install.html#configure-loadbalancer-for-contour-ingress-7) in _Installing the Tanzu Application Platform Package and Profiles_.

* Service Bindings v0.7.2 or later.
   * If you have installed Tanzu Application Platform v1.2 with the `full`, `iterate`, or `run` profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.2 without using a profile, see [Install Service Bindings](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-service-bindings-install-service-bindings.html).

* Tanzu Build Service v1.6.1 or later for building images.
   * If you have installed Tanzu Application Platform v1.2 with the `full`, `iterate`, or `build` profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.2 without using a profile, see [Installing Tanzu Build Service](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-tanzu-build-service-install-tbs.html).

The following dependencies are recommended to be installed to the target Kubernetes cluster.

* Supply Chain Choreographer v0.4.0 or later.
   * If you have installed Tanzu Application Platform v1.2 with the `full`, `iterate`, `build`, or `light` profile, this package is installed.
   * If you have installed Tanzu Application Platform v1.2 without using a profile, see [Install Supply Chain Choreographer](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.2/tap/GUID-scc-install-scc.html).

* [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server/) v0.4.0 or later for app instance resource metrics.
  > **Note:** Many Kubernetes distributions automatically come with the Metrics Server installed. If the API resources in your target cluster include the `PodMetrics` Kind in the `metrics.k8s.io` API group, then the Metrics Server is already present.

After you have installed these prerequisites, proceed to [Installing Application Service Adapter](install.md).
