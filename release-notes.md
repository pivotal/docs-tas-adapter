# Release notes for Application Service Adapter

You can view the release notes for v1.0.0 through v1.0.4 of Application Service Adapter for VMware
Tanzu Application Platform on this page.

## <a id='1-0-4'></a> v1.0.4 Release

**Release Date**: July 18, 2023

### Changelog

* Updated package versions and associated dependencies.

## <a id='1-0-3'></a> v1.0.3 Release

**Release Date**: June 7, 2023

### Changelog

* Updated package versions and associated dependencies.

## <a id='1-0-2'></a> v1.0.2 Release

**Release Date**: February 8, 2023

### Security fixes

* System components in this release have been rebuilt with Go 1.19.5 to fix [CVE-2022-41717](https://nvd.nist.gov/vuln/detail/CVE-2022-41717) and [CVE-2022-41720](https://nvd.nist.gov/vuln/detail/CVE-2022-41720).

## <a id='1-0-1'></a> v1.0.1 Release

**Release Date**: December 13, 2022

### Resolved issues

* Application instance pods now have the `CF_INSTANCE_GUID`, `CF_INSTANCE_INTERNAL_IP`, `CF_INSTANCE_IP`, or `POD_NAME` environment variables set when Application Service Adapter is configured to use the experimental integration with the Supply Chain Choreographer.
* The output of `cf logs` no longer contains blank log lines interleaved with the log content from the appplication instances.
* The values schema published in the Tanzu package for Application Service Adapter now matches the values that the package installation recognizes.

### Components

This release contains the following components:

* cartographer-builder-runner @ 9ae3d6c
* Korifi @ [v0.3.0](https://github.com/cloudfoundry/korifi/tree/v0.3.0)
* tas-adapter-telemetry-controller @ 0a2e7ba

### Known Issues

* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application will fail to build with an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app will continue to fail to build.
* If you change the application code so that the build process generates a different start command for the app, the app's start command is not updated, and the app may fail to start correctly. As a workaround, you can manually override the start command with the `command` property in the application manifest or with the `-c` flag on `cf push`.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.


## <a id='1-0-0'></a> v1.0.0 Release

**Release Date**: November 10, 2022


### Features

#### Application management

* Application developers can use `cf push` to create, build, and run applications from source code.
* Application developers can use `cf stop` to stop an application without deleting it.
* Application developers can use `cf start` to start a stopped application.
* Application developers can use `cf restart` to restart an application.
* Application developers can use `cf restage` to restage an application.
* Application developers can use `cf delete` to delete an application.
* Application developers can use `cf apps` to list the applications in a Cloud Foundry space.
* Application developers can use `cf app` to describe an application, including the status and CPU and memory usage metrics for its instances.
* Application developers can use `cf buildpacks` to list the system buildpacks.
* Application developers can use `cf get-health-check` to get the health-check type of an application's process types.
* Application developers can use `cf set-health-check` to set the health-check type of an application's process types.
* Application processes with a route mapped to them have a port-based health-check by default.
* Application developers can use `cf env` to list the environment variables on an application.
* Application developers can use `cf set-env` to set an environment variable on an application.
* Application developers can use `cf unset-env` to remove an environment variable on an application.
* Application environment variables are included in the environment variables set during the application build process.
* Application developers can use `cf set-label` to set a label on an application.
* Application developers can use `cf unset-label` to unset a label on an application.
* Application developers can use `cf labels` to read labels on an application.
* Application developers can use CF API endpoints to read, set, and unset annotations on an application.
* Applications have a default memory limit of 1024M.


#### Application routes and domains

* Application developers can use `cf create-route` to create a route.
* Application developers can use `cf delete-route` to delete a route.
* Application developers can use `cf map-route` to map a route from an application.
* Application developers can use `cf unmap-route` to unmap a route from an application.
* Application developers can use `cf domains` to list the shared domains available for routes.
* Applications receive a HTTP route on `cf push` unless opting out with the `--no-route` flag.
* Application developers can obtain a randomly generated route for their app using the `--random-route` flag on `cf push` or the `random-route` key in the application manifest.
* Application Service Adapter validates that domains do not overlap.
* Application Service Adapter validates that each route has a non-empty hostname and has a fully qualified domain name that is a valid DNS name.
* Application Service Adapter validates that paths of routes are well-formed and less than 128 characters in length.


#### Application logs

* Application developers can see application staging logs during `cf push`.
* Application developers can use `cf logs` to stream logs from running applications.
* Application developers can use `cf logs --recent` to retrieve recent build and runtime logs for applications.


#### Application tasks

* Application developers can use `cf run-task` to run a one-off task for an application.
* Application developers can use `cf tasks` to list the one-off tasks for an application.
* Application developers can use `cf terminate-task` to cancel a running one-off task for an application.


#### Service management

* Application developers can use `cf create-user-provided-service` to create a user-provided service instance.
* Application developers can use `cf services` to list the user-provided service instances in the current CF space.
* Application developers can use `cf service` to describe a user-provided service instance.
* Application developers can use `cf delete-service` to delete a user-provided service instance.
* Application developers can use `cf bind-service` to bind a user-provided service instance to an application. After the app is restarted or restaged, the  contains the credentials for the bound service.
* Application Service Adapter presents service credentials to bound applications both in the application's `VCAP_SERVICES` environment variable as [filesystem projections](https://github.com/servicebinding/spec#workload-projection) under the service binding root directory.
* Application developers can use `cf unbind-service` to unbind a user-provided service instance from an application.


#### User authentication and authorization

* Application developers can use `cf login` to log into the Application Service Adapter with authentication information from their local kubeconfig file.
* The Application Service Adapter enforces authorization rules for API resources. A user must have an admin role or a Space Developer role to push apps and map routes.


#### System installation

* System operators can use the `tanzu` CLI to install Application Service Adapter as a Tanzu package.
* System operators can configure the Application Service Adapter system to trust container registries that have certificates signed by private certificate authorities.
* System operators can set replica counts and resource limits for each Application Service Adapter system component.
* System operators can set the expiry duration beyond which users of the Application Service Adapter API are notified to use shorter-lived certificates for authentication.
* System operators must specify a domain to use as the default shared domain for application routes.
* System operators can change the default shared domain for application routes after an initial installation.
* System operators can specify the name of the kpack ClusterBuilder to use to build application images, defaulting to `default`.
* System operators can configure Application Service Adapter to work with Kubernetes authentication proxies such as Pinniped.
* System operators can configure the certificate/private key pairs for the Application Service Adapter API and app ingress TLS using Kubernetes secrets instead of certificate and key contents.
* System operators can opt out of telemetry collection by creating a particular ConfigMap in the underlying Kubernetes cluster, identical to the one used to opt out of telemetry collection for Tanzu Application Platform.


#### Org and space management

* System operators can use `cf create-org` to create a Cloud Foundry org.
* System operators can use `cf create-space` to create a Cloud Foundry space.
* System operators can also create `CFOrg` and `CFSpace` custom resources in the Kubernetes API to create orgs and spaces.
* System operators can use `cf delete-space` to delete a Cloud Foundry space.
* System operators can use `cf delete-org` to delete a Cloud Foundry org.
* System operators can use `cf set-label` to set a label on an org or a space.
* System operators can use `cf unset-label` to unset a label on an org or a space.
* System operators can use `cf labels` to read labels on an org or a space.
* System operators can use CF API endpoints to read, set, and unset annotations on an org or a space.


#### System security

* App traffic runs over HTTPS instead of HTTP, with the terminating TLS certificate and private key specified at installation time.


#### Supply Chain Choreographer integration (experimental)

* **EXPERIMENTAL**: Platform operators can configure the Application Service Adapter system to build and run applications using the Workload resource from Tanzu Application Platform. Note that this configuration is experimental and applies system-wide.


### Components

This release contains the following components:

* cartographer-builder-runner @ 9ae3d6c
* Korifi @ [v0.3.0](https://github.com/cloudfoundry/korifi/tree/v0.3.0)
* tas-adapter-telemetry-controller @ 0a2e7ba


### Known Issues

* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application will fail to build with an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app will continue to fail to build.
* If you change the application code so that the build process generates a different start command for the app, the app's start command is not updated, and the app may fail to start correctly. As a workaround, you can manually override the start command with the `command` property in the application manifest or with the `-c` flag on `cf push`.
* Application instance pods do not have the `CF_INSTANCE_GUID`, `CF_INSTANCE_INTERNAL_IP`, `CF_INSTANCE_IP`, or `POD_NAME` environment variables set when Application Service Adapter is configured to use the experimental integration with the Supply Chain Choreographer.
* The output of `cf logs` contains blank log lines interleaved with the log content from the appplication instances.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.
* The values schema published in the Tanzu package for Application Service Adapter does not precisely match the values that the package installation recognizes. The published schema contained obsolete parameters for scaling the `kube_rbac_proxy` component and for enabling telemetry collection and omitted the `api_ingress.port` parameter.
