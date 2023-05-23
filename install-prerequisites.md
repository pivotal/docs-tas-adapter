# Installation prerequisites

This topic describes the first part of installation for Application Service Adapter for VMware Tanzu Application Platform.

----

Application Service Adapter requires using Cloud Foundry command-line interface (cf CLI) v8.1.0 or later. VMware recommends using v8.5.0 or later.
For more information, see the [cf CLI repository](https://github.com/cloudfoundry/cli) on GitHub.

### <a id="kubernetes-cluster-image-registry"></a>Kubernetes cluster and container image registry

To install Application Service Adapter, you need:

* Admin access to a Kubernetes cluster that meets the same requirements as the version of Tanzu Application Platform you have installed, either v1.4 or v1.5. See [Kubernetes cluster requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/prerequisites.html#kubernetes-cluster-requirements-3) in _Prerequisites_.
  * As of Tanzu Application Platform v1.5, Kubernetes v1.24, v1.25, or v1.26 is required. There are additional requirements for some implementations. For example, Amazon Elastic Kubernetes Service (EKS) requires containerd as the Container Runtime Interface (CRI) among with other requirements.

* A container image registry. See [VMware Tanzu Network and container image registry requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.5/tap/prerequisites.html#vmware-tanzu-network-and-container-image-registry-requirements-0) in _Prerequisites_. Application Service Adapter does not support Amazon's Elastic Container Registry (ECR).

### <a id="required-installation-tools"></a>Required installation tools

The following tools must be installed in the workstation environment in which you intend to perform the installation:

* The Kubernetes CLI (kubectl) v1.23-v1.26.

* Tanzu CLI and its plug-ins. See [Install or update the Tanzu CLI and plug-ins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/install-tanzu-cli.html#install-or-update-the-tanzu-cli-and-plugins-3) in _Accepting Tanzu Application Platform EULAs and installing Tanzu CLI_.

After you install the Tanzu CLI, run `tanzu plugin list` to verify that the required `package` and `secret` plug-ins are installed. To install these plug-ins, run:

    tanzu plugin install --local cli package
    tanzu plugin install --local cli secret

### <a id="required-components"></a>Required components

The following dependencies must be installed to the target Kubernetes cluster before installing Application Service Adapter. If you completed an installation of the `full` profile or the `iterate` profile of Tanzu Application Platform v1.3 or v1.4 in your target Kubernetes cluster, these dependencies are already present.

* Cluster Essentials. See [Deploying Cluster Essentials v1.4](https://docs.vmware.com/en/Cluster-Essentials-for-VMware-Tanzu/1.4/cluster-essentials/deploy.html).
   > **Note** If you are operating a Tanzu Kubernetes Grid cluster, the Cluster Essentials are already installed.

* cert-manager v1.7.2 or later for managing internal certificates.
   * If you installed Tanzu Application Platform v1.3 or v1.4 with a profile, this package is installed.
   * If you installed Tanzu Application Platform v1.3 without using a profile, see [Install cert-manager](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html#install-certmanager-1) in _Install cert-manager, Contour_.
   * If you installed Tanzu Application Platform v1.4 without using a profile, see [Install cert-manager](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/cert-manager-install.html).

* Contour v1.22.0 or later for ingress control.
   * If you installed Tanzu Application Platform v1.3 or v1.4 with a profile, this package is installed.
   * If you installed Tanzu Application Platform v1.3 without using a profile, see [Install Contour](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html#install-contour-2) in _Install cert-manager, Contour_.
   * If you installed Tanzu Application Platform v1.4 without using a profile, see [Install Contour](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/contour-install.html).
   > **Note** You must configure Contour's ingress to provision a LoadBalancer. See the default configuration in [Install your Tanzu Application Platform profile](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.5/tap/install-online-profile.html#install-your-tanzu-application-platform-profile-2) in _Installing Tanzu Application Platform Package and Profiles_.

* Service Bindings v0.8.1 or later.
   * If you installed Tanzu Application Platform v1.3 or v1.4 with the `full`, `iterate`, or `run` profile, this package is installed.
   * If you installed Tanzu Application Platform without using a profile, see [Install Service Bindings](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/service-bindings-install-service-bindings.html).

* Tanzu Build Service v1.7.4 or later for building images.
   * If you installed Tanzu Application Platform v1.3 with the `full`, `iterate`, or `build` profile, this package is installed.
   * If you installed Tanzu Application Platform without using a profile, see [Installing Tanzu Build Service](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/tanzu-build-service-install-tbs.html).

### <a id="required-components-cartographer"></a>Required components for experimental Cartographer integration

The following dependencies are required to be installed to the target Kubernetes cluster to opt into using the experimental Cartographer integration. If you installed Tanzu Application Platform v1.3 or v1.4 with the `full`, `iterate`, or `build` profile, these dependencies are already present.

* Cartographer Conventions v0.7.1 or later.
  * If you installed Tanzu Application Platform without using a profile, see [Install Cartographer Conventions](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/cartographer-conventions-install-conv-service.html).

* Out of the Box Templates v0.10.5 or later.
   * If you installed Tanzu Application Platform without using a profile, see [Install Out of the Box Templates](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/scc-install-ootb-templates.html).

* Supply Chain Choreographer v0.5.4 or later.
   * If you installed Tanzu Application Platform without using a profile, see [Install Supply Chain Choreographer](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/scc-install-scc.html).

* Source Controller v0.5.1 or later.
   * If you installed Tanzu Application Platform without using a profile, see [Install Source Controller](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/source-controller-install-source-controller.html).

* Tekton v0.39.0 or later.
   * If you installed Tanzu Application Platform without using a profile, see [Install Tekton](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/tekton-install-tekton.html).

### <a id="recommended-components"></a>Recommended components

VMware recommends installing the following dependencies to the target Kubernetes cluster.

* [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server/) v0.4.0 or later for app instance resource metrics.
  > **Note** Many Kubernetes distributions automatically come with the Metrics Server installed. If the API resources in your target cluster include the `PodMetrics` Kind in the `metrics.k8s.io` API group, the Metrics Server is already present.

After you installed these prerequisites, proceed to [Install Application Service Adapter](install.md).

### <a id="ecr-configuration"></a>AWS IAM Configuration for ECR

When installed to Amazon Elastic Container Service with ECR, Application Service
Adapter requires a similar configuration to the [setup required by Tanzu
Application Platform](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/install-aws-resources.html),
but needs access to additional resources and trusted service accounts.

Follow the Tanzu Application Platform instructions, with these amendments:

* In `build-service-trust-policy.json` add the following service accounts to the
  list of trusted service accounts:
  * "system:serviceaccount:tas-adapter-system:korifi-api-system-serviceaccount"
  * "system:serviceaccount:tas-adapter-system:korifi-controllers-controller-manager"
  * "system:serviceaccount:tas-adapter-system:korifi-kpack-build-controller-manager"
  * "system:serviceaccount:tas-adapter-system:cartographer-builder-runner-controller-manager"
  * "system:serviceaccount:*:kpack-service-account"

* In build-service-policy.json, add the following Resource:
 * "arn:aws:ecr:${AWS_REGION}:${AWS_ACCOUNT_ID}:repository/${REPOSITORY_PREFIX_PATH}*"

   Where `REPOSITORY_PREFIX_PATH` is the path portion of the `REPOSITORY-PREFIX`
   you will provide when setting `tas-adapter-values.yaml` for installation.
