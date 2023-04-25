# Release notes

This topic contains release notes for Application Service Adapter for VMware Tanzu Application Platform.

## <a id='1-2-0'></a> v1.2.0 Release

**Release Date**: April 25, 2023

### Features

This release includes the following new features:

#### Application management
- Deleting a package or build/droplet will trigger deletion of the corresponding container image (when possible).
- Only the 5 most recent packages and builds/droplets are retained for each app. This number is configurable.
- Memory and disk quotas are now included in process stats.
- Labels and annotations are now supported on all CF resources.
- Application containers now include the VCAP_APPLICATION environment variable.
- Users can now push manifests containing multiple apps.
- Added support for the /v3/apps/<guid>/packages API endpoint

#### Service management
- Users can add custom service labels to user-provided service instances to make them appear in VCAP_SERVICES under that key and with that label.

#### User authentication and authorization
- Kubernetes service accounts can now be granted space and org roles via the `cf set-org-role` and `cf set-space-role` commands.
- The `cf unset-org-role` and `cf unset-space-role` commands are now supported for Kubernetes users and service accounts.

#### System operation
- System operators can now use alternative ingress controllers.
- Components now log each message at the appropriate level (e.g. Error, Info, Debug), and the system operator can choose which levels are emitted.
- Components use fewer resources due to newly consolidated single controller image replacing previous multiple images.

#### Org and space management
- Orgs and spaces now support all names that would be considered valid by traditional Cloud Foundry.

#### Supply Chain Choreographer integration (experimental)
- TAP GUI now displays more information about workloads running on Application Service Adapter.

### Components

This release contains the following components:

- Cartographer-Builder-Runner @ af2eedb
- Korifi @ [v0.7.1](https://github.com/cloudfoundry/korifi/tree/v0.7.1)
- TAS-Adapter-Telemetry-Controller @ 37dda46

### Security fixes

This release includes no security fixes.

### Resolved Issues

The following issues are resolved in this release:
- Resolved bugs that caused unnecessary app restarts and task reruns.
- Resolved a bug which caused the list of orgs returned by /v3/orgs to be incorrect in some circumstances.
- 404 Not Found errors from the API now comply with the Cloud Foundry error format and will be correctly handled by the CLI.

### Known Issues

This release has no known issues.

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
