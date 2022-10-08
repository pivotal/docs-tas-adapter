# Uninstalling Application Service Adapter

This topic describes how to uninstall the Application Service Adapter for VMware Tanzu Application Platform.

To uninstall Application Service Adapter:

1. Uninstall the Application Service Adapter package from your cluster.

    ```bash
    tanzu package installed delete tas-adapter \
      --namespace tap-install
    ```

1. Remove the repository from your cluster.

    ```bash
    tanzu package repository delete tas-adapter-repository \
      --namespace tap-install
    ```

1. Delete the image pull secret.

    ```bash
    tanzu secret registry delete tanzunet-tas-adapter-registry \
      --namespace tap-install
    ```
