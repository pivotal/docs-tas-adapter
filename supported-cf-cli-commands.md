# Supported cf CLI commands

This topic describes the Cloud Foundry command-line interface (cf CLI) commands and options that this version of the Application Service Adapter supports:

* [cf push](#cf-push)
* [App operations](#app-operations)
* [Org and space operations](#org-space-operations)
* [Route and domain operations](#route-domain-operations)
* [Service operations](#service-operations)

## <a id="cf-push"></a> cf push

Application Service Adapter supports the basic use of `cf push APP-NAME`, where `APP-NAME` is the name of your app.

| Flag | Supported? | Notes |
|------|------------|-------|
|`--app-start-timeout, -t`| N | |
|`--buildpack, -b`| N | Automatic buildpack detection is supported. Omit the flag or set to `null` or `default`.<br/>User-specified buildpacks are not supported. |
|`--disk, -k`| N |  |
|`--docker-image, -o`| N |  |
|`--docker-username`| N |  |
|`--droplet`| N |  |
|`--endpoint`| N |  |
|`--health-check-type, -u`| N |  |
|`--instances, -i`| N |  |
|`--manifest, -f`| Y | Some manifest configurations described in [App Manifest Attribute Reference](https://docs.cloudfoundry.org/devguide/deploy-apps/manifest-attributes.html) are supported. See the [Supported manifest configuration](#supported-manifest-configuration) section. |
|`--memory, -m`| N |  |
|`--no-manifest`| Y |  |
|`--no-route`| N | |
|`--no-start`| Y |  |
|`--no-wait`| N |  |
|`--path, -p`| N |  |
|`--random-route`| N |  |
|`--stack, -s`| N |  |
|`--start-command, -c`| N |  |
|`--strategy`| N |  |
|`--task`| N |  |
|`--var`| N |  |
|`--vars-file`| N |  |

### <a id="supported-manifest-configuration"></a> Supported manifest configuration

The Application Service Adapter supports a subset of the Cloud Foundry manifest schema. For more information, see [The manifest schema](https://v3-apidocs.cloudfoundry.org/version/3.112.0/index.html#the-manifest-schema) in the Cloud Foundry API documentation.

#### <a id="supported-app-level-configuration"></a> Supported app-level configuration

| Attribute | Supported? | Notes |
|------|------------|-------|
| `buildpacks` | N | |
| `command` | N | |
| `default-route` | Y | |
| `disk_quota` | N | |
| `docker` | N | |
| `env` | Y | |
| `no-route` | N | |
| `processes` | Y | See the [Supported process-level configuration](#supported-process-level-configuration) section. |
| `health-check-http-endpoint` | N | |
| `health-check-invocation-timeout` | N | |
| `health-check-type` | N | |
| `instances` | Y | |
| `memory` | N | |
| `metadata` | N | |
| `no-route` | N | |
| `path` | N | |
| `random-route` | N | |
| `routes` | Y | See the [Supported route-level configuration](#supported-route-level-configuration) section. |
| `services` | N | |
| `sidecars` | N | |
| `stack` | N | |
| `timeout` | N | |

#### <a id="supported-process-level-configuration"></a> Supported process-level configuration

| Attribute | Supported? | Notes |
|------|------------|-------|
| `type` | Y | |
| `command` | Y | |
| `disk_quota` | Y | |
| `docker` | N | |
| `health-check-http-endpoint` | Y | |
| `health-check-invocation-timeout` | Y | |
| `health-check-type` | Y | |
| `instances` | Y | |
| `memory` | Y | |
| `timeout` | Y | |

#### <a id="supported-route-level-configuration"></a> Supported route-level configuration

| Attribute | Supported? | Notes |
|------|------------|-------|
| `route` | Y | |
| `protocol` | N | Only `http1` routes are supported. |


## <a id="app-operations"></a> App operations

| Command | Supported? | Notes |
|---------|------------|-------|
|`cf apps`| Y | The default usage of `cf apps` is supported.<br />Using `--labels` to filter apps is not supported. |
|`cf app` | Y |     |
|`cf create-app` | Y |     |
|`cf scale` | Y |     |
|`cf delete` | Y |     |
|`cf rename` | N |     |
|`cf cancel-deployment` | N |     |
|`cf start` | Y |     |
|`cf stop` | Y |     |
|`cf restart` | Y |     |
|`cf stage-package` | Y |     |
|`cf restage` | Y | Using `--strategy` flag is not supported.    |
|`cf restart-app-instance` | N |     |
|`cf run-task` | N |     |
|`cf tasks` | N |     |
|`cf terminate-task` | N |     |
|`cf packages` | Y |     |
|`cf create-package` | Y |     |
|`cf droplets` | N |     |
|`cf set-droplet` | Y |     |
|`cf download-droplet` | N |     |
|`cf events` | N |     |
|`cf logs` | N |     |
|`cf env` | Y | Fetching `system-provided`, `running`, and `staging` environment variables are not supported.   |
|`cf set-env` | Y |     |
|`cf unset-env` | Y |     |
|`cf stacks` | N |     |
|`cf stack` | N |     |
|`cf copy-source` | N |     |
|`cf create-app-manifest` | N |     |
|`cf get-health-check` | Y |     |
|`cf set-health-check` | Y |     |
|`cf enable-ssh` | N |     |
|`cf disable-ssh` | N |     |
|`cf ssh-enabled` | N |     |
|`cf ssh` | N |     |


## <a id="org-space-operations"></a> Org and space operations

This section describes the org and space operations that Application Service Adapter supports.

### Org operations

| Command | Supported? | Notes |
|---------|------------|-------|
|`cf orgs`| Y |   |
|`cf org`| Y | Using the `--guid` flag to retrieve the GUID of the org is supported.<br/>Using the command without the `--guid` flag is not supported.  |
|`cf create-org`| Y |   |
|`cf delete-org`| Y |   |
|`cf rename-org`| N |   |

### Space operations

| Command | Supported? | Notes |
|---------|------------|-------|
|`cf spaces`| Y |   |
|`cf space`| Y | Using the `--guid` flag to retrieve the GUID of the space is supported.<br/>Using the command without the `--guid` flag is not supported. |
|`cf create-space`| Y |   |
|`cf delete-space`| Y |   |
|`cf rename-space`| N |   |
|`cf apply-manifest`| Y |   |
|`cf allow-space-ssh`| N |   |
|`cf disallow-space-ssh`| N |   |
|`cf space-ssh-allowed`| N |   |


## <a id="route-domain-operations"></a> Route and domain operations

This section describes the route and domain operations that Application Service Adapter supports.

### Route operations

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf routes` | Y |   |
| `cf route` | Y |   |
| `cf create-route` | Y |   |
| `cf check-route` | N |   |
| `cf map-route` | Y |   |
| `cf unmap-route` | N |   |
| `cf delete-route` | Y |   |
| `cf delete-orphaned-routes` | N |   |

### Domain operations

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf domains` | Y |  |
| `cf create-private-domain` | N |  |
| `cf delete-private-domain` | N |  |
| `cf create-shared-domain` | N |  |
| `cf delete-shared-domain` | N |  |
| `cf router-groups` | N |  |


## <a id="service-operations"></a> Service operations

This section describes the service operations that Application Service Adapter supports.

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf marketplace` | N |  |
| `cf services` | Y |  |
| `cf service` | Y |  |
| `cf create-service` | N |  |
| `cf update-service` | N |  |
| `cf upgrade-service` | N |  |
| `cf delete-service` | Y |  |
| `cf rename-service` | N |  |
| `cf create-service-key` | N |  |
| `cf bind-service` | Y |  |
| `cf unbind-service` | Y |  |
| `cf delete-service-key` | N |  |
| `cf bind-route-service` | N |  |
| `cf unbind-route-service` | N |  |
| `cf create-user-provided-service` | Y |  |
| `cf update-user-provided-service` | N |  |
| `cf share-service` | N |  |
| `cf unshare-service` | N |  |
