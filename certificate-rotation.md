# Rotate Application Service Adapter certificates

You may need to manually rotate your Application Service Adapter system
certificates. This topic tells you how.

> **Note** Ingress certificate rotation does not result in downtime.

## <a id="rotating-ingress-certificates"></a>Rotate ingress certificates

When using the `shared.ingress_issuer` configuration option, ingress certificates are managed by Certificate Manager
and do not require manual rotation.
The default Certificate Manager configuration provides certificates that are valid for 90 days.
Certificates are renewed 30 days before expiry. For more information, see the
[cert-manager documentation](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.CertificateSpec).

When not using the `shared.ingress_issuer` configuration option, the platform operator must rotate the ingress certificate
data in the secrets used by Application Service Adapter.

1. Acquire new certificates for API and App Ingress, and store the PEM-encoded public certificate data of each
certificate in its own secret of type `kubernetes.io/tls`.

1. Set up environment variables for the installation:

    ```bash
    export TAS_ADAPTER_VERSION=VERSION-NUMBER
    ```

    Where `VERSION-NUMBER` is the version of Application Service Adapter you want to install. For example, `1.0.0`.

2. Update your `tas-adapter-values.yaml` file with the name and namespace of the secrets with the new
API and App Ingress TLS certificates.

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
       - `NEW-APP-TLS-SECRET-NAME` is the kubernetes.io/tls secret containing the PEM-encoded public certificate for applications deployed by using Application Service Adapter.
       - `NEW-APP-TLS-SECRET-NAMESPACE` is the namespace containing the Application Service Adapter applications secret.

1. Install Application Service Adapter to the cluster by running:

    ```bash
    tanzu package install tas-adapter \
      -p application-service-adapter.tanzu.vmware.com \
      --version "${TAS_ADAPTER_VERSION}" \
      --values-file tas-adapter-values.yaml \
      --namespace tap-install
    ```

2. Verify that the package install was successful. Run:

    ```bash
    tanzu package installed get tas-adapter \
      --namespace tap-install
    ```

   The following is an example output:

    ```bash
    | Retrieving installation details for tas-adapter...
    NAME:                    tas-adapter
    PACKAGE-NAME:            application-service-adapter.tanzu.vmware.com
    PACKAGE-VERSION:         1.0.0
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:
    ```

## <a id="rotating-issuer-certificates"></a> Rotate issuer certificate

When using Certificate Manager with a LetsEncrypt issuer, the certificate authority is managed by the LetsEncrypt
service itself and does not require manual intervention to the issuer.

When using Certificate Manager with a certificate authority issuer, rotation requires manual configuration of the
issuer's underlying secret with the new certificate data.

> **Note** Certificate Manager does not provide an interface for the best practice of introducing a new certificate authority before revoking an existing certificate authority. A configuration error of the certificate authority issuer may cause service downtime.

## <a id="rotating-internal-certificates"></a>Rotating internal certificates

Internal certificates are managed by Certificate Manager with a self-signed certificate authority. The default Certificate Manager configuration provides certificates that are valid for 90 days. Certificates are renewed 30 days before expiry. For more information, see the [cert-manager documentation](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.CertificateSpec).
