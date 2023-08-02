# Release notes for Application Service Adapter

You can view the release notes for v1.3.0 of Application Service Adapter for VMware
Tanzu Application Platform on this page.

## <a id='1-3-0'></a> v1.3.0 Release

Release Date: MMM DD, 2023

### Features

This release includes the following new features:



### Components

This release contains the following components:

- cartographer-builder-runner @ cb29b8d
- Korifi @ [v0.7.1](https://github.com/cloudfoundry/korifi/tree/v0.7.1)

### Known issues

This release has the following issues:

- Deleting CF orgs and spaces or uninstalling Application Service Adapter may fail due to an issue with
  `ServiceBindingProjection` resource cleanup that prevents deletion of the underlying Kubernetes namespace.
  As a workaround, you can manually remove the `finalizers` from the `ServiceBindingProjections` to allow
  namespace deletion to complete.

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
