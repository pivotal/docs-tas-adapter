# Rotating Certificates

This topic describes how to rotate Application Service Adapter for VMware Tanzu Application Platform system certificates:

* [Rotating Ingress Certificates](#rotating-ingress-certificates)
* [Rotating Internal Certificates](#rotating-internal-certificates)

---

**Certificate rotation should not result in downtime.**

## <a id="rotating-ingress-certificates"></a>Rotating Ingress Certificates

1. Update your `tas-adapter-values.yml` file with new API and App Ingress TLS certificates (crt and key).

    The following values will be updated:

    ```yaml
    api_ingress:
    tls:
        crt: |
          NEW-API-TLS-CRT
        key: |
          NEW-API-TLS-KEY
    app_ingress:
    tls:
        crt: |
          NEW-APP-TLS-CRT
        key: |
          NEW-APP-TLS-KEY
    ```

    Where:

    - `NEW-API-TLS-CRT` is the PEM-encoded public certificate for the Application Service Adapter API.
    - `NEW-API-TLS-KEY` is the PEM-encoded private key for the Application Service Adapter API.
    - `NEW-APP-TLS-CRT` is the PEM-encoded public certificate for applications deployed using the Application Service Adapter.
    - `NEW-APP-TLS-KEY` is the PEM-encoded private key for applications deployed using the Application Service Adapter.

1. Install the Application Service Adapter to the cluster.

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.tanzu.vmware.com \
      --version 0.6.1 \
      --values-file tas-adapter-values.yml \
      --namespace tas-adapter-install
    ```

1. Verify that the package install was successful.

    ```bash
    tanzu package installed get tas-adapter \
      --namespace tas-adapter-install
    ```

   The output looks like the following:

    ```bash
    | Retrieving installation details for tas-adapter...
    NAME:                    tas-adapter
    PACKAGE-NAME:            application-service-adapter.tanzu.vmware.com
    PACKAGE-VERSION:         0.6.1
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:
    ```

## <a id="rotating-internal-certificates"></a>Rotating Internal Certificates

Internal certificates are managed by Certificate Manager with a self-signed Certificate Authority. The default [Certificate Manager](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.CertificateSpec) configuration provides certificates that are valid for 90 days. Certificates will be renewed 30 days before expiry.
