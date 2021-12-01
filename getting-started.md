# Getting Started with Application Service Adapter

This topic provides an overview of how to get started using the Application Service Adapter for Tanzu Application Platform:

* [Log in with the CF CLI](#log-in)
* [Creating Orgs and Spaces](#create-orgs-spaces)
* [Deploying a Sample App](#deploy-sample-app)
* [Routing to an App](#routing-sample-app)

## <a id="log-in"></a>Log in with the CF CLI

Once you have installed the experimental version of the CF CLI, use it to target and log into the Application Service Adapter.

1. Target the CF CLI at the API endpoint:

    ```bash
    cf api <API-FQDN> --skip-ssl-validation
    ```

    Note that if you configured the Application Service Adapter with a globally trusted certificate during installation, you do not need to specify the `--skip-ssl-validation` flag.

1. Log in with the CF CLI:

    ```bash
    cf login
    ```

    The CF CLI will take you through an interactive flow to set your user, and will detect the user accounts in your local Kubeconfig file. Select one that is a cluster-admin for the Kubernetes cluster to which the Application Service Adapter is installed.

    **NOTE**: The Application Service Adapter API does not verify authentication credentials in this release, so any user will be able to execute commands against the API.

## <a id="create-orgs-spaces"></a>Creating Orgs and Spaces

You can use `cf create-org` and `cf create-space` the same way that you would with Tanzu Application Service. Under the hood, these commands create a Kubernetes namespace for each org and each space, connected into a hierarchy using the [Hierarchical Namespaces Controller](https://github.com/kubernetes-sigs/hierarchical-namespaces) (HNC).

The version of HNC included in this release does not correctly propagate `ServiceAccounts` down the namespace hierarchy. When `cf create-space` is called, the `ServiceAccount` required for Tanzu Build Service to build images is absent. Therefore as a user, you must create the `ServiceAccount` with a reference to the image registry credentials.

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
    cf create-org <ORG-NAME>
    cf target -o <ORG-NAME>
    cf create-space <SPACE-NAME>
    ```

    where `<ORG-NAME>` is the name of the CF org you wish to create and `<SPACE-NAME>` is the name of the CF space.

1. Get the GUID of the CF space:

    ```bash
    cf space <SPACE-NAME> --guid
    ```

1. Apply the `service-accounts.yml` to the Kubernetes namespace with name equal to the CF space GUID you retrieved above:
    
    ```bash
    kubectl apply -f service-accounts.yml -n <SPACE-GUID>
    ```

    Now the CF space you just created is ready to accept application workloads.

1. Target the new CF space:

    ```bash
    cf target -s <SPACE-NAME>
    ```

## <a id="deploy-sample-app"></a>Deploying a Sample App

The `cf push` command remains the same. Once you've targeted at the org and the space you just created, you can push the application to the Kubernetes cluster:

```bash
cf push <APP-NAME>
```
**NOTE:** For HTTP ingress routing to work, as below, you need to set the `PORT` environment variable to `8080` in the application manifest. See [Release Notes](release-notes.md) for more information.


## <a id="routing-sample-app"></a>Routing to an App

To configure routing for the app that you pushed, you must do the following:

1. Apply the cluster-scoped `CFDomain` custom resource for the ingress domain you have configured. Here is an example:
   ```yaml
   apiVersion: networking.cloudfoundry.org/v1alpha1
   kind: CFDomain
   metadata:
     name: 5b5032ab-7fc8-4da5-b853-821fd1879201
   spec:
     name: apps.example.com
   ```

   Note that the GUID in the `.metadata.name` property above can be any v4 UUID that you choose or generate. The Application Service Adapter API will report this name as the GUID of this domain.

2. Use the `cf` CLI to map a route to your app:

   ```bash
   cf map-route <my_app_name> apps.example.com --hostname my-app --destination-protocol http
   ```
