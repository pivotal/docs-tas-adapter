# Installation prerequisites

This topic describes the first part of installation for the Application Service Adapter for VMware Tanzu Application Platform.

----

Application Service Adapter requires using Cloud Foundry command-line interface (cf CLI) v8.1.0 or later. VMware recommends using v8.5.0 or later.
For more information, see the [cf CLI repository](https://github.com/cloudfoundry/cli) on GitHub.

### <a id="kubernetes-cluster-image-registry"></a>Kubernetes cluster and container image registry

To install the Application Service Adapter, you need:

* Admin access to a Kubernetes cluster that meets the same requirements as the version of Tanzu Application Platform you have installed, either v1.2 or v1.3. See [Kubernetes cluster requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-prerequisites.html#kubernetes-cluster-requirements-3) in _Prerequisites_.
  * As of Tanzu Application Platform v1.3, Kubernetes v1.22, v1.23, or v1.24 is required. There are additional requirements for some implementations. For example, Amazon Elastic Kubernetes Service (EKS) requires containerd as the Container Runtime Interface (CRI) among with other requirements.

* A container image registry. See [VMware Tanzu Network and container image registry requirements](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/prerequisites.html) in _Prerequisites_.

### <a id="required-installation-tools"></a>Required installation tools

The following tools must be installed in the workstation environment in which you intend to perform the installation:

* The Kubernetes CLI (kubectl) v1.22 or v1.23.

* Tanzu CLI and its plug-ins. See [Install or update the Tanzu CLI and plugins](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-install-tanzu-cli.html#install-or-update-the-tanzu-cli-and-plugins-3) in _Accepting Tanzu Application Platform EULAs and installing Tanzu CLI_.

After you install the Tanzu CLI, run `tanzu plugin list` to verify that the required `package` and `secret` plug-ins are installed. To install these plug-ins, run:

    ```bash
    tanzu plugin install --local cli package
    tanzu plugin install --local cli secret
    ```

### <a id="required-components"></a>Required components

The following dependencies must be installed to the target Kubernetes cluster before installing Application Service Adapter. If you completed an installation of the `full` profile or the `iterate` profile of Tanzu Application Platform v1.2 in your target Kubernetes cluster, these dependencies are already present.

* Cluster Essentials. See [Deploying Cluster Essentials v1.3](https://docs.vmware.com/en/Cluster-Essentials-for-VMware-Tanzu/1.3/cluster-essentials/GUID-deploy.html).
   > **Note** If you are operating a Tanzu Kubernetes Grid cluster, the Cluster Essentials are already installed.

* cert-manager v1.5.3 or later for managing internal certificates.
   * If you installed Tanzu Application Platform v1.3 with a profile, this package is installed.
   * If you installed Tanzu Application Platform v1.3 without using a profile, see [Install cert-manager](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html#install-certmanager-1) in _Install cert-manager, Contour_.

* Contour v1.18.2 or later for ingress control.
   * If you installed Tanzu Application Platform v1.3 with a profile, this package is installed.
   * If you installed Tanzu Application Platform v1.3 without using a profile, see [Install Contour](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-cert-mgr-contour-fcd-install-cert-mgr.html#install-contour-2) in _Install cert-manager, Contour_.
   > **Note** You must configure Contour's ingress to provision a LoadBalancer. See the default configuration in [Install your Tanzu Application Platform profile](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-install.html#install-your-tanzu-application-platform-profile-1) in _Installing Tanzu Application Platform Package and Profiles_.

* Service Bindings v0.7.2 or later.
   * If you installed Tanzu Application Platform v1.3 with the `full`, `iterate`, or `run` profile, this package is installed.
   * If you installed Tanzu Application Platform v1.3 without using a profile, see [Install Service Bindings](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-service-bindings-install-service-bindings.html).

* Tanzu Build Service v1.6.1 or later for building images.
   * If you installed Tanzu Application Platform v1.3 with the `full`, `iterate`, or `build` profile, this package is installed.
   * If you installed Tanzu Application Platform v1.3 without using a profile, see [Installing Tanzu Build Service](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-tanzu-build-service-install-tbs.html).

### <a id="required-components-cartographer"></a>Required components for experimental Cartographer integration

The following dependencies are required to be installed to the target Kubernetes cluster to opt into using the experimental Cartographer integration. If you installed Tanzu Application Platform v1.3 with the `full`, `iterate`, or `build` profile, these dependencies are already present.

* Out of the Box Templates v0.8.1 or later.
   * If you installed Tanzu Application Platform v1.3 without using a profile, see [Install Out of the Box Templates](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-scc-install-ootb-templates.html).

* Supply Chain Choreographer v0.4.0 or later.
   * If you installed Tanzu Application Platform v1.2 without using a profile, see [Install Supply Chain Choreographer](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-scc-install-scc.html).

* Source Controller v0.4.1 or later.
   * If you installed Tanzu Application Platform v1.2 without using a profile, see [Install Source Controller](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-source-controller-install-source-controller.html).

* Tekton v0.33.5 or later.
   * If you installed Tanzu Application Platform v1.2 without using a profile, see [Install Tekton](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.3/tap/GUID-tekton-install-tekton.html).

### <a id="recommended-components"></a>Recommended components

VMware recommends installing the following dependencies to the target Kubernetes cluster.

* [Kubernetes Metrics Server](https://github.com/kubernetes-sigs/metrics-server/) v0.4.0 or later for app instance resource metrics.
  > **Note** Many Kubernetes distributions automatically come with the Metrics Server installed. If the API resources in your target cluster include the `PodMetrics` Kind in the `metrics.k8s.io` API group, the Metrics Server is already present.

After you installed these prerequisites, proceed to [Install Application Service Adapter](install.md).

### <a id="ecr-configuration"></a>AWS IAM Configuration for ECR

When installed to Amazon Elastic Container Service with ECR, Application Service
Adapter requires a similar configuration to the [setup required by Tanzu
Application Service](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/aws-resources.html#create-iam-roles-5),
but needs access to additional resources and trusted service accounts.

Follow the TAP instructions, with these amendments:

* In `build-service-trust-policy.json` add the following service accounts to the
  list of trusted service accounts:
  * "system:serviceaccount:tas-adapter-system:korifi-api-system-serviceaccount"
  * "system:serviceaccount:tas-adapter-system:korifi-controllers-controller-manager"
  * "system:serviceaccount:tas-adapter-system:korifi-kpack-build-controller-manager"
  * "system:serviceaccount:tas-adapter-system:cartographer-builder-runner-controller-manager"
  * "system:serviceaccount:*:kpack-service-account"

* In build-service-policy.json add the following Resource:
 * "arn:aws:ecr:${AWS_REGION}:${AWS_ACCOUNT_ID}:repository/${REPOSITORY_PREFIX_PATH}*"
   Where `REPOSITORY_PREFIX_PATH` is the path portion of the `REPOSITORY-PREFIX`
   you will provide when setting `tas-adapter-values.yml` for installation.
