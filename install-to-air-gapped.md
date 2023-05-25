# Install Application Service Adapter to air-gapped environments

You can install Application Service Adapter to a K8s cluster and registry
that is air-gapped from outside traffic. This page has the steps you
need to take.

For instructions on how to install prerequisites in air-gapped environments, see:

* [Tanzu Application Platform](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.5/tap/install-offline-intro.html)
* [Tanzu Build Service](https://docs.vmware.com/en/Tanzu-Build-Service/1.5/vmware-tanzu-build-service/GUID-installing-no-kapp.html#installation-to-air-gapped-environment)

----

## <a id="relocate-images-to-registry"></a>Relocate images to a registry (air-gapped)

This procedure relocates images from VMware Tanzu Network registry to an internal container image registry that is available to the air-gapped environment through a local machine.
The local machine must have access to the air-gapped environment.

1. Set up environment variables for the installation:

    ```bash
    export TAS_ADAPTER_VERSION=VERSION-NUMBER
    ```

    Where `VERSION-NUMBER` is the version of Application Service Adapter you want to install. For example, `1.0.0`.

2. Log in to VMware Tanzu Network registry with your VMware Tanzu Network credentials:
   ```bash
    docker login registry.tanzu.vmware.com
   ```

3. Copy the Application Service Adapter bundle to a tarball with the [Carvel](https://carvel.dev/imgpkg/) `imgpkg` tool by running:
   ```bash
    imgpkg copy -b registry.tanzu.vmware.com/app-service-adapter/tas-adapter-package-repo:${TAS_ADAPTER_VERSION} --to-tar tas-adapter-package-repo.tar
   ```
4. Move the tarball file `tas-adapter-package-repo.tar` to the local machine that has access to the air-gapped environment.

5. Log in to the internal image registry from the local machine:

   ```bash
    docker login INTERNAL-REGISTRY
   ```

   Where `INTERNAL-REGISTRY` is the name of your internal image registry.

6. Unpackage the images from the tarball to the internal registry:

   ```bash
    imgpkg copy --tar tas-adapter-package-repo.tar --to-repo=INTERNAL-REGISTRY /tas-adapter-package-repo
   ```

## <a id="install-package-repo"></a>Install the package repository

After the images are relocated:

1. Verify that the `tap-install` namespace exists in your cluster.

    ```bash
    kubectl get ns tap-install
    ```

    The output lists the status of the `tap-install` namespace:

    ```bash
    NAME          STATUS   AGE
    tap-install   Active   2d
    ```

2. Create a registry secret to store your registry credentials in the `tap-install` namespace. These are required so that the Kubernetes cluster can pull images for the Application Service Adapter system from the internal registry.

    ```bash
    tanzu secret registry add internal-tas-adapter-registry \
      --username INTERNAL-REGISTRY-USERNAME \
      --password INTERNAL-REGISTRY-PASSWORD \
      --server INTERNAL-REGISTRY \
      --export-to-all-namespaces \
      --yes \
      --namespace tap-install
    ```

   Where `INTERNAL-REGISTRY-USERNAME` and `INTERNAL-REGISTRY-PASSWORD` are your credentials for `INTERNAL-REGISTRY`.

3. Add the Application Service Adapter package repository to the cluster.

    ```bash
    tanzu package repository add tas-adapter-repository \
      --url <INTERNAL-REGISTRY>/tas-adapter-package-repo:${TAS_ADAPTER_VERSION} \
      --namespace tap-install
    ```

4. Verify that the package repository contains the Application Service Adapter package.

    ```bash
    tanzu package available list \
      --namespace tap-install
    ```

   The output includes the Application Service Adapter package:

    ```bash
    NAME                                          DISPLAY-NAME                 SHORT-DESCRIPTION                                                   LATEST-VERSION
    ...
    application-service-adapter.tanzu.vmware.com  Application Service Adapter  Application Service Adapter for VMware Tanzu Application Platform  1.0.0
    ...
    ```

5. List the installation settings for the `application-service-adapter` package.

    ```bash
    tanzu package available get application-service-adapter.tanzu.vmware.com/${TAS_ADAPTER_VERSION} --values-schema --namespace tap-install
    ```

   It should output a list of settings similar to:

    ```
    | Retrieving package details for application-service-adapter.tanzu.vmware.com/1.0.0...
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
