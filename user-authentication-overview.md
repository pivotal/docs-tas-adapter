# Authentication model 

This topic describes how the Application Service Adapter authenticates and authorizes roles using the Kubernetes API server.

## <a id="auth-over"></a> Authentication and Authorization Overview

### <a id="backrnd"></a> Background

Traditionally, the cf CLI has authenticated with the platform using Cloud Foundry's [User Account and Authentication (UAA)](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) server which acts as an OAuth2 provider. In this model, the Cloud Foundry API (CAPI) server validated the user's token and was responsible for most authorization decisions in Cloud Foundry.

With the Application Service Adapter, VMware takes a different approach. Rather than owning Auth(N/Z) and maintaining a list of roles ourselves, VMware is delegating this responsibility to the [Kubernetes API server](https://kubernetes.io/docs/reference/access-authn-authz/authentication/) and Kubernetes-native RBAC. VMware has updated the CLI to  authenticate with the Kubernetes API using the developer's local kubeconfig. The CLI extracts the user's token or client certificate or key pair from the authenticated Kubernetes client and sends them on every request over TLS in the Authorization header.
The CAPI translation layer then instantiates a new Kubernetes API client using the user's credentials to perform requests on their behalf. This client (and the user's credentials) only persists in memory for the duration of the HTTP request.

## <a id="arch"></a> Architecture

![CF API User Auth Flow](images/tas_adapter_user_auth_flow.jpg)

Users must communicate with the API through https due to the presence of the Auth header which contains the user’s authentication token or client certificate  or key pair. The API translates the Cloud Foundry API request into Kubernetes API requests using the provided credentials.

>**Note:** The user is authenticated through their Kubernetes token or client certificate or key for each request to the Adapter's API, with no persistent session data stored in-between.

### <a id="NAME"></a> Note on Best Practices

It is generally advisable to use short lived tokens or certificates with short expiry dates.
By default, the Application Service Adapter warns users if their certificate is longer-lived than one week.
