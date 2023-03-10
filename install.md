# Install Application Service Adapter

This topic describes how to install Application Service Adapter for VMware Tanzu Application Platform.

After you complete the steps in [Installing Prerequisites](install-prerequisites.md), set the Kubernetes context to the cluster where you installed kpack and Contour.

## <a id="add-package-repo"></a>Add the Application Service Adapter package repository

Adding the Application Service Adapter package repository makes Application Service Adapter available for installation.

To add the Application Service Adapter package repository to your cluster:

1. Set up environment variables for the installation:

   ```bash
   export TAS_ADAPTER_VERSION=VERSION-NUMBER
   ```

   Where `VERSION-NUMBER` is the version of Application Service Adapter you want to install. For example, `1.1.0`.

1. Verify that the `tap-install` namespace exists in your cluster.

   ```bash
   kubectl get ns tap-install
   ```

   The output lists the status of the `tap-install` namespace:

   ```bash
   NAME          STATUS   AGE
   tap-install   Active   2d
   ```

2. Create a registry secret to store your VMware Tanzu Network credentials in the `tap-install` namespace. These are required so that the Kubernetes cluster can pull images for the Application Service Adapter system components from the VMware Tanzu Network registry.

   ```bash
   tanzu secret registry add tanzunet-tas-adapter-registry \
     --username "TANZU-NET-USERNAME" \
     --password "TANZU-NET-PASSWORD" \
     --server registry.tanzu.vmware.com \
     --export-to-all-namespaces \
     --yes \
     --namespace tap-install
   ```

   Where:

   - `TANZU-NET-USERNAME` is your user name on VMware Tanzu Network.
   - `TANZU-NET-PASSWORD` the password for your user name on VMware Tanzu Network.

3. Add the Application Service Adapter package repository to the cluster.

   ```bash
   tanzu package repository add tas-adapter-repository \
     --url registry.tanzu.vmware.com/app-service-adapter/tas-adapter-package-repo:${TAS_ADAPTER_VERSION} \
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
   application-service-adapter.tanzu.vmware.com  Application Service Adapter  Application Service Adapter for VMware Tanzu® Application Platform  1.1.0
   ...
   ```

5. List the installation settings for the `application-service-adapter` package.

   ```bash
   tanzu package available get application-service-adapter.tanzu.vmware.com/${TAS_ADAPTER_VERSION} --values-schema --namespace tap-install
   ```

   It should output a list of settings similar to:

   ```bash
   | Retrieving package details for application-service-adapter.tanzu.vmware.com/1.1.0...
     KEY                         DEFAULT  TYPE     DESCRIPTION
     api_auth_proxy.ca_cert.data          string   TLS CA certificate of your cluster's auth proxy
     api_auth_proxy.host                  string   FQDN of your cluster's auth proxy
     api_ingress.fqdn                     string   FQDN used to access the Application Service Adapter API
     api_ingress.tls.secret_name          string   Name of the secret containing the TLS certificate for the Application Service Adapter API (PEM format)
     api_ingress.tls.namespace            string   Namespace containing the Application Service Adapter API TLS secret
     app_ingress.default_domain           string   Default application domain
     app_ingress.tls.secret_name          string   Name of the secret containing the TLS certificate for the default application domain (PEM format)
     app_ingress.tls.namespace            string   Namespace containing the default application domain TLS secret
     app_registry.path.droplets           string   Container registry repository where staged, runnable app images (droplets) will be stored
     app_registry.path.packages           string   Container registry repository where uploaded app source code (packages) will be stored
     kpack_clusterbuilder_name   default  string   Name of the kpack cluster builder to use for staging
     ...
   ```

## <a id="configure-installation-settings"></a>Configure the installation settings

To configure the installation settings:

