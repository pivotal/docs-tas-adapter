# Getting started with Application Service Adapter

This get-started walkthrough gives you hands-on experience of Application
Service Adapter: creating an org and space, deploying
an app, and more.

## <a id="create-orgs-spaces"></a>Create orgs and spaces

You can use `cf create-org` and `cf create-space` the same way that you do with Tanzu Application Service for VMs. Under the hood, these commands create a Kubernetes namespace for each org and each space and propagate required resources into the namespace.

To create orgs and spaces:

1. Create the Cloud Foundry org and space.

    ```bash
    cf create-org ORG-NAME
    cf target -o ORG-NAME
    cf create-space SPACE-NAME
    cf target -s SPACE-NAME
    ```

    Where:

    - `ORG-NAME` is the name of the org you want to create.
    - `SPACE-NAME` is the name of the space you want to create.

1. (Optional) Assign the SpaceDeveloper role to other users in the Kubernetes cluster.

   ```
   cf set-space-role USER-NAME ORG-NAME SPACE-NAME SpaceDeveloper
   ```

   Where `USER-NAME` is the name of another user in the Kubernetes cluster.


## <a id="deploy-sample-app"></a>Deploy a sample app

Use the cf CLI to deploy a sample app to the Application Service Adapter installation.

```bash
cf push APP-NAME
```

Where `APP-NAME` is the name of your app.

## <a id="routing-sample-app"></a>Route to an app

Applications automatically receive a default HTTP route unless pushed with the `--no-route` flag. This default route uses the name of the app as the route host name.
To configure additional routes for the app that you pushed, use the cf CLI to map a route to your app.

```bash
cf map-route APP-NAME apps.example.com --hostname my-app
```

## <a id="user-provided-services"></a>Create and bind to a user-provided service instance

Service credentials are provided to apps through user-provided service instances. See [User-Provided Service Instances](https://docs.cloudfoundry.org/devguide/services/user-provided.html) in the Cloud Foundry documentation.

To create and bind user-provided service instances, do the following:

1. Create a user-provided service instance containing the credentials necessary for accessing your service:

    ```bash
    cf create-user-provided-service SERVICE-INSTANCE-NAME -p '{"credential-name": "credential-value"}'
    ```

    Where `SERVICE-INSTANCE-NAME` is the name of your service instance.

1. Bind the service instance to your app:

    ```bash
    cf bind-service APP-NAME SERVICE-INSTANCE-NAME
    ```

1. Restart (or restage if a buildpack relies on the service) the app to make the service credentials available:

    ```bash
    cf restart APP-NAME
    ```

User-provided service instance credentials is provided to the app and staging tasks in two ways to support both existing TAS applications and next-generation frameworks, such as [Spring Cloud Bindings](https://github.com/spring-cloud/spring-cloud-bindings):

* As part of the traditional Cloud Foundry [VCAP_SERVICES environment variable](https://docs.cloudfoundry.org/devguide/deploy-apps/environment-variable.html#VCAP-SERVICES)
* As volume mounted secrets in accordance with the [Service Bindings for Kubernetes specification](https://servicebinding.io/spec/core/1.0.0/#workload-projection)
  * This workload projection handles the [Service Bindings Package](https://docs.vmware.com/en/Tanzu-Application-Platform/1.1/tap/GUID-service-bindings-install-service-bindings.html) from Tanzu Application Platform
