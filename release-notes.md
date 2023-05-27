# Release notes for Application Service Adapter

You can view the release notes for v1.3.0 of Application Service Adapter for VMware
Tanzu Application Platform on this page.

## <a id='1-3-0'></a> v1.3.0 Release

Release Date: July 25, 2023

### Features

This release includes the following new features:



### Components

This release contains the following components:

- cartographer-builder-runner @ cb29b8d
- Korifi @ [v0.7.1](https://github.com/cloudfoundry/korifi/tree/v0.7.1)

### Known issues

This release has the following issues:

- The value of the `cf_api` key in the `VCAP_APPLICATION` environment variable is incorrectly set to "https://".
  As a workaround, the value of the `extraVCAPApplicationValues.cf_api` key can be manually updated in the `korifi-controllers-config` `ConfigMap` resource.

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
