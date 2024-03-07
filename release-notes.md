# Release notes for Application Service Adapter

You can view the release notes for v1.3.0 and v1.3.1 of Application Service Adapter for VMware
Tanzu Application Platform on this page.

## <a id='1-3-1'></a> v1.3.1 release

**Release date**: Mar 07, 2024

### Changelog

* Fixed compatibility issue with CF CLI v8.7.8.
* Updated package versions and associated dependencies.

## <a id='1-3-0'></a> v1.3.0 release

Release date: Aug 08, 2023

### Features

This release includes the following new features:

- Telemetry gathering is moved into the existing Tanzu Application Platform
  process, so Application Service Adapter no longer requires a separate
  deployment.
- Improved `cf push` performance for subsequent pushes of an app.
- Buildpack selection from available buildpacks through `cf push -b`.
- Support for `--strategy=rolling` for `cf push`, `restart`, and `restage`. This
  only works for apps started by v1.3 or later. Be sure to run a regular `cf
  restart` to enable rolling restart on your existing apps. `--strategy=rolling`
  is not supported when `experimental_use_cartographer` is set to `true`.
- The CLI now waits until deletions are complete instead of always
  returning immediately.
- Support for updating an app name, its lifecycle.data.buildpacks, and
  lifecycle.data.stack.

#### Installation properties

The following installation properties are new for Application Service Adapter
v1.3.0:

- `openshift.create_scc` for better control over UIDs on OpenShift clusters.
- `staging_resources.build_cache_mb` to configure the size of the build cache.
- `kpack_clusterbuilder_service_account_name` to allow the use of custom
  `ClusterBuilders` that rely on a different `ServiceAccount`.

### Components

This release contains the following components:

- cartographer-builder-runner @ 960f0275
- Korifi @ [v0.8.1](https://github.com/cloudfoundry/korifi/tree/v0.8.1)

### Known issues

This release has the following issues:

- Deleting CF orgs and spaces or uninstalling Application Service Adapter may
  fail due to issues with `ServiceBindingProjection` and `Application` resource
  cleanup that prevent deletion of the underlying Kubernetes namespace. As a
  workaround, you can manually remove the `finalizers` from any remaining
  `ServiceBindingProjections` or `Applications` to allow namespace deletion to
  complete.

### Deprecations

No features are deprecated in this release.

#### Deprecated installation properties

The following properties on the installation package for Application Service
Adapter are deprecated as of v1.3.0 and are designated for removal no earlier
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
