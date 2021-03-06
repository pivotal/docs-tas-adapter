# Rotating certificates

This topic describes how to rotate Application Service Adapter for VMware Tanzu Application Platform system certificates, and includes the following sections:

* [Rotating Ingress Certificates](#rotating-ingress-certificates)
* [Rotating Internal Certificates](#rotating-internal-certificates)

> **Note:** Certificate rotation should not result in downtime.

## <a id="rotating-ingress-certificates"></a>Rotating ingress certificates

1. Update your `tas-adapter-values.yml` file with new API and App Ingress TLS certificates (crt and key).

    The following values are updated:

    ```yaml
    api_ingress:
      tls:
        secret_name: NEW-API-TLS-SECRET-NAME
        namespace: NEW-API-TLS-SECRET-NAMESPACE
    app_ingress:
      tls:
        secret_name: NEW-APP-TLS-SECRET-NAME
        namespace: NEW-APP-TLS-SECRET-NAMESPACE
    ```

    Where:

    - `NEW-API-TLS-SECRET-NAME` is the kubernetes.io/tls secret containing the PEM-encoded public certificate for the Application Service Adapter API.
    - `NEW-API-TLS-SECRET-NAMESPACE` is namespace containing the Application Service Adapter API secret.
    - `NEW-APP-TLS-SECRET-NAME` is the kubernetes.io/tls secret containing the PEM-encoded public certificate for applications deployed using the Application Service Adapter.
    - `NEW-APP-TLS-SECRET-NAMESPACE` is the namespace containing the Application Service Adapter applications secret.

1. Install the Application Service Adapter to the cluster by running:

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.tanzu.vmware.com \
      --version 0.8.0 \
      --values-file tas-adapter-values.yml \
      --namespace tas-adapter-install
    ```

1. Verify that the package install was successful. Run:

    ```bash
    tanzu package installed get tas-adapter \
      --namespace tas-adapter-install
    ```

   The output is similar to the following:

    ```bash
    | Retrieving installation details for tas-adapter...
    NAME:                    tas-adapter
    PACKAGE-NAME:            application-service-adapter.tanzu.vmware.com
    PACKAGE-VERSION:         0.8.0
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:
    ```

## <a id="rotating-internal-certificates"></a>Rotating internal certificates

Internal certificates are managed by Certificate Manager with a self-signed Certificate Authority. The default [Certificate Manager](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.CertificateSpec) configuration provides certificates that are valid for 90 days. Certificates are renewed 30 days before expiry.
