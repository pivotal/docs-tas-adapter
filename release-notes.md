# Release notes

This topic contains release notes for Application Service Adapter for VMware Tanzu Application Platform.

## <a id='1-1-2'></a> v1.1.2 Release

**Release Date**: March 7, 2023

### Resolved issues

The following issues are resolved in this release:

* Certificates for HTTPS ingress to applications and to the Application Service Adapter API can now be generated using the `shared.ingress_issuer` property with common issuers, such as the ACME issuer from Let's Encrypt.

### Components

This release contains the following components:

* cartographer-builder-runner @ baf5676
* Korifi @ [v0.5.0](https://github.com/cloudfoundry/korifi/tree/v0.5.0)
* tas-adapter-telemetry-controller @ 3692dc1

### Known issues

This release has the following known issues:

* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application fails to build with an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app continues to fail to build.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.

## <a id='1-1-1'></a> v1.1.1 Release

**Release Date**: February 15, 2023

### Resolved issues

The following issues are resolved in this release:

* Credentials for service instances are now provided to application instances as directories of files under the service-binding root directory.

### Components

This release contains the following components:

* cartographer-builder-runner @ baf5676
* Korifi @ [v0.5.0](https://github.com/cloudfoundry/korifi/tree/v0.5.0)
* tas-adapter-telemetry-controller @ 3692dc1

### Known issues

This release has the following known issues:

* When using the `shared.ingress_issuer` property to generate certificates for HTTPS ingress to Application Service Adapter, some issuers will fail to issue the certificates because the list of DNS SANs on the certificate request does not include the common name. In particular, the ACME issuer from Let's Encrypt reports this error when generating these certificates. As a workaround, issue the certificates separately, then use the `api_ingress.tls` and `app_ingress.tls` properties to configure Application Service Adapter with the Kubernetes Secret containing the certificate data.
* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application fails to build with an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app continues to fail to build.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.

## <a id='1-1-0'></a> v1.1.0 Release

**Release Date**: February 8, 2023

### Features

#### Application management

* **FIXED**: When an application developer changes application code so that the build process generates a different start command for the application, the application's start command updates correctly.

#### Labels and annotations

* Application developers can use Cloud Foundry API endpoints to read, set, and unset annotations on application packages, processes, and routes.

#### System operation

* System operators now configure Application Service Adapter with a single image registry location to store application droplets and packages.
* System operators can use ECR as a container image registry when Application Service Adapter is installed on an EKS cluster.
* System operators get logs from system components in a common JSON-based structured format.
* System operators can install Application Service Adapter on Red Hat OpenShift v4.11.

#### Supply Chain Choreographer integration (experimental)

> **Note** Upgrades from v1.0 to v1.1 are not supported if you have enabled the experimental Cartographer integration. Existing application workloads will not behave correctly after upgrading from v1.0 to v1.1.

* Application developers can run `tanzu apps workload get` to display the pods for application instances.
* Application developers can run `tanzu apps workload tail` to display build logs for applications and running logs for application instances.
* Application process types in addition to the default `web` type now also run. Each process type runs as a separate Tanzu Application Platform Workload.
* The Tanzu Application Platform Convention Service applies its conventions to applications processed in a supply chain.


### Components

This release contains the following components:

* cartographer-builder-runner @ baf5676
* Korifi @ [v0.5.0](https://github.com/cloudfoundry/korifi/tree/v0.5.0)
* tas-adapter-telemetry-controller @ 3692dc1

### Known issues

* Credentials for service instances are not provided to application instances as directories of files under the service-binding root directory, although they are still present in the `VCAP_SERVICES` environment variable. Applications that rely on libraries that consume the file-based presentation of these credentials, such as the spring-cloud-bindings library from the Tanzu Java Buildpack, may fail to process these credentials as a result.
* When using the `shared.ingress_issuer` property to generate certificates for HTTPS ingress to Application Service Adapter, some issuers will fail to issue the certificates because the list of DNS SANs on the certificate request does not include the common name. In particular, the ACME issuer from Let's Encrypt reports this error when generating these certificates. As a workaround, issue the certificates separately, then use the `api_ingress.tls` and `app_ingress.tls` properties to configure Application Service Adapter with the Kubernetes Secret containing the certificate data.
* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application fails to build with an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app continues to fail to build.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.

### Deprecations

The following properties on the installation package for Application Service Adapter are deprecated:

* `app_registry.hostname`
* `app_registry.paths.droplets`
* `app_registry.paths.packages`

Use the `app_registry.repository_prefix` property instead. See [_Install Application Service Adapter_](install.md) and [_Upgrading Application Service Adapter_](upgrading.md) for more details.
