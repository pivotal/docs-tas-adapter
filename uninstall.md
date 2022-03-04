# Uninstalling Application Service Adapter

This topic describes how to uninstall the Application Service Adapter for VMware Tanzu Application Platform.

To uninstall Application Service Adapter:

1. Delete all Cloud Foundry orgs and spaces from your cluster.

    ```bash
    cf delete-space SPACE-NAME
    cf delete-org ORG-NAME
    ```

1. Uninstall the Application Service Adapter package from your cluster.

    ```bash
    tanzu package installed delete tas-adapter \
      --namespace tas-adapter-install
    ```

1. Remove the repository from your cluster.

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
