# Installing Application Service Adapter

This topic describes how to install the Application Service Adapter for VMware Tanzu Application Platform system.

----

Once you have installed all the [prerequisites](install-prerequisites.md), make sure that you set the Kubernetes context to the cluster where you have installed the Tanzu Build Service and Contour.

1. Create a namespace called `tas-adapter-install` for deploying the TAS adapter to your cluster.

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

    If you are using openssl, or libressl v3.1.0 or later:

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=<API-FQDN>' \
      -addext "subjectAltName = DNS:<API-FQDN>" \
      -days 365
    ```
    where `<API-FQDN>` is the fully qualified domain name (FQDN) that you want to use to access the API.


    If you are using a version of libressl older than v3.1.0 (the default on macOS):

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

1. Install the Application Service Adapter to the cluster.

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.vmware.com \
      --version 0.1.0 \
      --values-file tas-adapter-values.yml \
      --namespace tas-adapter-install
    ```

1. Verify that the package install was successful.

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

1. Add a DNS entry for the FQDN of the Application Service Adapter API. This step varies depending on the IaaS used to provision your cluster.

    For clusters that support LoadBalancer services, you can obtain the external IP address of the LoadBalancer Service.

    ```bash
    kubectl get service envoy -n projectcontour -ojsonpath='{.status.loadBalancer.ingress[*].ip}'
    ```

    Create an A record in your DNS zone that resolves the API FQDN to this external IP address.

1. Verify that the Contour HTTPProxy for the API endpoint is valid.

    ```bash
    kubectl get httpproxy cf-k8s-api-proxy -n cf-k8s-api-system
    ```

    The output should look like the following:
    ```bash
    NAME               FQDN                         TLS SECRET                STATUS   STATUS DESCRIPTION
    cf-k8s-api-proxy   api.tas.em.migration.quest   cf-k8s-api-ingress-cert   valid    Valid HTTPProxy
    ```


Now you should be able to target the API endpoint by running `cf api <API-FQDN>`.

**NOTE**: In this beta version the Application Service Adapter API does not verify authentication. So once you have verified the validity of the API endpoint, you are able to deploy workloads to Application Service Adapter.

Go to [Getting Started](getting-started.md) to test the adapter.
