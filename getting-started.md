# Getting Started with Application Service Adapter

This topic provides an overview of how to get started using the Application Service Adapter for Tanzu Application Platform:

* [Creating Orgs and Spaces](#create-orgs-spaces)
* [Deploying a Sample App](#deploy-sample-app)

## <a id="create-orgs-spaces"></a>Creating Orgs and Spaces
You can use `cf create-org` and `cf create-space` as if you were still using Tanzu Application Service. Under the hood these commands will create a nested `namespace` structure in the Kubernetes cluster.

As of the current release of Application Service Adapter, [Hierarchical Namespaces Controller](https://github.com/kubernetes-sigs/hierarchical-namespaces), which we rely on to create the nested namespaces, does not correctly propagate `ServiceAccounts`. When `cf create-space` is called, the ServiceAccount required for image building is absent. Therefore as a user, you must create the ServiceAccount with a reference to the image registry credentials.

1. Have a local copy of the required ServiceAccount resources

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

1. Create the cf space

    ```bash
    cf create-org <org_name>
    cf target -o <org_name>
    cf create-space <space_name>
    ```

1. Get the cf space guid which corresponds to the kubernetes namespace in which we create the ServiceAccount

    ```bash
    cf space <space_name> —guid
    ```

1. Apply the `service-accounts.yml` to that namespace
    
    ```bash
    kubectl apply -f service-accounts.yml -n <space_guid>
    ```

Now the space you just created is ready to accept application workloads.

## <a id="deploy-sample-app"></a>Deploying a Sample App

**NOTE:** This release does not support the user authentication yet (see [Release Notes](release-notes.md)). Once you have the orgs and spaces set up at the 

The `cf push` command remains the same. Once you've targeted at the org and the space you just created, you can push the application to the Kubernetes cluster:

```bash
cf push <my_app_name>
```