# Installing Application Service Adapter

This topic describes how to install the Application Service Adapter for VMware Tanzu Application Platform system:

* [Install the package repository](#install-package-repo)
* [Configure the installation settings](#configure-installation-settings)
* [Install Application Service Adapter](#install-adapter)
* [Configure DNS for the Application Service Adapter](#configure-dns)
* [Assign the admin role to a user](#assign-admin-user)

----

After you have completed the steps in [Installing Prerequisites](install-prerequisites.md), set the Kubernetes context to the cluster where you have installed kpack and Contour.

## <a id="install-package-repo"></a>Install the package repository

To install Application Service Adapter:

1. Create a namespace called `tas-adapter-install` for deploying Application Service Adapter to your cluster.

    ```bash
    kubectl create ns tas-adapter-install
    ```

1. Create an image pull secret to store your Tanzu Network credentials. These are required so that the cluster can pull images from the Tanzu Network registry.

    ```bash
    tanzu secret registry add tanzunet-registry \
      --username "TANZU-NET-USER" --password "TANZU-NET-PASSWORD" \
      --server registry.tanzu.vmware.com \
      --export-to-all-namespaces --yes \
      --namespace tas-adapter-install
    ```

   Where `TANZU-NET-USER` and `TANZU-NET-PASSWORD` are your credentials for Tanzu Network.

1. Add the Application Service Adapter package repository to the cluster.

    ```bash
    tanzu package repository add tas-adapter-repository \
      --url registry.tanzu.vmware.com/app-service-adapter/tas-adapter-package-repo:0.10.0 \
      --namespace tas-adapter-install
    ```
1. Verify that the package repository contains the Application Service Adapter package.

    ```bash
    tanzu package available list \
      --namespace tas-adapter-install
    ```

   The output includes the Application Service Adapter package:

    ```bash
    NAME                                          DISPLAY-NAME                 SHORT-DESCRIPTION                                                   LATEST-VERSION
    ...
    application-service-adapter.tanzu.vmware.com  Application Service Adapter  Application Service Adapter for VMware Tanzu® Application Platform  0.10.0
    ...
    ```

1. List the installation settings for the `application-service-adapter` package.

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
      api_ingress.tls.secret_name          string   Name of the secret containing the TLS certificate for the CF API (PEM format)
      api_ingress.tls.namespace            string   Namespace containing the CF API TLS secret
      app_ingress.default_domain           string   Default application domain
      app_ingress.tls.secret_name          string   Name of the secret containing the TLS certificate for the default application domain (PEM format)
      app_ingress.tls.namespace            string   Namespace containing the default application domain TLS secret
      app_registry.path.droplets           string   Container registry repository where staged, runnable app images (Droplets) will be stored
      app_registry.path.packages           string   Container registry repository where uploaded app source code (Packages) will be stored
      kpack_clusterbuilder_name  default  string   Name of the kpack cluster builder to use for staging
      ...
    ```

## <a id="configure-installation-settings"></a>Configure the installation settings

To configure the installation settings:

1. If you do not already have a secret containing a certificate and private keypair for HTTPS ingress to the Application Service Adapter API:
   * If you have a certificate and private keypair, create a secret.

      ```bash
      kubectl create namespace API-TLS-SECRET-NAMESPACE
      kubectl delete secret API-TLS-SECRET-NAME -n API-TLS-SECRET-NAMESPACE
      kubectl create secret tls API-TLS-SECRET-NAME \
        --cert=tls.crt \
        --key=tls.key \
        -n API-TLS-SECRET-NAMESPACE
     ```

   * If you do not have a certificate and private keypair, you can [generate a self-signed secret using cert-manager](https://cert-manager.io/docs/usage/certificate/#creating-certificate-resources).

   * Alternatively, you can generate a self-signed certificate manually.
     * If you are using `openssl`, or `libressl v3.1.0` or later:

        ```bash
        openssl req -x509 -newkey rsa:4096 \
          -keyout tls.key -out tls.crt \
          -nodes -subj '/CN=API-FQDN' \
          -addext "subjectAltName = DNS:API-FQDN" \
          -days 365
        ```
        Where `API-FQDN` is the fully qualified domain name (FQDN) to use to access the API.

     * If you are using version `libressl` v3.1.0 or earlier:

        ```bash
        openssl req -x509 -newkey rsa:4096 \
          -keyout tls.key -out tls.crt \
          -nodes -subj '/CN=API-FQDN' \
          -extensions SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[ SAN ]\nsubjectAltName='DNS:API-FQDN'")) \
          -days 365
        ```

     * > **Note:** `libressl` v3.1.0 is the default version in macOS.

     * Create a secret.

       ```bash
       kubectl create namespace API-TLS-SECRET-NAMESPACE
       kubectl delete secret API-TLS-SECRET-NAME -n API-TLS-SECRET-NAMESPACE
       kubectl create secret tls API-TLS-SECRET-NAME \
         --cert=tls.crt \
         --key=tls.key \
         -n API-TLS-SECRET-NAMESPACE
       ```

2. If you do not already have a secret containing a wildcard certificate and private keypair for HTTPS application ingress:
   * If you have a wildcard certificate and private keypair, create a secret.

      ```bash
      kubectl create namespace APP-TLS-SECRET-NAMESPACE
      kubectl delete secret APP-TLS-SECRET-NAME -n APP-TLS-SECRET-NAMESPACE
      kubectl create secret tls APP-TLS-SECRET-NAME \
        --cert=tls.crt \
        --key=tls.key \
        -n APP-TLS-SECRET-NAMESPACE
     ```

   * If you do not have a wildcard certificate and private keypair, you can [generate a self-signed secret using cert-manager](https://cert-manager.io/docs/usage/certificate/#creating-certificate-resources).

   * Alternatively, you can generate a self-signed certificate manually.
      * If you are using `openssl`, or `libressl v3.1.0` or later:

         ```bash
         openssl req -x509 -newkey rsa:4096 \
           -keyout tls.key -out tls.crt \
           -nodes -subj '/CN=*.APP-DOMAIN' \
           -addext "subjectAltName = DNS:*.APP-DOMAIN" \
           -days 365
         ```
         Where `APP-DOMAIN` is the FQDN of the shared domain to use for application routes. By default, each application is mapped to a route on a subdomain of this shared domain.

      * If you are using version `libressl` v3.1.0 or earlier:

        ```bash
        openssl req -x509 -newkey rsa:4096 \
          -keyout tls.key -out tls.crt \
          -nodes -subj '/CN=*.APP-DOMAIN' \
          -extensions SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[ SAN ]\nsubjectAltName='DNS:*.APP-DOMAIN'")) \
          -days 365
        ```

      * > **Note:** The TLS certificate for application ingress must be a wildcard certificate.
      * > **Note:** `libressl` v3.1.0 is the default version in macOS.

      * Create a secret.

      ```bash
      kubectl create namespace APP-TLS-SECRET-NAMESPACE
      kubectl delete secret APP-TLS-SECRET-NAME -n APP-TLS-SECRET-NAMESPACE
      kubectl create secret tls APP-TLS-SECRET-NAME \
        --cert=tls.crt \
        --key=tls.key \
        -n APP-TLS-SECRET-NAMESPACE
      ```

3. If you do not already have a secret containing the hostname, username and password for your application image registry:
   * Create a secret.

      ```bash
      kubectl create namespace APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE
      kubectl delete secret APP-REGISTRY-CREDENTIALS-SECRET-NAME -n APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE
      kubectl create secret docker-registry APP-REGISTRY-CREDENTIALS-SECRET-NAME \
        --docker-server=APP-REGISTRY-HOSTNAME \
        --docker-username=APP-REGISTRY-USERNAME \
        --docker-password=$(cat /path/to/APP-REGISTRY-PASSWORD) \
        -n APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE
      ```

4. Create a `tas-adapter-values.yml` file with the desired installation settings, following the schema specified for the package.

   The following values are required:

    ```yaml
    ---
    ceip_policy_disclosed: FALSE-OR-TRUE-VALUE # Installation fails if this is not set to true. Not a string.
    api_ingress:
      fqdn: "API-FQDN"
      tls:
        secret_name: API-TLS-SECRET-NAME
        namespace: API-TLS-SECRET-NAMESPACE
    app_ingress:
      default_domain: "DEFAULT-APP-DOMAIN"
      tls:
        secret_name: APP-TLS-SECRET-NAME
        namespace: APP-TLS-SECRET-NAMESPACE
    app_registry:
      credentials:
        secret_name: "APP-REGISTRY-CREDENTIALS-SECRET-NAME"
        namespace: "APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE"
      hostname: "APP-REGISTRY-HOSTNAME"
      path:
        droplets: "APP-REGISTRY-PATH-DROPLETS"
        packages: "APP-REGISTRY-PATH-PACKAGES"
    ```

   Where:

   - `API-FQDN` is the FQDN that you want to use for the Application Service Adapter API.
   - `API-TLS-SECRET-NAME` is the `kubernetes.io/tls` secret containing the PEM-encoded public certificate for the Application Service Adapter API.
   - `API-TLS-SECRET-NAMESPACE` is the namespace containing the API TLS secret.
   - `DEFAULT-APP-DOMAIN` is the domain that you want to use for automatically configured application routes.
   - `APP-TLS-SECRET-NAME` is the `kubernetes.io/tls` secret containing the PEM-encoded public certificate for applications deployed using the Application Service Adapter.
   - `APP-TLS-SECRET-NAMESPACE` is the namespace containing the application TLS secret.
   - `APP-REGISTRY-CREDENTIALS-SECRET-NAME` is the `kubernetes.io/dockerconfigjson` secret containing the hostname, username and password for the application image registry.
   - `APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE` is the namespace containing the application image registry secret.
   - `APP-REGISTRY-HOSTNAME` is the hostname of the registry to be used for app packages and droplets. This value should be the same as the server name in a `dockerconfigjson` Kubernetes secret. Examples:
     - Harbor has the form `hostname: "my-harbor.io"`
     - Docker Hub has the form `hostname: "https://index.docker.io/v1"`
     - Google Cloud Registry has the form `hostname: "gcr.io"`
  - `APP-REGISTRY-PATH-DROPLETS` is the path to the directory or project in the app registry where Application Service Adapter uploads droplets, such as runnable application images. This value should not include the registry hostname itself. Examples:
    - Harbor has the form `droplets: "project-name/my-repo-name"`
    - Docker Hub has the form `droplets: "my-dockerhub-username"`
    - Google Cloud Registry has the form `droplets: "project-id/my-repo-name"`
  - `APP-REGISTRY-PATH-PACKAGES` is the is the path to the directory or project in the app registry where Application Service Adapter uploads packages, such as application source code. This value should not include the registry hostname itself. Examples:
    - Harbor has the form `packages: "project-name/my-repo-name"`
    - Docker Hub has the form `packages: "my-dockerhub-username"`
    - Google Cloud Registry has the form `packages: "project-id/my-repo-name"`

  See optional values in the following example. For more information, see the Tanzu CLI output.

   ```yaml
   ---
   api_auth_proxy:
     ca_cert:
       data: |
         API-AUTH-PROXY-TLS-CRT
     host: "API-AUTH-PROXY-FQDN"
   app_registry:
     ca_cert:
       data: |
         PEM-ENCODED-CERTIFICATE-CONTENTS
   experimental_use_cartographer: FALSE-OR-TRUE-VALUE
   kpack_clusterbuilder_name: "KPACK-CLUSTER-BUILDER-NAME"
   scaling:
     korifi_api:
       limits:
         cpu: "API-CPU-LIMIT"
         memory: "API-MEMORY-LIMIT"
       requests:
         cpu: "API-CPU-REQUEST"
         memory: "API-MEMORY-REQUEST"
       replicas: API-REPLICA-COUNT
     korifi_controllers:
       ... #! scaling keys are the same as above
     korifi_job_task_runner:
       ... #! scaling keys are the same as above
     korifi_kpack_image_builder:
       ... #! scaling keys are the same as above
     korifi_statefulset_runner:
       ... #! scaling keys are the same as above
     cartographer_builder_runner:
       ... #! scaling keys are the same as above
     telemetry_informer:
       ... #! scaling keys are the same as above
   telemetry:
     heartbeat_interval: TELEMETRY-HEARTBEAT-INTERVAL
   user_certificate_expiration_warning_duration: "USER-CERT-EXPIRY-WARNING-DURATION"
   ```

  Where:

  - `API-AUTH-PROXY-TLS-CRT` is the CA certificate from the authentication proxy running along side your Kubernetes cluster.
  - `API-AUTH-PROXY-FQDN` is the FQDN for the authentication proxy running along side your Kubernetes cluster.
  - `PEM-ENCODED-CERTIFICATE-CONTENTS` is a PEM encoded multiline string containing the Certificate Authority certificate
      - The value must be inserted into your values file as a yaml multiline string with a block scalar literal.
  - `KPACK-CLUSTER-BUILDER-NAME` is the name of the kpack cluster builder to use for staging. Tanzu Build Service provides two cluster builders named `base` and `default`. To create your own builder, see [Managing Builders](https://docs.vmware.com/en/Tanzu-Build-Service/1.3/vmware-tanzu-build-service-v13/GUID-managing-builders.html) in the Tanzu Build Service documentation, and update this setting with the corresponding builder name.
  - `USER-CERT-EXPIRY-WARNING-DURATION` is the recommended duration beyond which user are warned to use short-lived certificates for authentication. Default is 168 hours.
  - `API-CPU-LIMIT` is the desired CPU resource limit for the pods in the specified deployment. Default is 1 cpu.
  - `API-MEMORY-LIMIT` is the desired memory resource limit for the pods in the specified deployment. Default is 1000Mi.
  - `API-CPU-REQUEST` is the desired CPU resource request for the pods in the specified deployment. Default is 50m.
  - `API-MEMORY-REQUEST` is the desired memory resource request for the pods in the specified deployment. Default is 100Mi.
  - `API-REPLICA-COUNT` is the desired number of replicas for the specified deployment. Default is 1.
  - `TELEMETRY-HEARTBEAT-INTERVAL` is how often telemetry data is sent to VMware. Default is every 24 hours.

  The `requests` and `limits` fields map directly to the resource requests and limits fields on the Kubernetes containers for these system components.
  For more information, see [Resource requests and limits of Pod and container](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-requests-and-limits-of-pod-and-container) in the Kubernetes documentation.


### <a id="opt-out-telemetry"></a>Opting out of telemetry reporting

By default, when you install Application Service Adapter, you opt into telemetry collection. To turn off telemetry collection, complete the following instructions:

1. Ensure your Kubernetes context is pointing to the cluster where Application Service Adapter is installed.

1. Run the following kubectl command:

  ```yaml
  kubectl apply -f - <<EOF
  apiVersion: v1
  kind: Namespace
  metadata:
    name: vmware-system-telemetry
  ---
  apiVersion: v1
  kind: ConfigMap
  metadata:
    namespace: vmware-system-telemetry
    name: vmware-telemetry-cluster-ceip
  data:
    level: disabled
  EOF
  ```

Your Application Service Adapter deployment no longer emits telemetry, and you are opted out of the VMware Customer Experience Improvement Program.

### <a id="custom-ca-registry"></a>Configure a Registry With a Custom Certificate Authority

To configure Application Service Adapter with a registry that has a custom or self-signed certificate authority:

1. Set the value of the `app_registry_credentials.ca_cert_data` property in the `tas-adapter-values.yml` file with a PEM encoded Certificate Authority certificate.

> **Note:** Your cluster nodes must trust the Certificate Authority that the Application Service Adapter is configured with. Tanzu Build Service must be configured to trust this registry Certificate Authority certificate.

## <a id="experimental-cartographer-integration"></a>(Optional) Configure the Experimental Cartographer Integration

> **Note**: Opting into the experimental Cartographer integration requires a larger set of Tanzu Application Platform packages to be installed. See [Required components for experimental Cartographer integration](install-prerequisites.md#required-components-cartographer) in _Install Prerequisites_.

To configure the experimental Cartographer integration:

1. Set the value of the `experimental_use_cartographer` property in the `tas-adapter-values.yml` file to `true`. Note that this value is a boolean and not a string.

## <a id="install-adapter"></a>Install Application Service Adapter

To install Application Service Adapter:

1. Install Application Service Adapter to the cluster.

    ```bash
    tanzu package install tas-adapter \
      --package-name application-service-adapter.tanzu.vmware.com \
      --version 0.10.0 \
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
    PACKAGE-VERSION:         0.10.0
    STATUS:                  Reconcile succeeded
    CONDITIONS:              [{ReconcileSucceeded True  }]
    USEFUL-ERROR-MESSAGE:
    ```

## <a id="configure-dns"></a>Configure DNS for Application Service Adapter

To configure DNS for Application Service Adapter:

1. Determine the external IP address to be used for ingress to your cluster. This step varies depending on the IaaS used to provision your cluster.

   For clusters that support LoadBalancer services, you can obtain the external IP address of the LoadBalancer Service that is associated with Contour's Envoy proxy. The Namespace for this service is typically either `tanzu-system-ingress` or `projectcontour` depending on how Contour was installed.

    ```bash
    kubectl -n tanzu-system-ingress get service envoy -ojsonpath='{.status.loadBalancer.ingress[*].ip}'
    ```

   > **Note:** If you are using a cluster deployed on AWS, your LoadBalancer has a DNS name instead of an IP address.

1. Create an A record in your DNS zone that resolves the configured API FQDN to the external IP address from step 1. This step varies depending on your DNS provider.

   > **Note:** If you are using a cluster deployed on AWS, create a CNAME record that resolves to the DNS name of the load balancer instead of an A record.

1. Create a wildcard A record in your DNS zone that resolves all sub-domains of the configured application domain to the external IP address from step 1. This step varies depending on your DNS provider.

1. Verify that the Contour HTTPProxy for the API endpoint is valid.

    ```bash
    kubectl -n korifi-api-system get httpproxy korifi-api-proxy
    ```

   The output looks like the following:
    ```bash
    NAME               FQDN       TLS SECRET                STATUS   STATUS DESCRIPTION
    korifi-api-proxy   API-FQDN   korifi-api-ingress-cert   valid    Valid HTTPProxy
    ```

## <a id="assign-admin-user"></a>Assign the admin role to a user

After you install the Cloud Foundry command-line interface (cf CLI), log in to Application Service Adapter and assign the admin role to an existing user in the Kubernetes cluster:

1. Target the cf CLI at the API endpoint.

    ```bash
    cf api API-FQDN --skip-ssl-validation
    ```

    Where `API-FQDN` is the fully qualified domain name (FQDN) for the Application Service Adapter API.

    >**Note:** If you configured the Application Service Adapter with a globally trusted certificate during installation, you can omit the `--skip-ssl-validation` flag.

1. Log in with the cf CLI.

    ```bash
    cf login
    ```

    The cf CLI detects the user authentication entries in your local Kubeconfig file and presents them for you to select one interactively. Select a user on your target cluster whom you want to act as an admin.

1. Use the `cf curl` command to verify the subject name of the logged-in user.

    ```bash
    cf curl /whoami
    ```

    The output looks like the following:

    ```
    {"name":"my_user@example.com","kind":"User"}
    ```

    The value of the `name` field in the response is the subject name of the user.

    >**Note:** The `kind` field in the output must have the value `User`. If it is some other value, such as `ServiceAccount`, log in to the Application Service Adapter with an account for a user in the Kubernetes cluster.


1. Create a `tas-adapter-admin.yaml` file with a RoleBinding definition for the admin user:

    ```yaml
    ---
    apiVersion: rbac.authorization.k8s.io/v1
    kind: RoleBinding
    metadata:
      name: cf-admin
      namespace: cf
      annotations:
        cloudfoundry.org/propagate-cf-role: "true"
    subjects:
    - kind: User
      name: CF-ADMIN-USERNAME
      apiGroup: rbac.authorization.k8s.io
    roleRef:
      kind: ClusterRole
      name: korifi-controllers-admin
      apiGroup: rbac.authorization.k8s.io
    ```

    Where `CF-ADMIN-USERNAME` is the user name you verify earlier.

1. Create the admin RoleBinding in the target cluster.

    ```bash
    kubectl apply -f tas-adapter-admin.yaml
    ```


   To test Application Service Adapter, continue to [Getting Started](getting-started.md).
