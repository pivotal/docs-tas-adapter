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

1. Create a `tas-adapter-values.yml` file with the installation configuration options. You can use the following sample as a template.

    ```yaml
    ---
    api_url: "<API-URL>"
    ```

    Where:
    * `<API-URL>` is the DNS URL that you want to use for the TAS adapter API.

    //TODO: we need to add the configuration for the certs and image registry.

1. Install the TAS adapter to the cluster.

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.vmware.com \
      --version 0.0.1 \
      --values-file tas-adapter-values.yml \
      --namespace tas-adapter-install
    ```

1. Verify that the package install was successful.

    ```bash
    tanzu package installed get tas-adapter \
      --namespace tas-adapter-install
    ```

1. Update the DNS entry for the API endpoint for your cluster. This step is highly dependent on the IaaS used to provision your cluster.

    * For clusters that support LoadBalancer services, you can use `kubectl get service envoy -n projectcontour` to obtain the external IP address of the LoadBalancer.

1. Generate a self-signed certificate.

    If you are using openssl, or libressl v3.1.0 or later:

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=<API-URL>' \
      -addext "subjectAltName = DNS:<API-URL>" \
      -days 365
    ```

    If you are using an older version of libressl (the default on OSX):

    ```bash
    openssl req -x509 -newkey rsa:4096 \
      -keyout tls.key -out tls.crt \
      -nodes -subj '/CN=<API-URL>' \
      -extensions SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[ SAN ]\nsubjectAltName='DNS:<API-URL>'")) \
      -days 365
    ```

1. Create a TLS secret called `cf-k8s-api-ingress-cert` using the self-signed certificate generated above, or from your own existing certificate:

    ```bash
    kubectl create secret tls \
      cf-k8s-api-ingress-cert \
      --cert=./tls.crt --key=./tls.key \
      -n cf-k8s-api-system
    ```

    **NOTE**: If you choose to generate a self-signed certificate, you will need to either skip TLS validation using the `--skip-ssl-validation` flag or use the `--cacert` flag with the generated certificate when connecting to the API.

1. Verify that the Contour HTTPProxy for the API endpoint is valid.

    ```bash
    kubectl get httpproxy cf-k8s-api-proxy -n cf-k8s-api-system
    ```

Now you should be able to target the API endpoint using `cf api`. 

//TODO: How do you sign in as an admin once you've targeted at the API endpoints?