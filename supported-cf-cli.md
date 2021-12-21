# Supported CF CLI

This topic describes the CF CLI that this version of Application Service Adapter can support.

* [CF Push and Its Related Flags](#cf-push-flags)
* [App Operations](#app-operations)
* [Service Operations](#service-operations)

## <a id="cf-push-flags"></a> CF Push and Its Related Flags

The basic usage of `cf push <APP-NAME>` is supported.

| Flag | Supported? | Notes |
|------|------------|-------|
|`--app-start-timeout, -t`| N | |
|`--buildpack, -b`| N | Autodetection of buildpacks is supported (flag omitted, or set to `null` or `default`);<br> User-specified buildpacks are not supported. |
|`--disk, -k`| N |  |
|`--docker-image, -o`| N |  |
|`--docker-username`| N |  |
|`--droplet`| N |  |
|`--endpoint`| N |  |
|`--health-check-type, -u`| N |  |
|`--instances, -i`| N |  |
|`--manifest, -f`| Y | Support a subset of the app manifest attribute specified in [Cloud Foundry documentation](https://docs.cloudfoundry.org/devguide/deploy-apps/manifest-attributes.html). See [supported app manifest attributes](#supported-manifest-attributes) section below. |
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

### <a id="supported-manifest-attributes"></a> Supported App Manifest App-level configuration
We support a subset of the [CF App Manifest schema](https://v3-apidocs.cloudfoundry.org/version/3.112.0/index.html#the-manifest-schema).

| Attribute | Supported? | Notes |
|------|------------|-------|
| `buildpacks` | N | |
| `command` | N | |
| `default-route` | Y | |
| `disk_quota` | N | |
| `docker` | N | |
| `env` | Y | |
| `no-route` | N | |
| `processes` | Y | See [supported Process-level attributes](#supported-manifest-attributes-processes) for more details. |
| `health-check-http-endpoint` | N | |
| `health-check-invocation-timeout` | N | |
| `health-check-type` | N | |
| `instances` | Y | |
| `memory` | N | |
| `metadata` | N | |
| `no-route` | N | |
| `path` | N | |
| `random-route` | N | |
| `routes` | Y | See [supported Route-level attributes](#supported-manifest-attributes-routes) for more details. |
| `services` | N | |
| `sidecars` | N | |
| `stack` | N | |
| `timeout` | N | |

#### <a id="supported-manifest-attributes-processes"></a> Supported App Manifest Process-level configuration
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

#### <a id="supported-manifest-attributes-routes"></a> Supported App Manifest Route-level configuration
| Attribute | Supported? | Notes |
|------|------------|-------|
| `route` | Y | |
| `protocol` | N | Only `http1` routes are supported. |

## <a id="app-operations"></a> App Operations
| Command | Supported? | Notes |
|---------|------------|-------|
|`cf apps`| Y | The default usage of `cf apps` is supported, using `--labels` to filter apps is not supported. |
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
|`cf restage` | N |     |
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
|`cf env` | N |     |
|`cf set-env` | N |     |
|`cf unset-env` | N |     |
|`cf stacks` | N |     |
|`cf stack` | N |     |
|`cf copy-source` | N |     |
|`cf create-app-manifest` | N |     |
|`cf get-health-check` | Y |     |
|`cf set-health-check` | N |     |
|`cf enable-ssh` | N |     |
|`cf disable-ssh` | N |     |
|`cf ssh-enabled` | N |     |
|`cf ssh` | N |     |

## <a id="service-operations"></a> Service Operations
Service operations are not supported.

## <a id="org-space-operations"></a> Org and Space Operations

### Orgs Operations
| Command | Supported? | Notes |
|---------|------------|-------|
|`cf orgs`| Y |   |
|`cf org`| N |   |
|`cf create-org`| Y |   |
|`cf delete-org`| N |   |
|`cf rename-org`| N |   |

### Space Operations

| Command | Supported? | Notes |
|---------|------------|-------|
|`cf spaces`| Y |   |
|`cf space`| N |   |
|`cf create-space`| Y |   |
|`cf delete-space`| N |   |
|`cf rename-space`| N |   |
|`cf apply-manifest`| Y |   |
|`cf allow-space-ssh`| N |   |
|`cf disallow-space-ssh`| N |   |
|`cf space-ssh-allowed`| N |   |

## Routes and Domains

### Routes Operations

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf routes` | Y |   |
| `cf route` | Y |   |
| `cf create-route` | Y |   |
| `cf check-route` | N |   |
| `cf map-route` | Y |   |
| `cf unmap-route` | N |   |
| `cf delete-route` | N |   |
| `cf delete-orphaned-routes` | N |   |

### Routes Operations

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf domains` | N |  |
| `cf create-private-domain` | N |  |
| `cf delete-private-domain` | N |  |
| `cf create-shared-domain` | N |  |
| `cf delete-shared-domain` | N |  |
| `cf router-groups` | N |  |
