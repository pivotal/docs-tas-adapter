# Uninstalling Application Service Adapter

This topic describes how to uninstall the Application Service Adapter for VMware Tanzu Application Platform.

1. Uninstall the TAS adapter package from your cluster.

    ```bash
    tanzu package installed delete tas-adapter \
      --namespace tas-adapter-install
    ```

1. Remove the TAS adapter repostory from your cluster.

    ```bash
    tanzu package repository delete tas-adapter-repository \
      --namespace tas-adapter-install
    ```

1. Delete the image pull secret.

    ```bash
    tanzu secret registry delete tanzunet-registry \
      --namespace tas-adapter-install
    ```

1. Delete the `tas-adapter-install` namespace.

    ```bash
    kubectl delete ns tas-adapter-install
    ```
