# Installing Application Service Adapter

This topic describes how to install the Application Service Adapter for VMware Tanzu Application Platform system.

* [Preparing the Kubernetes cluster](#prepare-kubernetes-cluster)
* [Configuring the installation settings](#configure-installation-settings)
* [Installing the adapter](#install-adapter)
* [Configuring Tanzu Application Service to work with the adapter](#configure-tbs)
* [Configuring DNS for the adapter](#configure-dns)
----


After you have completed the steps in [Installing Prerequisites](install-prerequisites.md), set the Kubernetes context to the cluster where you have installed kpack and Contour.

To install Application Service Adapter:

1. Create a namespace called `tas-adapter-install` for deploying Application Service Adapter to your cluster:

    ```bash
    kubectl create ns tas-adapter-install
    ```

1. Create an image pull secret to store your Tanzu Network credentials. These are required for the cluster to be able to pull images from the Tanzu Network registry.

    ```bash
    tanzu secret registry add tanzunet-registry \
      --username "TANZU-NET-USER" --password "TANZU-NET-PASSWORD" \
      --server registry.tanzu.vmware.com \
      --export-to-all-namespaces --yes \
      --namespace tas-adapter-install
    ```

    Where `TANZU-NET-USER` and `TANZU-NET-PASSWORD` are your credentials for Tanzu Network.

1. Add the Application Service Adapter package repository to the cluster.

    ```bash
    tanzu package repository add tas-adapter-repository \
      --url registry.tanzu.vmware.com/app-service-adapter/tas-adapter-package-repo:0.1.0-build.1 \
      --namespace tas-adapter-install
    ```
1. Verify that the package repository contains the Application Service Adapter package.

    ```bash
    tanzu package available list \
      --namespace tas-adapter-install
    ```

    The output should look like the following:

    ```bash
    NAME                                    DISPLAY-NAME                 SHORT-DESCRIPTION                                                   LATEST-VERSION
    application-service-adapter.vmware.com  Application Service Adapter  Application Service Adapter for VMware Tanzu® Application Platform  0.1.0
    ```

1. Generate a self-signed certificate for TLS ingress to the Application Service Adapter API.

    If you are using `openssl`, or `libressl v3.1.0` or later:

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=<API-FQDN>' \
      -addext "subjectAltName = DNS:<API-FQDN>" \
      -days 365
    ```
    where `<API-FQDN>` is the fully qualified domain name (FQDN) that you want to use to access the API.


    If you are using a version of `libressl` older than v3.1.0 (the default on macOS):

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=<API-FQDN>' \
      -extensions SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[ SAN ]\nsubjectAltName='DNS:<API-FQDN>'")) \
     -days 365
    ```

1. List the installation settings for the `application-service-adapter` package.

    ```bash
    tanzu package available get application-service-adapter.vmware.com/0.1.0 --values-schema --namespace tas-adapter-install
    ```

    It should output a list of settings:

    ```
    | Retrieving package details for application-service-adapter.vmware.com/0.1.0...
      KEY                         DEFAULT  TYPE     DESCRIPTION
      api_ingress.fqdn                     string   FQDN used to access the CF API
      api_ingress.replicas        1        integer  Desired number of API instances
      api_ingress.tls.crt                  string   TLS certificate for the CF API (PEM format)
      api_ingress.tls.key                  string   TLS private key for the CF API (PEM format)
      kpack_image_tag_prefix               string   Container registry repository where staged, runnable app images (Droplets) will be stored
      package_registry_base_path           string   Container registry repository where uploaded app source code (Packages) will be stored
    ```

## <a id="configure-installation-settings"></a>Configuring the installation settings
1. Create a `tas-adapter-values.yml` file with the desired installation settings, following the schema specified for the package.

    You can use the following sample as a template:

    ```yaml
    ---
    api_ingress:
      fqdn: "<API-FQDN>"
      replicas: <API-REPLICA-COUNT>
      tls:
        crt: "<TLS-CRT>"
        key: "<TLS-KEY>"
    package_registry_base_path: "<PACKAGE-REGISTRY-BASE>"
    kpack_image_tag_prefix: "<KPACK-TAG-PREFIX>"
    ```

    Where:
    * `<API-FQDN>` is the FQDN that you want to use for the Application Service Adapter API.
    * `<API-REPLICA-COUNT>` is the desired number of instances for the Application Service Adapter API deployment.
    * `<TLS-CRT>` is the PEM-encoded public certificate for the Application Service Adapter API.
    * `<TLS-KEY>` is the PEM-encoded private key for the Application Service Adapter API.
    * `<PACKAGE-REGISTRY-BASE>` is the container registry "folder"/"project" where application source code (Packages) will be uploaded.
    * `<KPACK-TAG-PREFIX>` is the container registry "folder"/"project" where runnable application images (Droplets) will be uploaded.

## <a id="install-adapter"></a>Installing the adapter

1. Install the Application Service Adapter to the cluster.

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.vmware.com \
      --version 0.1.0 \
      --values-file tas-adapter-values.yml \
      --namespace tas-adapter-install
    ```

1. Verify that the package install was successful:

    ```bash
    tanzu package installed get tas-adapter \
      --namespace tas-adapter-install
    ```

    The output should look like the following:

    ```bash
    | Retrieving installation details for tas-adapter...
    NAME:                    tas-adapter
    PACKAGE-NAME:            application-service-adapter.vmware.com
    PACKAGE-VERSION:         0.1.0
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:
    ```
## <a id="configure-tbs"></a>Configuring Tanzu Build Service to work with the adapter

In order to stage applications, we need to create the following secret and service account in the `cf` namespace created by the Application Service Adapter install package, along with kpack resources that provide the Paketo cloud-native buildpacks.

1. Create image registry secret   

    This secret is used to push/pull images from the registry specified earlier in the `tas-adapter-values.yaml` file

    ```yaml
    kubectl create secret docker-registry image-registry-credentials \
      -n cf \
      --docker-server=<DOCKER_SERVER> \
      --docker-username=<DOCKER_USERNAME> \
      --docker-password=<DOCKER_PASSWORD>"
    ```
1. Create and `kubectl apply` a `service_account.yaml`
    
    ```yaml
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: kpack-service-account
      namespace: cf
    secrets:
    - name: image-registry-credentials
    imagePullSecrets:
    - name: image-registry-credentials
    ```
1. Create and `kubectl apply` a `cluster_stack.yaml`
    ```yaml
    apiVersion: kpack.io/v1alpha2
    kind: ClusterStack
    metadata:
      name: cf-default-stack
    spec:
      id: "io.buildpacks.stacks.bionic"
      buildImage:
        image: "paketobuildpacks/build:base-cnb"
      runImage:
        image: "paketobuildpacks/run:base-cnb"

    ```
1. Create and `kubectl apply` a `cluster_store.yaml`
    ```yaml
    apiVersion: kpack.io/v1alpha2
    kind: ClusterStore
    metadata:
      name: cf-default-buildpacks
    spec:
      sources:
      - image: gcr.io/paketo-buildpacks/java:5.21.1
      - image: gcr.io/paketo-buildpacks/nodejs
      - image: gcr.io/paketo-buildpacks/ruby
      - image: gcr.io/paketo-buildpacks/procfile:4.4.1
      - image: gcr.io/paketo-buildpacks/go
    ```
1. Create and `kubectl apply` a `cluster_builder.yaml`

    **NOTE**: replace the tag to match your container registry defined in your `tas-adapter-values.yaml`  

    ```yaml
    apiVersion: kpack.io/v1alpha2
    kind: ClusterBuilder
    metadata:
      name: cf-kpack-cluster-builder
    spec:
      serviceAccountRef:
        name: kpack-service-account
        namespace: cf
      # Replace with real docker registry
      tag: “<REPLACE-WITH-PACKAGE-REGISTRY-BASE>/builder”
      stack:
        name: cf-default-stack
        kind: ClusterStack
      store:
        name: cf-default-buildpacks
        kind: ClusterStore
      order:
      - group:
        - id: paketo-buildpacks/java
      - group:
        - id: paketo-buildpacks/go
      - group:
        - id: paketo-buildpacks/nodejs
      - group:
        - id: paketo-buildpacks/ruby
      - group:
        - id: paketo-buildpacks/procfile
    ```
    
## <a id="configure-dns"></a>Configuring DNS for the adapter
1. Add a DNS entry for the FQDN of the Application Service Adapter API. This step varies depending on the IaaS used to provision your cluster.

    For clusters that support LoadBalancer services, you can obtain the external IP address of the LoadBalancer Service.

    ```bash
    kubectl get service envoy -n projectcontour -ojsonpath='{.status.loadBalancer.ingress[*].ip}'
    ```

    Create an A record in your DNS zone that resolves the API FQDN to this external IP address.

1. Verify that the Contour HTTPProxy for the API endpoint is valid:

    ```bash
    kubectl get httpproxy cf-k8s-api-proxy -n cf-k8s-api-system
    ```

    The output should look like the following:
    ```bash
    NAME               FQDN                         TLS SECRET                STATUS   STATUS DESCRIPTION
    cf-k8s-api-proxy   api.tas.em.migration.quest   cf-k8s-api-ingress-cert   valid    Valid HTTPProxy
    ```

    Now you should be able to target the API endpoint by running `cf api <API-FQDN>` and start deploying applications. Go to [Getting Started](getting-started.md) to test the adapter.
