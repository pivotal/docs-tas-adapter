# Release notes for Application Service Adapter

You can view the release notes for v1.2.0 and v1.2.1 of Application Service Adapter for VMware
Tanzu Application Platform on this page.

## <a id='1-2-1'></a> v1.2.1 Release

**Release date**: July 18, 2023

### Changelog

* Add missing `cf_api` env var to App environments.
* Changes to support Tanzu Application Platform 1.6.
* Updated package versions and associated dependencies.

## <a id='1-2-0'></a> v1.2.0 Release

**Release date**: April 26, 2023

### Features

This release includes the following new features:

#### Application management

- Application developers can now:
  - Set labels and annotations on an application through the application
    manifest.
  - Push manifests containing multiple applications.
  - See the application memory and disk quotas in the output of `cf app`.
  - Use CF API endpoints to read, set, and unset labels and annotations on a
    build or an application task.
- Application instances now have the `VCAP_APPLICATION` environment variable
  available in their runtime environment.

#### Application routes and domains

- System operators can now use:
  - `cf set-label` to set a label on a domain.
  - `cf unset-label` to unset a label on a domain.
  - `cf labels` to read labels on a domain.
  - Cloud Foundry API endpoints to read, set, and unset annotations on a domain.

#### Service management

- Application developers can now:
  - Set a service label on a user-provided service instance to override the
    default `user-provided` label in the `VCAP_SERVICES` environment variable.
  - Use `cf set-label` to set a label on a service instance or a service
    binding.
  - Use `cf unset-label` to unset a label on a service instance or a service
    binding.
  - Use `cf labels` to read labels on a service instance or a service binding.
  - Use Cloud Foundry API endpoints to read, set, and unset annotations on a
    service instance or a service binding.

#### User authentication and authorization

- System operators can now use:
  - `cf set-org-role` and `cf set-space-role` to assign Cloud Foundry user roles
    to Kubernetes service accounts.
  - `cf unset-org-role` and `cf unset-space-role` to remove Cloud Foundry user
    roles from Kubernetes service accounts.

#### System operation

- Application Service Adapter deletes the corresponding container image from the
  image registry when a source-code package or build is deleted.
- Application Service Adapter deletes older inactive builds and source-code
  packages for applications.
- System operators can set the number of inactive builds to retain per
  application, defaulting to 5.
- System operators can set the number of inactive source-code packages to retain
  per application, defaulting to 5.
- System components include a log level of `error`, `info`, or `debug` in each
  log message.
- System operators can set the installation values under `log_level` to
  configure the log level for each system component.
- Application Service Adapter consumes fewer system resources in a default
  installation through consolidation of system components into fewer container
  images.

#### Org and space management

- System operators can name orgs and spaces with validation rules matching those
  in Tanzu Application Service.
- The `cf orgs` now returns the correct list of organizations.

#### Supply Chain Choreographer integration (experimental)

- Application developers can view the Cartographer workloads and deliverables
  generated for applications in the Supply Chains section of the Tanzu
  Application Platform GUI.

### Components

This release contains the following components:

- cartographer-builder-runner @ cb29b8d
- Korifi @ [v0.7.1](https://github.com/cloudfoundry/korifi/tree/v0.7.1)
- tas-adapter-telemetry-controller @ 37dda46

### Known issues

This release has the following issues:

- The value of the `cf_api` key in the `VCAP_APPLICATION` environment variable is incorrectly set to "https://".
  As a workaround, the value of the `extraVCAPApplicationValues.cf_api` key can be manually updated in the `korifi-controllers-config` `ConfigMap` resource.
- If you push an application with a specific buildpack set with the `buildpacks`
  property in the application manifest or with the `-b` flag, that application
  fails to build showing an error that only autodetection of buildpacks is
  supported. As a workaround, set `buildpacks: ~` in the application manifest or
  `-b null` on `cf push` to reset the app to use buildpack autodetection. If you
  only remove the field from the manifest or the flag from the `cf push`
  command, the app continues to fail to build.
- When deleting CF spaces or uninstalling Application Service Adapter, the underlying Kubernetes namespaces may not be
  deleted due to an issue with `ServiceBindingProjection` resource cleanup. As a workaround, you can manually remove the
  `finalizers` from the `ServiceBindingProjections` to allow namespace deletion to complete.
- The organization manager role does not have permissions to create Cloud
  Foundry spaces. As a workaround, instead use the Cloud Foundry admin role to
  create spaces in organizations.

### Deprecations

The following features are deprecated.

#### Installation Properties

The following properties on the installation package for Application Service
Adapter are deprecated as of v1.1.0 and are designated for removal no earlier
than v1.4.0:

- `app_registry.hostname`
- `app_registry.paths.droplets`
- `app_registry.paths.packages`

Use the app_registry.repository_prefix property instead. See [_Install
Application Service Adapter_](install.md) and [_Upgrading Application Service
Adapter_](upgrading.md) for more details.
