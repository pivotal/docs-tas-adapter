# Installing Application Service Adapter to air-gapped environments

For instructions on how to install prerequisites in air-gapped environments, see:

* [Tanzu Application Platform](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-install.html)
* [Tanzu Build Service](https://docs.vmware.com/en/Tanzu-Build-Service/1.5/vmware-tanzu-build-service/GUID-installing-no-kapp.html#installation-to-air-gapped-environment)

The topics below describe how Application Service Adapter for VMware Tanzu Application Platform system are installed to a Kubernetes Cluster and registry that are air-gapped from external traffic:

* [Relocate Images to a Registry](#relocate-images-to-registry)
* [Install the package repository](#install-package-repo)

----

## <a id="relocate-images-to-registry"></a>Relocate images to a registry (air-gapped)

This procedure relocates images from the Tanzu Network registry to an internal container image registry that is available to the air-gapped environment through a local machine.
The local machine must have access to the air-gapped environment.

1. Log in to the Tanzu Network registry with your Tanzu Network credentials:
   ```bash
    docker login registry.tanzu.vmware.com
   ```

2. Copy the Application Service Adapter bundle to a tarball with the [Carvel](https://carvel.dev/imgpkg/) `imgpkg` tool by running:
   ```bash
    imgpkg copy -b registry.tanzu.vmware.com/app-service-adapter/tas-adapter-package-repo:0.10.0 --to-tar tas-adapter-package-repo.tar
   ```
3. Move the tarball file `tas-adapter-package-repo.tar` to the local machine that has access to the air-gapped environment.

4. Log in to the internal image registry from the local machine:
   ```bash
    docker login <INTERNAL-REGISTRY>
   ```

   Where `INTERNAL-REGISTRY` is the name of your internal image registry.

5. Unpackage the images from the tarball to the internal registry:
   ```bash
    imgpkg copy --tar tas-adapter-package-repo.tar --to-repo=<INTERNAL-REGISTRY> /tas-adapter-package-repo
   ```

## <a id="install-package-repo"></a>Install the package repository

After the images are relocated:

1. Create a namespace called `tas-adapter-install` for deploying Application Service Adapter to your cluster.

    ```bash
    kubectl create ns tas-adapter-install
    ```

2. Create an image pull secret to store credentials to your internal registry. These are required so that the cluster can pull images from the internal registry.

    ```bash
    tanzu secret registry add internal-image-registry \
      --username <INTERNAL-REGISTRY-USERNAME> \
      --password <INTERNAL-REGISTRY-PASSWORD> \
      --server <INTERNAL-REGISTRY> \
      --export-to-all-namespaces --yes \
      --namespace tas-adapter-install
    ```

   Where `INTERNAL-REGISTRY-USERNAME` and `INTERNAL-REGISTRY-PASSWORD` are your credentials for `INTERNAL-REGISTRY`.

3. Add the Application Service Adapter package repository to the cluster.

    ```bash
    tanzu package repository add tas-adapter-repository \
      --url <INTERNAL-REGISTRY> /tas-adapter-package-repo:0.10.0 \
      --namespace tas-adapter-install
    ```
4. Verify that the package repository contains the Application Service Adapter package.

    ```bash
    tanzu package available list \
      --namespace tas-adapter-install
    ```

   The output includes the Application Service Adapter package:

    ```bash
    NAME                                          DISPLAY-NAME                 SHORT-DESCRIPTION                                                   LATEST-VERSION
    ...
    application-service-adapter.tanzu.vmware.com  Application Service Adapter  Application Service Adapter for VMware Tanzu Application Platform  0.10.0
    ...
    ```

5. List the installation settings for the `application-service-adapter` package.

    ```bash
    tanzu package available get application-service-adapter.tanzu.vmware.com/0.10.0 --values-schema --namespace tas-adapter-install
    ```

   It should output a list of settings similar to:

    ```
    | Retrieving package details for application-service-adapter.tanzu.vmware.com/0.10.0...
      KEY                         DEFAULT  TYPE     DESCRIPTION
      api_auth_proxy.ca_cert.data          string   TLS CA certificate of your cluster's auth proxy
      api_auth_proxy.host                  string   FQDN of your cluster's auth proxy
      api_ingress.fqdn                     string   FQDN used to access the CF API
      api_ingress.tls.crt                  string   TLS certificate for the CF API (PEM format)
      api_ingress.tls.key                  string   TLS private key for the CF API (PEM format)
      app_ingress.default_domain           string   Default application domain
      app_ingress.tls.crt                  string   TLS certificate for the default application domain (PEM format)
      app_ingress.tls.key                  string   TLS private key for the default application domain (PEM format)
      app_registry.path.droplets           string   Container registry repository where staged, runnable app images (Droplets) will be stored
      app_registry.path.packages           string   Container registry repository where uploaded app source code (Packages) will be stored
      kpack_clusterbuilder_name  default   string   Name of the kpack cluster builder to use for staging
      ...
    ```

---

For installation and configuring instructions, see the [install](install.md) guide.
