# Installing Application Service Adapter

This topic describes how to install the Application Service Adapter for VMware Tanzu Application Platform system.

----

Once you have installed all the [prerequisites](install-prerequisites.md), make sure that you set the Kubernetes context to the cluster where you have installed Kpack and Contour.

1. Create a namespace called `tas-adapter-install` for deploying the TAS adapter to your cluster.

    ```bash
    kubectl create ns tas-adapter-install
    ```

1. Create an image pull secret to store your Tanzu Network credentials. These are required to allow the cluster to pull images from the Tanzu Network registry.

    ```bash
    tanzu secret registry add tanzunet-registry \
      --username "TANZU-NET-USER" --password "TANZU-NET-PASSWORD" \
      --server dev.registry.tanzu.vmware.com \
      --export-to-all-namespaces --yes \
      --namespace tas-adapter-install
    ```

    Where `TANZU-NET-USER` and `TANZU-NET-PASSWORD` are your credentials for Tanzu Network.

1. Add the TAS adapter package repository to the cluster.

    ```bash
    tanzu package repository add tas-adapter-repository \
      --url dev.registry.tanzu.vmware.com/app-service-adapter/tas-adapter-package-repo:latest \
      --namespace tas-adapter-install
    ```
1. Verify the contents of the package repository.

   ```bash
   tanzu package available list \
     --namespace tas-adapter-install
   ```

1. Generate a certificate for TLS ingress to the API.

   If you are using openssl, or libressl v3.1.0 or later:

   ```bash
   openssl req -x509 -newkey rsa:4096 \
     -keyout tls.key -out tls.crt \
     -nodes -subj '/CN=<API-FQDN>' \
     -addext "subjectAltName = DNS:<API-FQDN>" \
     -days 365
   ```

   If you are using an older version of libressl (the default on OSX):

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
   * `<API-FQDN>` is the FQDN that you want to use for the TAS adapter API.
   * `<API-REPLICA-COUNT>` is the desired number of instances for the TAS adapter API deployment.
   * `<TLS-CRT>` is the PEM-encoded public certificate for the TAS adapter API.
   * `<TLS-KEY>` is the PEM-encoded private key for the TAS adapter API.
   * `<PACKAGE-REGISTRY-BASE>` is the container registry "folder"/"project" where application source code (Packages) will be uploaded
   * `<KPACK-TAG-PREFIX>` is the container registry "folder"/"project" where runnable application images (Droplets) will be uploaded

1. Install the TAS adapter to the cluster.

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

1. Update the DNS entry for the API endpoint for your cluster. This step is highly dependent on the IaaS used to provision your cluster.

   For clusters that support LoadBalancer services, you can obtain the external IP address of the LoadBalancer Service.

   ```bash
   kubectl get service envoy -n projectcontour
   ```

1. Verify that the Contour HTTPProxy for the API endpoint is valid.

   ```bash
   kubectl get httpproxy cf-k8s-api-proxy -n cf-k8s-api-system
   ```


Now you should be able to target the API endpoint using `cf api`.

**NOTE**: In this beta version we have not implemented API authentication. So once you have verified the validity of the API endpoint, you are able to deploy workloads to Application Service Adapter.

Go to [Getting Started](getting-started.md) to test the adapter.