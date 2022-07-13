# Using a Registry With a Custom Certificate Authority

This topic describes how to configure the Application Service Adapter for use with a registry that has a custom or self-signed certificate authority.

* [Configuring Application Service Adapter](#configuring-application-service-adapter)

## <a id="configuring-application-service-adapter"></a>Configuring Application Service Adapter

1. Update your `tas-adapter-values.yml` file with a PEM encoded Certificate Authority certificate.

    The following value is inserted/updated:

    ```yaml
    app_registry_credentials:
      ca_cert_data: |
        PEM-ENCODED-CERTIFICATE-CONTENTS
    ```

    Where:

    - `PEM-ENCODED-CERTIFICATE-CONTENTS` is a PEM encoded multiline string containing the Certificate Authority certificate
      - The value must be inserted into your values file as a yaml multiline string with a block scalar literal.

    Note:  
    - __Your cluster nodes must trust the Certificate Authority that the Application Service Adapter is configured with.__
    - __Tanzu Build Service must also be configured to trust this registry Certificate Authority.__

2. Install the Application Service Adapter to the cluster by running:

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.tanzu.vmware.com \
      --version 0.8.0 \
      --values-file tas-adapter-values.yml \
      --namespace tas-adapter-install
    ```
