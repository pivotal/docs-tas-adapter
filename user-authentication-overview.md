# User authentication

This topic describes how Application Service Adapter authenticates and authorizes users using the Kubernetes API server.

## <a id="background"></a> Background

Traditionally, the cf CLI authenticates with the Cloud Foundry [User Account and Authentication](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) (UAA) server, which acts as an OAuth2 provider. In this model, the Cloud Foundry API server validates the user's token and authorizes user actions based on its own set of user role assignments.

Application Service Adapter takes a different approach to user authentication and authorization. Instead of requiring UAA as a separate account and authentication service, Application Service Adapter delegates this responsibility to the [Kubernetes API server](https://kubernetes.io/docs/reference/access-authn-authz/authentication/). The cf CLI now recognizes when it targets a Kubernetes-backed CAPI server such as Application Service Adapter, uses user information from the local kubeconfig file to authenticate with the underlying Kubernetes API, and extracts the user token or client certificate or key pair from the authentication response. When it makes a request to the CAPI server, it then sends that credential in the Authorization header. The Cloud Foundry API server uses that credential to perform requests on behalf of the end user and retains it in memory only for the duration of the user's API request.

Application Service Adapter relies on core Kubernetes role-based access control (RBAC) resources such as `ClusterRole` and `RoleBinding` to configure user authorization rules. Platform operators can create these RBAC resources either using the Cloud Foundry role API endpoints or directly in the Kubernetes API.


## <a id="architecture"></a> Architecture

![CF API User Authentication Flow, showing user connection through cd CLI and TLS into the Kubernetes cluster.](images/tas_adapter_user_auth_flow.jpg)

The Application Service Adapter API requires that users connect to it using HTTPS because the Authorization header contains the user’s authentication token or client certificate or key pair. The API translates the CAPI request into Kubernetes API requests using the provided credentials.

>**Note** The user is authenticated through their Kubernetes token or client certificate or key for each request to the Adapter's API. There is no persistent session data stored between requests.

VMware recommends using short-lived tokens or certificates to authenticate with Application Service Adapter.
Application Service Adapter warns users if their certificate is still valid in one week.