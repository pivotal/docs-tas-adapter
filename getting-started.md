# Getting started with Application Service Adapter

This topic provides an overview of how to get started using the Application Service Adapter for Tanzu Application Platform (TAP):

* [Log in with the cf CLI](#log-in)
* [Creating Orgs and Spaces](#create-orgs-spaces)
* [Deploying a Sample App](#deploy-sample-app)
* [Routing to an App](#routing-sample-app)

## <a id="login"></a>Log in with the cf CLI

After you have installed the experimental version of the Cloud Foundry command-line interface (cf CLI), use it to target and log in to the Application Service Adapter.

To log in to the Application Service Adapter with the cf CLI:

1. Target the cf CLI at the API endpoint:

    ```bash
    cf api <API-FQDN> --skip-ssl-validation
    ```

    > **Note**: If you configured the Application Service Adapter with a globally trusted certificate during installation, you do not need to use the `--skip-ssl-validation` flag.

1. Log in with the cf CLI:

    ```bash
    cf login
    ```

    The cf CLI takes you through an interactive flow to set your username, and detects the user accounts in your local Kubeconfig file. Select one that is a cluster-admin for the Kubernetes cluster to which the Application Service Adapter is installed.

    > **Note**: The Application Service Adapter API does not verify authentication credentials in this release, so any user can execute commands against the API.

## <a id="create-orgs-spaces"></a>Create orgs and spaces

You can use `cf create-org` and `cf create-space` the same way that you do with Tanzu Application Service for VMs. Under the hood, these commands create a Kubernetes namespace for each org and each space, connected into a hierarchy using the Hierarchical Namespaces Controller (HNC). For more information, see the [hierarchical-namespaces](https://github.com/kubernetes-sigs/hierarchical-namespaces) repository on GitHub.

The version of HNC in this release does not correctly propagate `ServiceAccounts` down the namespace hierarchy. When `cf create-space` is called, the `ServiceAccount` required for Tanzu Build Service to build images is absent. Therefore as a user, you must create the `ServiceAccount` with a reference to the container image registry credentials.

1. Create a local file with the required ServiceAccount resources:

    ```yaml
    cat <<EOF >> service-accounts.yml
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: eirini
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: kpack-service-account
    imagePullSecrets:
    - name: image-registry-credentials
    secrets:
    - name: image-registry-credentials
    EOF
    ```

1. Create the CF org and space:

    ```bash
    cf create-org ORG-NAME
    cf target -o ORG-NAME
    cf create-space SPACE-NAME
    ```

    Where `ORG-NAME` is the name of the CF org you want to create and `SPACE-NAME` is the name of the CF space.

1. Get the GUID of the CF space:

    ```bash
    cf space SPACE-NAME --guid
    ```

1. Apply the `service-accounts.yml` to the Kubernetes namespace with name equal to the CF space GUID you retrieved earlier:

    ```bash
    kubectl apply -f service-accounts.yml -n SPACE-GUID
    ```

    The CF space you created is ready to accept app workloads.

1. Target the new CF space:

    ```bash
    cf target -s <SPACE-NAME>
    ```

## <a id="deploy-sample-app"></a>Deploy a sample app

The `cf push` command remains the same. After you've targeted the org and the space you created, you can push the app to the Kubernetes cluster:

```bash
cf push APP-NAME
```

> **Note:** For HTTP ingress routing to work, set the `PORT` environment variable to `8080` in the app manifest. For more information, see the [Release Notes](release-notes.md).

## <a id="routing-sample-app"></a>Route to an app

To configure routing for the app that you pushed:

1. Apply the cluster-scoped `CFDomain` custom resource for the ingress domain you have configured. For example:
   ```yaml
   apiVersion: networking.cloudfoundry.org/v1alpha1
   kind: CFDomain
   metadata:
     name: 5b5032ab-7fc8-4da5-b853-821fd1879201
   spec:
     name: apps.example.com
   ```

   The GUID in the `.metadata.name` property can be any v4 UUID that you choose or generate. The app Service Adapter API reports this name as the GUID of this domain.

2. Use the cf CLI to map a route to your app:

   ```bash
   cf map-route APP-NAME apps.example.com --hostname my-app --destination-protocol http
   ```
