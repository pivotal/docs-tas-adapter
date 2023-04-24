# Supported cf CLI commands

This topic describes the Cloud Foundry command-line interface (cf CLI) commands
and options that this version of Application Service Adapter supports.

Application Service Adapter supports the following cf CLI commands by providing
a subset of the endpoints of the v3 Cloud Foundry API (CAPI) through its API server.
For more information, see [the Cloud Foundry
documentation](https://v3-apidocs.cloudfoundry.org/).

**Only the listed commands are supported**. Other commands might fail or behave
incorrectly.

## <a id="getting-started"></a> Getting started

| Command | Supported? | Notes |
|---------|------------|-------|
|`cf login`| Y | Using the `--sso` flag is not supported. |
|`cf logout` | Y |   _n/a_  |
|`cf target` | Y |  _n/a_   |
|`cf passwd` | N |  _n/a_  |
|`cf api` | Y |  _n/a_   |
|`cf auth` | Y | Use the name of a user authentication information entry from the local Kubeconfig file as the username argument. The password field is ignored. |


## <a id="cf-push"></a> The `cf push` command

Application Service Adapter supports the basic use of `cf push APP-NAME`, where
`APP-NAME` is the name of your app.

| Flag | Supported? | Notes |
|------|------------|-------|
|`--app-start-timeout, -t`| Y |_n/a_ |
|`--buildpack, -b`| N | Automatic buildpack detection is supported. Omit the flag or set to `null` or `default`.<br/>User-specified buildpacks are not supported. |
|`--disk, -k`| Y | _n/a_ |
|`--docker-image, -o`| N | _n/a_ |
|`--docker-username`| N | _n/a_ |
|`--droplet`| N | _n/a_ |
|`--endpoint`| N |_n/a_  |
|`--health-check-type, -u`| Y |_n/a_  |
|`--instances, -i`| Y | _n/a_ |
|`--manifest, -f`| Y | Some manifest configurations described in [App Manifest Attribute Reference](https://docs.cloudfoundry.org/devguide/deploy-apps/manifest-attributes.html) are supported. See the [Supported manifest configuration](#supported-manifest-configuration) section. |
|`--memory, -m`| Y | _n/a_ |
|`--no-manifest`| Y | _n/a_ |
|`--no-route`| Y |_n/a_ |
|`--no-start`| Y | _n/a_ |
|`--no-wait`| N | _n/a_ |
|`--path, -p`| Y | _n/a_ |
|`--random-route`| Y | _n/a_ |
|`--stack, -s`| N | _n/a_ |
|`--start-command, -c`| Y |_n/a_  |
|`--strategy`| N | _n/a_ |
|`--task`| Y | _n/a_ |
|`--var`| Y | _n/a_ |
|`--vars-file`| Y | _n/a_ |

### <a id="supported-manifest-configuration"></a> Supported manifest configuration

The Application Service Adapter supports a subset of the Cloud Foundry manifest
schema. For more information, see [The manifest
schema](https://v3-apidocs.cloudfoundry.org/version/3.112.0/index.html#the-manifest-schema)
in the CAPI documentation.

#### <a id="supported-app-level-configuration"></a> Supported app-level configuration

| Attribute | Supported? | Notes |
|------|------------|-------|
| `buildpacks` | N |_n/a_ |
| `command` | Y | _n/a_|
| `default-route` | Y |_n/a_ |
| `disk_quota` | Y |_n/a_ |
| `docker` | N |_n/a_ |
| `env` | Y |_n/a_ |
| `health-check-http-endpoint` | Y | _n/a_|
| `health-check-invocation-timeout` | Y |_n/a_ |
| `health-check-type` | N |_n/a_ |
| `instances` | Y |_n/a_ |
| `memory` | Y |_n/a_ |
| `metadata` | N |_n/a_ |
| `no-route` | Y |_n/a_ |
| `path` | N |_n/a_ |
| `processes` | Y | See the [Supported process-level configuration](#supported-process-level-configuration) section. |
| `random-route` | Y |_n/a_ |
| `routes` | Y | See the [Supported route-level configuration](#supported-route-level-configuration) section. |
| `services` | N |_n/a_ |
| `sidecars` | N |_n/a_ |
| `stack` | N |_n/a_ |
| `timeout` | Y |_n/a_ |

#### <a id="supported-process-level-configuration"></a> Supported process-level configuration

| Attribute | Supported? | Notes |
|------|------------|-------|
| `type` | Y | _n/a_|
| `command` | Y |_n/a_ |
| `disk_quota` | Y | _n/a_|
| `docker` | N |_n/a_ |
| `health-check-http-endpoint` | Y |_n/a_ |
| `health-check-invocation-timeout` | Y |_n/a_ |
| `health-check-type` | Y |_n/a_ |
| `instances` | Y |_n/a_ |
| `memory` | Y |_n/a_ |
| `timeout` | Y |_n/a_ |

#### <a id="supported-route-level-configuration"></a> Supported route-level configuration

| Attribute | Supported? | Notes |
|------|------------|-------|
| `route` | Y |_n/a_ |
| `protocol` | N | Only `http1` routes are supported. |


## <a id="app-operations"></a> App operations

| Command | Supported? | Notes |
|---------|------------|-------|
|`cf apps`| Y | The default usage of `cf apps` is supported.<br />Using `--labels` to filter apps is not supported. |
|`cf app` | Y |   _n/a_  |
|`cf create-app` | Y |  _n/a_   |
|`cf scale` | Y |  _n/a_   |
|`cf delete` | Y |  _n/a_   |
|`cf rename` | N |  _n/a_   |
|`cf cancel-deployment` | N |  _n/a_   |
|`cf start` | Y |  _n/a_   |
|`cf stop` | Y |  _n/a_   |
|`cf restart` | Y |  _n/a_   |
|`cf stage-package` | Y |  _n/a_   |
|`cf restage` | Y | Using `--strategy` flag is not supported.    |
|`cf restart-app-instance` | N | _n/a_    |
|`cf run-task` | Y | Only the `-c` parameter is supported. |
|`cf tasks` | Y |_n/a_|
|`cf terminate-task` | Y |_n/a_|
|`cf packages` | Y |  _n/a_   |
|`cf create-package` | Y |  _n/a_   |
|`cf droplets` | N |  _n/a_   |
|`cf set-droplet` | Y | _n/a_    |
|`cf download-droplet` | N |  _n/a_   |
|`cf events` | N | _n/a_    |
|`cf logs` | Y |  _n/a_   |
|`cf env` | Y | Fetching `system-provided`, `running`, and `staging` environment variables are not supported.   |
|`cf set-env` | Y |  _n/a_   |
|`cf unset-env` | Y | _n/a_    |
|`cf stacks` | N | _n/a_    |
|`cf stack` | N |  _n/a_   |
|`cf copy-source` | N |  _n/a_   |
|`cf create-app-manifest` | N |  _n/a_   |
|`cf get-health-check` | Y |   _n/a_  |
|`cf set-health-check` | Y |   _n/a_  |
|`cf enable-ssh` | N |  _n/a_   |
|`cf disable-ssh` | N |  _n/a_   |
|`cf ssh-enabled` | N |  _n/a_   |
|`cf ssh` | N |  _n/a_   |

## <a id="org-space-operations"></a> Org and space operations

This section describes the org and space operations that Application Service
Adapter supports.

### Org operations

| Command             | Supported? | Notes |
|---------------------|------------|-------|
| `cf orgs`           | Y          | _n/a_  |
| `cf org`            | Y          | Using the `--guid` flag to retrieve the GUID of the org is supported.<br/>Using the command without the `--guid` flag is not supported.  |
| `cf create-org`     | Y          | _n/a_  |
| `cf delete-org`     | Y          | _n/a_  |
| `cf rename-org`     | N          | _n/a_  |
| `cf set-org-role`   | Y     | _n/a_  |
| `cf unset-org-role` | Y     | _n/a_  |

### Space operations

| Command                 | Supported? | Notes |
|-------------------------|------------|-------|
| `cf spaces`             | Y | _n/a_  |
| `cf space`              | Y | Using the `--guid` flag to retrieve the GUID of the space is supported.<br/>Using the command without the `--guid` flag is not supported. |
| `cf create-space`       | Y | _n/a_  |
| `cf delete-space`       | Y | _n/a_  |
| `cf rename-space`       | N | _n/a_  |
| `cf apply-manifest`     | Y | _n/a_  |
| `cf allow-space-ssh`    | N |  _n/a_ |
| `cf disallow-space-ssh` | N | _n/a_  |
| `cf space-ssh-allowed`  | N | _n/a_  |
| `cf set-space-role`     | Y     | _n/a_  |
| `cf unset-space-role`   | Y     | _n/a_  |


## <a id="route-domain-operations"></a> Route and domain operations

This section describes the route and domain operations that Application Service
Adapter supports.

### Route operations

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf routes` | Y | _n/a_  |
| `cf route` | Y | _n/a_  |
| `cf create-route` | Y | _n/a_  |
| `cf check-route` | N | _n/a_  |
| `cf map-route` | Y | _n/a_  |
| `cf unmap-route` | Y | _n/a_  |
| `cf delete-route` | Y |  _n/a_ |
| `cf delete-orphaned-routes` | N | _n/a_  |

### Domain operations

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf domains` | Y | _n/a_ |
| `cf create-private-domain` | N | _n/a_ |
| `cf delete-private-domain` | N |_n/a_  |
| `cf create-shared-domain` | N |_n/a_  |
| `cf delete-shared-domain` | N | _n/a_ |
| `cf router-groups` | N | _n/a_ |


## <a id="service-operations"></a> Service operations

This section describes the service operations that Application Service Adapter
supports.

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf marketplace` | N | _n/a_ |
| `cf services` | Y | _n/a_ |
| `cf service` | Y | _n/a_ |
| `cf create-service` | N | _n/a_ |
| `cf update-service` | N |_n/a_  |
| `cf upgrade-service` | N | _n/a_ |
| `cf delete-service` | Y | _n/a_ |
| `cf rename-service` | N | _n/a_ |
| `cf create-service-key` | N | _n/a_ |
| `cf bind-service` | Y | _n/a_ |
| `cf unbind-service` | Y | _n/a_ |
| `cf delete-service-key` | N | _n/a_ |
| `cf bind-route-service` | N |_n/a_ |
| `cf unbind-route-service` | N | _n/a_ |
| `cf create-user-provided-service` | Y | _n/a_ |
| `cf update-user-provided-service` | N | _n/a_ |
| `cf share-service` | N |_n/a_  |
| `cf unshare-service` | N |_n/a_  |


## <a id="metadata-operations"></a> Metadata operations

This section describes the metadata operations that Application Service Adapter
supports.

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf labels` | Y | The `app`, `org`, and `space` resources are supported. |
| `cf set-label` | Y | The `app`, `org`, and `space` resources are supported. |
| `cf unset-label` | Y | The `app`, `org`, and `space` resources are supported. |

## <a id="role-management"></a> User Role Management

This section describes the operations for adding and removing roles that
Application Service Adapter supports. These commands work for both Kubernetes
users and Kubernetes service accounts. When assigning roles to [Kubernetes
service
accounts](https://kubernetes.io/docs/concepts/security/service-accounts/),
specify the user name in the format
`system:serviceaccount:<ServiceAccountNamespace>:<ServiceAccountName>`, with
`<ServiceAccountNamespace>` and `<ServiceAccountName>` replaced with the actual
values.

| Command               | Supported? | Notes                                                       |
| --------------------- | ---------- |-------------------------------------------------------------|
| `cf set-org-role`     | N          | The command succeeds, but there are no supported org roles. |
| `cf unset-org-role`   | N          | The command succeeds, but there are no supported org roles. |
| `cf set-space-role`   | Y          | Supported roles: `SpaceDeveloper`                           |
| `cf unset-space-role` | Y          | Supported roles: `SpaceDeveloper`                           |
