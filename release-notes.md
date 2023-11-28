# Release notes for Application Service Adapter

You can view the release notes for v1.4.0 of Application Service Adapter for VMware
Tanzu Application Platform on this page.

## <a id='1-3-0'></a> v1.4.0 release

Release date: mmmm dd, yyyy

### Features

This release includes the following new features:



#### Installation properties


### Components

This release contains the following components:

- cartographer-builder-runner @ 960f0275
- Korifi @ [v0.8.1](https://github.com/cloudfoundry/korifi/tree/v0.8.1)

### Known issues

This release has the following issues:



### Deprecations

No features are deprecated in this release.

#### Deprecated installation properties

The following properties on the installation package for Application Service
Adapter were deprecated as of v1.3.0 and are designated for removal no earlier
than v1.6.0:

- `telemetry.context_product_version`
- `telemetry.installed_for_vmware_internal_use`
- `telemetry.heartbeat_interval`
- `scaling.telemetry_informer.limits.cpu`
- `scaling.telemetry_informer.limits.memory`
- `scaling.telemetry_informer.requests.cpu`
- `scaling.telemetry_informer.requests.memory`
- `scaling.telemetry_informer.replicas`

The following properties on the installation package for Application Service
Adapter were deprecated as of v1.1.0 and are designated for removal no earlier
than v1.4.0:

- `app_registry.hostname`
- `app_registry.paths.droplets`
- `app_registry.paths.packages`

Use the `app_registry.repository_prefix` property instead. See [Install
Application Service Adapter](install.md) and [Upgrading Application Service
Adapter](upgrading.md) for more details.
