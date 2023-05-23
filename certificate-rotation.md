# Rotate Application Service Adapter certificates

You may need to manually rotate your Application Service Adapter system
certificates. This topic tells you how.

> **Note** Certificate rotation does not result in downtime.

## <a id="rotating-ingress-certificates"></a>Rotate ingress certificates

1. Set up environment variables for the installation:

    ```bash
    export TAS_ADAPTER_VERSION=VERSION-NUMBER
    ```

    Where `VERSION-NUMBER` is the version of Application Service Adapter you want to install. For example, `1.0.0`.

2. Update your `tas-adapter-values.yaml` file with new API and App Ingress TLS certificates, such as crt and key.

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
    - `NEW-APP-TLS-SECRET-NAME` is the kubernetes.io/tls secret containing the PEM-encoded public certificate for applications deployed using Application Service Adapter.
    - `NEW-APP-TLS-SECRET-NAMESPACE` is the namespace containing the Application Service Adapter applications secret.

3. Install Application Service Adapter to the cluster by running:

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.tanzu.vmware.com \
      --version "${TAS_ADAPTER_VERSION}" \
      --values-file tas-adapter-values.yaml \
      --namespace tap-install
    ```

4. Verify that the package install was successful. Run:

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

## <a id="rotating-internal-certificates"></a>Rotating internal certificates

Internal certificates are managed by Certificate Manager with a self-signed Certificate Authority. The default Certificate Manager configuration provides certificates that are valid for 90 days. Certificates are renewed 30 days before expiry. For more information, see the [cert-manager documentation](https://cert-manager.io/docs/reference/api-docs/#cert-manager.io/v1.CertificateSpec).
