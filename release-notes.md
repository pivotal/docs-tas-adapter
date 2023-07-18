# Release notes for Application Service Adapter

You can view the release notes for v1.1.0 through v1.1.6 of Application Service Adapter for VMware
Tanzu Application Platform on this page.

## <a id='1-1-6'></a> v1.1.6 Release

**Release date**: July 18, 2023

### Changelog

* Properly set `dnsNames` field on cert-manager `Certificate` resource.
* Updated package versions and associated dependencies.

## <a id='1-1-5'></a> v1.1.5 Release

**Release date**: June 7, 2023

### Changelog

* Updated package versions and associated dependencies.

## <a id='1-1-4'></a> v1.1.4 Release

**Release date**: April 4, 2023

### Resolved issues

The following issues are resolved in this release:

* Applications with more than one environment variable set no longer restart sporadically.

### Components

This release contains the following components:

* cartographer-builder-runner @ baf5676
* Korifi @ [v0.5.0](https://github.com/cloudfoundry/korifi/tree/v0.5.0)
* tas-adapter-telemetry-controller @ 3692dc1

### Known issues

This release has the following known issues:

* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application fails to build showing an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app continues to fail to build.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.

## <a id='1-1-3'></a> v1.1.3 Release

**Release date**: March 31, 2023

### Resolved issues

The following issues are resolved in this release:

* Application routes are now configured correctly when using the `shared.ingress_issuer` configuration property to generate ingress certificates.
* Applications no longer restart sporadically because of indeterminacy in the representation of pod affinity selectors on their underlying StatefulSet resources.

### Components

This release contains the following components:

* cartographer-builder-runner @ baf5676
* Korifi @ [v0.5.0](https://github.com/cloudfoundry/korifi/tree/v0.5.0)
* tas-adapter-telemetry-controller @ 3692dc1

### Known issues

This release has the following known issues:

* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application fails to build showing an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app continues to fail to build.
* Applications with more than one environment variable set may restart sporadically during periodic reconciliation because of indeterminacy in the representation of those environment variables on the applications' underlying StatefulSet resources.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.

## <a id='1-1-2'></a> v1.1.2 Release

**Release date**: March 7, 2023

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

* When using the `shared.ingress_issuer` property to generate certificates for HTTPS ingress to Application Service Adapter, HTTPS routes to applications fail on account of a mismatched internal Secret name. As a workaround, instead use the `app_ingress.tls.secret_name` and `app_ingress.tls.namespace` properties to configure the TLS secret for application ingress routes, and recreate any application routes created when `shared.ingress_issuer` was set.
* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application fails to build with an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app continues to fail to build.
* Applications may restart sporadically during periodic reconciliation because of indeterminacy in the representation of pod affinity selectors on the applications' underlying StatefulSet resources.
* Applications with more than one environment variable set may restart sporadically during periodic reconciliation because of indeterminacy in the representation of those environment variables on the applications' underlying StatefulSet resources.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.

## <a id='1-1-1'></a> v1.1.1 Release

**Release date**: February 15, 2023

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
* When using the `shared.ingress_issuer` property to generate certificates for HTTPS ingress to Application Service Adapter, HTTPS routes to applications fail on account of a mismatched internal Secret name. As a workaround, instead use the `app_ingress.tls.secret_name` and `app_ingress.tls.namespace` properties to configure the TLS secret for application ingress routes, and recreate any application routes created when `shared.ingress_issuer` was set.
* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application fails to build with an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app continues to fail to build.
* Applications may restart sporadically during periodic reconciliation because of indeterminacy in the representation of pod affinity selectors on the applications' underlying StatefulSet resources.
* Applications with more than one environment variable set may restart sporadically during periodic reconciliation because of indeterminacy in the representation of those environment variables on the applications' underlying StatefulSet resources.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.

## <a id='1-1-0'></a> v1.1.0 Release

**Release date**: February 8, 2023

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
* When using the `shared.ingress_issuer` property to generate certificates for HTTPS ingress to Application Service Adapter, HTTPS routes to applications fail on account of a mismatched internal Secret name. As a workaround, instead use the `app_ingress.tls.secret_name` and `app_ingress.tls.namespace` properties to configure the TLS secret for application ingress routes, and recreate any application routes created when `shared.ingress_issuer` was set.
* If you push an application with a specific buildpack set with the `buildpacks` property in the application manifest or with the `-b` flag, that application fails to build with an error that only autodetection of buildpacks is supported. As a workaround, set `buildpacks: ~` in the application manifest or `-b null` on `cf push` to reset the app to use buildpack autodetection. If you only remove the field from the manifest or the flag from the `cf push` command, the app continues to fail to build.
* When deleting CF spaces or uninstalling Application Service Adapter, the underlying Kubernetes namespaces may not be
deleted due to an issue with `ServiceBindingProjection` resource cleanup. As a workaround, you can manually remove the
`finalizers` from the `ServiceBindingProjections` to allow namespace deletion to complete.
* Applications may restart sporadically during periodic reconciliation because of indeterminacy in the representation of pod affinity selectors on the applications' underlying StatefulSet resources.
* Applications with more than one environment variable set may restart sporadically during periodic reconciliation because of indeterminacy in the representation of those environment variables on the applications' underlying StatefulSet resources.
* The organization manager role does not have permissions to create Cloud Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to create spaces in organizations.

---

## Deprecations

The following features are deprecated.
Deprecated features will remain on this list until they are retired from Application Service Adapter.

### Installation Properties

The following properties on the installation package for Application Service Adapter are deprecated as of v1.1.0 and are marked for removal in v1.4.0:

- `app_registry.hostname`
- `app_registry.paths.droplets`
- `app_registry.paths.packages`

Use the app_registry.repository_prefix property instead. See [_Install Application Service Adapter_](install.md) and [_Upgrading Application Service Adapter_](upgrading.md) for more details.