1. If you do not already have a secret containing a certificate and private key pair for HTTPS ingress to the Application Service Adapter API:

   > **Note** You do not need to create secrets containing certificates for HTTPS ingress for either the Application Service Adapter API or application workloads if you intend to use the shared certificate issuer from Tanzu Application Platform.

   * If you have a certificate and private key pair, create a secret containing them:

      ```bash
      kubectl create namespace API-TLS-SECRET-NAMESPACE
      kubectl create secret tls API-TLS-SECRET-NAME \
        --cert=tls.crt \
        --key=tls.key \
        --namespace API-TLS-SECRET-NAMESPACE
     ```

   * If you do not have a certificate and private key pair, you can [use cert-manager to generate a secret containing a self-signed certificate](https://cert-manager.io/docs/usage/certificate/#creating-certificate-resources) in the cert-manager documentation:

      ```bash
      kubectl apply -f - <<EOF
      ---
      apiVersion: cert-manager.io/v1
      kind: Issuer
      metadata:
        name: selfsigned-issuer
        namespace: API-TLS-SECRET-NAMESPACE
      spec:
        selfSigned: {}
      ---
      apiVersion: cert-manager.io/v1
      kind: Certificate
      metadata:
        name: api-selfsigned-certificate
        namespace: API-TLS-SECRET-NAMESPACE
      spec:
        commonName: API-FQDN
        dnsNames:
          - API-FQDN
        issuerRef:
          name: selfsigned-issuer
        privateKey:
          algorithm: RSA
        secretName: API-TLS-SECRET-NAME
        usages:
        - server auth
        - client auth
      EOF
      ```

2. If you do not already have a secret containing a wildcard certificate and private key pair for HTTPS application ingress:

   * If you have a wildcard certificate and private key pair, create a secret containing them:

      ```bash
      kubectl create namespace APP-TLS-SECRET-NAMESPACE
      kubectl create secret tls APP-TLS-SECRET-NAME \
        --cert=tls.crt \
        --key=tls.key \
        --namespace APP-TLS-SECRET-NAMESPACE
     ```

   * If you do not have a wildcard certificate and private key pair, you can [use cert-manager to generate a Secret containing a self-signed wildcard certificate](https://cert-manager.io/docs/usage/certificate/#creating-certificate-resources) in the cert-manager documentation:

      ```bash
      kubectl apply -f - <<EOF
      ---
      apiVersion: cert-manager.io/v1
      kind: Issuer
      metadata:
        name: selfsigned-issuer
        namespace: APP-TLS-SECRET-NAMESPACE
      spec:
        selfSigned: {}
      ---
      apiVersion: cert-manager.io/v1
      kind: Certificate
      metadata:
        name: app-domain-selfsigned-certificate
        namespace: APP-TLS-SECRET-NAMESPACE
      spec:
        commonName: *.DEFAULT-APP-DOMAIN
        dnsNames:
          - *.DEFAULT-APP-DOMAIN
        issuerRef:
          name: selfsigned-issuer
        privateKey:
          algorithm: RSA
        secretName: APP-TLS-SECRET-NAME
        usages:
        - server auth
        - client auth
      EOF
      ```

3. If you do not already have a secret containing the host name, user name, and password for your application image registry, create one:

> **Note** The app registry secret and secret export are not required when using Amazon Elastic Container Registry (ECR).

   ```bash
   kubectl create namespace APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE
   kubectl create secret docker-registry APP-REGISTRY-CREDENTIALS-SECRET-NAME \
     --docker-server=APP-REGISTRY-SERVER \
     --docker-username=APP-REGISTRY-USERNAME \
     --docker-password=$(cat /path/to/APP-REGISTRY-PASSWORD) \
     --namespace APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE
   ```

   Where:

   - `APP-REGISTRY-SERVER` is the address of the registry used for app packages and droplets. This value is the same as the server name in a `dockerconfigjson` Kubernetes secret. For example:
     - Harbor has the form `my-harbor.io`.
     - Docker Hub the form `https://index.docker.io/v1/`.
     - Google Container Registry has the form `gcr.io`.

4. Create a `SecretExport` to allow Application Service Adapter to copy the application image registry credentials secret into the `cf` namespace.

   ```bash
   kubectl apply -f - <<EOF
   ---
   apiVersion: secretgen.carvel.dev/v1alpha1
   kind: SecretExport
   metadata:
     name: APP-REGISTRY-CREDENTIALS-SECRET-NAME
     namespace: APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE
   spec:
     toNamespace: cf
   EOF
   ```

5. Create a `tas-adapter-values.yml` file with the installation settings that you want, following the schema specified for the package.

   The following values are required:

   ```yaml
   ceip_policy_disclosed: FALSE-OR-TRUE-VALUE # Installation fails if this is not set to the boolean value true. Not a string.
   api_ingress:
     fqdn: "API-FQDN"
   app_ingress:
     default_domain: "DEFAULT-APP-DOMAIN"
   app_registry:
     credentials:
       secret_name: "APP-REGISTRY-CREDENTIALS-SECRET-NAME"
       namespace: "APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE"
       aws_iam_role_arn: "AWS-IAM-ROLE-ARN"
     repository_prefix: "REPOSITORY-PREFIX"
   ```

   Where:

   - `API-FQDN` is the fully qualified domain name (FQDN) that you want to use for the Application Service Adapter API. Example: `api.example.com`
   - `DEFAULT-APP-DOMAIN` is the domain that you want to use for automatically configured application routes. Example: `apps.example.com`.
   - `APP-REGISTRY-CREDENTIALS-SECRET-NAME` is the `kubernetes.io/dockerconfigjson` secret containing the host, user name, and password for the application image registry.
       - Not required if you intend to use ECR as the application image registry.
   - `APP-REGISTRY-CREDENTIALS-SECRET-NAMESPACE` is the namespace containing the application image registry secret.
       - Not required if you intend to use ECR as the application image registry.
   - `AWS-IAM-ROLE-ARN` is the Amazon Resource Name (ARN) of an AWS IAM role that can be used to access Elastic Container Registries (ECR). Refer to additional
Application Service Adapter requirements in [AWS IAM Configuration for ECR](install-prerequisites.md#ecr-configuration).
       - Required if you intend to use ECR as the application image registry. Do not set the `app_registry.credentials.secret_name` and `app_registry.credentials.namespace` fields when this field is set.
   - `REPOSITORY-PREFIX` is the host and path combination used as the base for package and droplet images produced by Application Service Adapter.

Repository Prefix Examples:

| Registry  | containerRepositoryPrefix                                         | Resultant Image Ref                                                                 | Notes                                                                                                        |
|-----------|-------------------------------------------------------------------|-------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| ACR       | `<projectID>.azurecr.io/foo/bar/tas-adapter-`                     | `<projectID>.azurecr.io/foo/bar/tas-adapter-<appGUID>-packages`                     | Repositories are created dynamically during push by ACR                                                      |
| DockerHub | `index.docker.io/<dockerOrganisation>/`                           | `index.docker.io/<dockerOrganisation>/<appGUID>-packages`                           | Docker does not support nested repositories                                                                  |
| ECR       | `<projectID>.dkr.ecr.<region>.amazonaws.com/foo/bar/tas-adapter-` | `<projectID>.dkr.ecr.<region>.amazonaws.com/foo/bar/tas-adapter-<appGUID>-packages` | Application Service Adapter creates the repository before pushing, as dynamic repository creation is not possible on ECR |
| GAR       | `<region>-docker.pkg.dev/<projectID>/foo/bar/tas-adapter-`        | `<region>-docker.pkg.dev/<projectID>/foo/bar/tas-adapter-<appGUID>-packages`        | The `foo` repository must already exist in GAR                                                               |
| GCR       | `gcr.io/<projectID>/foo/bar/tas-adapter-`                         | `gcr.io/<projectID>/foo/bar/tas-adapter-<appGUID>-packages`                         | Repositories are created dynamically during push by GCR                                                      |

### (Optional) Configure your admin users

VMware recommends configuring one or more administrative users for Application Service Adapter. To do this:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   admin:
     users:
     - "ADMIN-USERNAME"
     ...
   ```

   Where:

   - `ADMIN-USERNAME` is the name of an existing user in the Kubernetes cluster
     to whom you want to grant system admin privileges. You can specify as many
     users as you want, one per line. These names are identifiers for Kubernetes
     user accounts, not Kubernetes service accounts.
     - For Amazon EKS, see the [AWS IAM user management for EKS section](user-management.md#aws-iam-user-management-eks) of the User Management topic for information about additional required cluster configuration to map AWS IAM users and roles to Kubernetes roles.
     - For clusters configured to use authentication proxies such as [Pinniped](https://pinniped.dev/), you can authenticate to the cluster and use the output of `cf curl /whoami` to see the user account name to provide.

    These user names are those Kubernetes recognizes as user identifiers in the subject section of its role-based access control (RBAC) resources, such as `RoleBindings`. They can differ from the names of the user entries in your local Kubeconfig file. If you are not certain of this user name, you can leave this entry empty for the initial installation. After completing the installation and logging in with the cf CLI, use the `cf curl /whoami` command to confirm the user name and then update the installation with the correct name value. For more information about user subject names in Kubernetes, see the [Referring to subjects](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#referring-to-subjects) section of _Using RBAC Authorization_ and the [Authenticating](https://kubernetes.io/docs/reference/access-authn-authz/authentication/) topic in the Kubernetes project documentation.

### <a id="kpack-cluster-builder"></a>(Optional) Configure the Kpack cluster builder to use for staging

Application Service Adapter uses the `default` cluster builder provided by Tanzu Build Service. To create your own builder, see [Managing Builders](https://docs.vmware.com/en/Tanzu-Build-Service/1.9/vmware-tanzu-build-service/managing-builders.html) in the Tanzu Build Service documentation, and update this setting with the corresponding builder name.

To configure Application Service Adapter to use a different Kpack cluster builder for staging:

1. View the available cluster builders:

   ```bash
   kubectl get clusterbuilder
   ```

2. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   kpack_clusterbuilder_name: "KPACK-CLUSTER-BUILDER-NAME"
   ```

   Where `KPACK-CLUSTER-BUILDER-NAME` is the name of the kpack cluster builder to use for staging. Tanzu Build Service provides two cluster builders named `base` and `default`. To create your own builder, see [Managing Builders](https://docs.vmware.com/en/Tanzu-Build-Service/1.9/vmware-tanzu-build-service/managing-builders.html) in the Tanzu Build Service documentation, and update this setting with the corresponding builder name.

### <a id="openshift"></a>(Optional) Configuration for OpenShift

To configure Application Service Adapter for installation on OpenShift:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   shared:
     kubernetes_distribution: "openshift"
     kubernetes_version: "KUBERNETES-VERSION"
   ```

   Where `KUBERNETES-VERSION` is the Kubernetes version of the cluster. Default is v1.23.3.

### <a id="aws-iam-registry-auth"></a>(Optional) Use AWS IAM authentication for registry credentials

Tanzu Build Service supports using AWS IAM roles to authenticate with Amazon Elastic Container Registry (ECR) on Amazon Elastic Kubernetes Service (EKS) clusters.

To use AWS IAM authentication:

1. Configure an AWS IAM role that has read and write access to the repository in the container image registry used when installing Tanzu Application Platform.

2. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   app_registry:
     credentials:
       aws_iam_role_arn: "AWS-IAM-ROLE-ARN"
   ```

   Where `AWS-IAM-ROLE-ARN` is the Amazon Resource Name (ARN) of an AWS IAM role that can be used to access an Elastic Container Registry (ECR).

   The `app_registry.credentials.secret_name` and `app_registry.credentials.namespace` properties should be omitted from your `tas-adapter-values.yml` file.

### <a id="custom-ca-registry"></a>(Optional) Configure a registry with a custom Certificate Authority

> **Note** Your Kubernetes cluster nodes and the Tanzu Build Service component of Tanzu Application Platform must also both be configured to trust this Certificate Authority for the registry.

To configure Application Service Adapter to trust a registry that has a custom or self-signed certificate authority:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   app_registry:
     ca_cert:
       data: |
         PEM-ENCODED-CERTIFICATE-CONTENTS
   ```

   Where `PEM-ENCODED-CERTIFICATE-CONTENTS` is a PEM-encoded multiline string containing the certificate authority (CA) certificate.
   The value must be inserted into your values file as a YAML multiline string with a block scalar literal.

### <a id="legacy-app-registry-settings"></a>(Optional) Configure a registry using the legacy installation settings

> **Note** These settings have been deprecated as of Application Service Adapter v1.1 and will be removed in a future version.

To configure Application Service Adapter using the legacy installation settings:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   app_registry:
     hostname: "APP-REGISTRY-HOSTNAME"
     path:
       droplets: "APP-REGISTRY-PATH-DROPLETS"
   ```

   Where:

   - `APP-REGISTRY-HOSTNAME` is the host name of the registry used for app packages and droplets. For example:
     - Harbor has the form `hostname: "my-harbor.io"`.
     - Docker Hub has the form `hostname: "index.docker.io"`.
     - Google Container Registry has the form `hostname: "gcr.io"`.
   - `APP-REGISTRY-PATH-DROPLETS` is the path to the directory or project in the app registry where Application Service Adapter uploads droplets, such as runnable application images. This value does not include the registry host name itself. Examples:
     - Harbor has the form `droplets: "project-name/my-repo-name"`.
     - Docker Hub has the form `droplets: "my-dockerhub-username"`.
     - Google Container Registry has the form `droplets: "project-id/my-repo-name"`.

    The values specified for `app_registry.hostname` and `app_registry.path.droplets` are combined (separated by a `/`) to form the value for the new `app_registry.repository_prefix` setting. The value of the `app_registry.path.packages` setting is ignored. Source package images are stored in the specified droplets repository.

### <a id="cert-manager-certificate-issuer"></a>(Optional) Configure the Cert Manager certificate issuer to use for ingress certificates

By default, Application Service Adapter uses the `tap-ingress-selfsigned` cluster issuer provided by Tanzu Application Platform. To configure Application Service Adapter to use a different Cert Manager certificate issuer:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   shared:
     ingress_issuer: "CERTIFICATE-ISSUER-NAME"
   ```

   Where `CERTIFICATE-ISSUER-NAME` is the name of the cert-manager cluster issuer to use to generate certificates for HTTPS ingress to Application Service Adapter. Defaults to `tap-ingress-selfsigned`.

### <a id="user-provided-ingress-certs"></a>(Optional) Configure ingress certificates using a secret name and namespace

Users can provide their own certificates for either the Application Service Adapter API or applications, or both.

To configure the ingress certificate for the API:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   api_ingress:
     tls:
       secret_name: API-TLS-SECRET-NAME
       namespace: API-TLS-SECRET-NAMESPACE
   ```

   Where:

   - `API-TLS-SECRET-NAME` is the `kubernetes.io/tls` secret containing the PEM-encoded public certificate for the Application Service Adapter API.
   - `API-TLS-SECRET-NAMESPACE` is the namespace containing the API TLS secret.

1. To configure the ingress certificate for workloads, include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   app_ingress:
     tls:
       secret_name: APP-TLS-SECRET-NAME
       namespace: APP-TLS-SECRET-NAMESPACE
   ```

   Where:

   - `APP-TLS-SECRET-NAME` is the `kubernetes.io/tls` secret containing the PEM-encoded public certificate for applications deployed using Application Service Adapter.
   - `APP-TLS-SECRET-NAMESPACE` is the namespace containing the application TLS secret.

### <a id="experimental-cartographer-integration"></a>(Optional) Configure the experimental Cartographer integration

> **Note** Opting into the experimental Cartographer integration requires a larger set of Tanzu Application Platform packages to be installed. See [Required components for experimental Cartographer integration](install-prerequisites.md#required-components-cartographer) in _Install Prerequisites_.

To configure the experimental Cartographer integration:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   experimental_use_cartographer: true
   ```

### <a id="scaling-components"></a>(Optional) Scale Application Service Adapter components

To configure the scaling parameters for each component of Application Service Adapter:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
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
     cartographer_builder_runner:
       ... #! scaling keys are the same as above
     telemetry_informer:
       ... #! scaling keys are the same as above
   ```

   Where:

   - `API-CPU-LIMIT` is the CPU resource limit for the pods that you want in the specified deployment. Default is 1 CPU.
   - `API-MEMORY-LIMIT` is the memory resource limit that you want for the pods in the specified deployment. Default is 1000Mi.
   - `API-CPU-REQUEST` is the CPU resource request that you want for the pods in the specified deployment. Default is 50m.
   - `API-MEMORY-REQUEST` is the memory resource request that you want for the pods in the specified deployment. Default is 100Mi.
   - `API-REPLICA-COUNT` is the number of replicas that you want for the specified deployment. Default is 1.

   The `requests` and `limits` text boxes map directly to the resource requests and limits text boxes on the Kubernetes containers for these system components.
   For more information, see [Resource requests and limits of Pod and container](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-requests-and-limits-of-pod-and-container) in the Kubernetes documentation.

### <a id="custom-api-ingress-port"></a>(Optional) Configure a custom API ingress port

By default, the Application Service Adapter API is configured to listen on port 443, the standard port for HTTPS traffic. To change this value:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   api_ingress:
     port: "API-PORT"
   ```

   Where `API-PORT` is the port number that clients can use to connect to the Application Service Adapter API.
   The API includes this number in URLs that direct back to itself, but when set to
   `0` or left unset, no port number is in those URLs.
   In that case, clients can connect to port 443, the standard port for HTTPS traffic.

### <a id="auth-proxy"></a>(Optional) Configure an authentication proxy

To configure Application Service Adapter to use an authentication proxy:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   api_auth_proxy:
     ca_cert:
       data: |
         API-AUTH-PROXY-TLS-CRT
     host: "API-AUTH-PROXY-FQDN"
   ```

   Where:

   - `API-AUTH-PROXY-TLS-CRT` is the CA certificate from the authentication proxy running along side your Kubernetes cluster.
   - `API-AUTH-PROXY-FQDN` is the FQDN for the authentication proxy running along side your Kubernetes cluster.

### <a id="task-ttl"></a>(Optional) Configure the task time-to-live (TTL)

By default, Application Service Adapter retails task workloads for 30 days to
allow users to view logs from past tasks. If you have configured your cluster to
egress logs to an external aggregation service, you can reduce this time to
eliminate resources from the cluster.

> **Note** Task logs are no longer viewable using the `cf logs` command once a task has been purged from Application Service Adapter due to its age.

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   tasks:
     ttl: "TASK-TTL-AGE"
   ```

   Where `TASK-TTL-AGE` is the length of time until completed tasks are purged from the cluster.
   You can specify this value as a time duration in seconds, minutes, hours, or days. For example, "86400s", "1440m", "24h", or "1d".

### <a id="opt-out-telemetry"></a>(Optional) Opt out of telemetry reporting

When you install Application Service Adapter, you opt into telemetry collection by default. To deactivate telemetry collection, complete the following:

1. Ensure that your Kubernetes context is pointing to the cluster where Application Service Adapter is installed.

2. Run the following kubectl command:

   ```bash
   kubectl apply -f - <<EOF
   ---
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

### <a id="telemetry-reporting-interval"></a>(Optional) Configure the telemetry reporting interval:

The default telemetry reporting interval is 24 hours. To change how frequently telemetry data is sent to VMware:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   telemetry:
     heartbeat_interval: TELEMETRY-HEARTBEAT-INTERVAL
   ```

   Where `TELEMETRY-HEARTBEAT-INTERVAL` is how often telemetry data is sent to VMware. Default is every 24 hours.
   This value must be specified as a time duration in hours. For example, "24h", not "1d".

### <a id="user-cert-expiration-warning"></a>(Optional) Configure the user certificate expiration warning duration

For security, the Application Service Adapter API returns a warning in the `X-Cf-Warnings` header of the `/v3/orgs`
endpoint when a user authenticates with a certificate that is valid for longer than the specified duration. To change this value:

1. Include the following values in your `tas-adapter-values.yml` file:

   ```yaml
   user_certificate_expiration_warning_duration: "USER-CERT-EXPIRY-WARNING-DURATION"
   ```

   Where `USER-CERT-EXPIRY-WARNING-DURATION` is the duration beyond which users are warned to use short-lived certificates for authentication. Defaults to 168 hours. This value must be specified as a time duration in hours. For example, "168h", not "7d".

## <a id="install-adapter"></a>Install Application Service Adapter

To install Application Service Adapter:

1. Install Application Service Adapter to the cluster.

   ```bash
   tanzu package install tas-adapter \
     --package-name application-service-adapter.tanzu.vmware.com \
     --version "${TAS_ADAPTER_VERSION}" \
     --values-file tas-adapter-values.yml \
     --namespace tap-install
   ```

2. Verify that the package install was successful.

   ```bash
   tanzu package installed get tas-adapter \
     --namespace tap-install
   ```

   The following is an example output:

   ```bash
   | Retrieving installation details for tas-adapter...
   NAME:                    tas-adapter
   PACKAGE-NAME:            application-service-adapter.tanzu.vmware.com
   PACKAGE-VERSION:         1.1.0
   STATUS:                  Reconcile succeeded
   CONDITIONS:              [{ReconcileSucceeded True  }]
   USEFUL-ERROR-MESSAGE:
   ```

## <a id="configure-dns"></a>Configure DNS for Application Service Adapter

To configure DNS for Application Service Adapter:

1. Determine the external IP address to use for ingress to your cluster. This step varies depending on the IaaS used to provision your cluster.

   For clusters that support LoadBalancer services, you can obtain the external IP address of the LoadBalancer Service associated with Contour's Envoy proxy. The namespace for this service is typically either `tanzu-system-ingress` or `projectcontour` depending on how Contour was installed.

   ```bash
   kubectl -n tanzu-system-ingress get service envoy -ojsonpath='{.status.loadBalancer.ingress[*].ip}'
   ```

   > **Note** If you are using a cluster deployed on AWS, your LoadBalancer has a DNS name instead of an IP address.

2. Create an A record in your DNS zone that resolves the configured API FQDN to the external IP address from step 1. This step varies depending on your DNS provider.

   > **Note** If you are using a cluster deployed on AWS, create a CNAME record that resolves to the DNS name of the load balancer instead of an A record.

3. Create a wildcard A record in your DNS zone that resolves all sub-domains of the configured application domain to the external IP address from step 1. This step varies depending on your DNS provider.

4. Verify that the Contour HTTPProxy for the API endpoint is valid.

   ```bash
   kubectl -n korifi-api-system get httpproxy korifi-api-proxy
   ```

   The following is an example output:

   ```bash
   NAME               FQDN       TLS SECRET                STATUS   STATUS DESCRIPTION
   korifi-api-proxy   API-FQDN   korifi-api-ingress-cert   valid    Valid HTTPProxy
   ```

## <a id="assign-admin-user"></a>Log in with a system admin user

After you install the Cloud Foundry command-line interface (cf CLI), log in to the Application Service Adapter API with one of the system admin users you configured in the `admin.users` value:

1. Target the Application Service Adapter API endpoint.

   ```bash
   cf api API-FQDN --skip-ssl-validation
   ```

   Where `API-FQDN` is the fully qualified domain name (FQDN) for the Application Service Adapter API.

   > **Note** If you configured Application Service Adapter with a globally trusted certificate during installation, you can omit the `--skip-ssl-validation` flag.

2. Log in.

   ```bash
   cf login
   ```

   The cf CLI detects the user authentication entries in your local Kubeconfig file and presents them for you to select one interactively. Select an entry corresponding to one of the users you configured in the list in the `admin.users` value.

3. Use the `cf curl` command to verify the subject name of the logged-in user.

   ```bash
   cf curl /whoami
   ```

   The output looks like the following:

   ```bash
   {"name":"my_user@example.com","kind":"User"}
   ```

   The value of the `name` text box in the response is the subject name of the user, and matches the name configured in `admin.users`.

   The `kind` text box in the output must have the value `User`. If it is some other value, such as `ServiceAccount`, log in to Application Service Adapter with an account for a user in the Kubernetes cluster.

   To test Application Service Adapter, continue to [Getting Started](getting-started.md).
