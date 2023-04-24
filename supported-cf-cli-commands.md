# Supported cf CLI commands

This topic describes the Cloud Foundry command-line interface (cf CLI) commands and options that this version of Application Service Adapter supports:

* [Getting started](#getting-started)
* [cf push](#cf-push)
* [App operations](#app-operations)
* [Org and space operations](#org-space-operations)
* [Route and domain operations](#route-domain-operations)
* [Service operations](#service-operations)
* [Metadata operations](#metadata-operations)
* [User role management](#role-management)

Application Service Adapter supports the following cf CLI commands by providing
a subset of the endpoints of the v3 Cloud Foundry
API through its API server. For more
information, see [the Cloud Foundry documentation](https://v3-apidocs.cloudfoundry.org/).

**Only the listed commands are supported**. Other commands may fail or behave incorrectly.

## <a id="getting-started"></a> Getting started

| Command | Supported? | Notes |
|---------|------------|-------|
|`cf login`| Y | Using the `--sso` flag is not supported. |
|`cf logout` | Y |     |
|`cf target` | Y |     |
|`cf passwd` | N |    |
|`cf api` | Y |     |
|`cf auth` | Y | Use the name of a user authentication info entry from the local Kubeconfig file as the username argument. The password field is ignored. |


## <a id="cf-push"></a> cf push

Application Service Adapter supports the basic use of `cf push APP-NAME`, where `APP-NAME` is the name of your app.

| Flag | Supported? | Notes |
|------|------------|-------|
|`--app-start-timeout, -t`| Y | |
|`--buildpack, -b`| N | Automatic buildpack detection is supported. Omit the flag or set to `null` or `default`.<br/>User-specified buildpacks are not supported. |
|`--disk, -k`| Y |  |
|`--docker-image, -o`| N |  |
|`--docker-username`| N |  |
|`--droplet`| N |  |
|`--endpoint`| N |  |
|`--health-check-type, -u`| Y |  |
|`--instances, -i`| Y |  |
|`--manifest, -f`| Y | Some manifest configurations described in [App Manifest Attribute Reference](https://docs.cloudfoundry.org/devguide/deploy-apps/manifest-attributes.html) are supported. See the [Supported manifest configuration](#supported-manifest-configuration) section. |
|`--memory, -m`| Y |  |
|`--no-manifest`| Y |  |
|`--no-route`| Y | |
|`--no-start`| Y |  |
|`--no-wait`| N |  |
|`--path, -p`| Y |  |
|`--random-route`| Y |  |
|`--stack, -s`| N |  |
|`--start-command, -c`| Y |  |
|`--strategy`| N |  |
|`--task`| Y |  |
|`--var`| Y |  |
|`--vars-file`| Y |  |

### <a id="supported-manifest-configuration"></a> Supported manifest configuration

The Application Service Adapter supports a subset of the Cloud Foundry manifest schema. For more information, see [The manifest schema](https://v3-apidocs.cloudfoundry.org/version/3.112.0/index.html#the-manifest-schema) in the Cloud Foundry API documentation.

#### <a id="supported-app-level-configuration"></a> Supported app-level configuration

| Attribute | Supported? | Notes |
|------|------------|-------|
| `buildpacks` | N | |
| `command` | Y | |
| `default-route` | Y | |
| `disk_quota` | Y | |
| `docker` | N | |
| `env` | Y | |
| `health-check-http-endpoint` | Y | |
| `health-check-invocation-timeout` | Y | |
| `health-check-type` | N | |
| `instances` | Y | |
| `memory` | Y | |
| `metadata` | N | |
| `no-route` | Y | |
| `path` | N | |
| `processes` | Y | See the [Supported process-level configuration](#supported-process-level-configuration) section. |
| `random-route` | Y | |
| `routes` | Y | See the [Supported route-level configuration](#supported-route-level-configuration) section. |
| `services` | N | |
| `sidecars` | N | |
| `stack` | N | |
| `timeout` | Y | |

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
|`cf run-task` | Y | Only the `-c` parameter is supported. |
|`cf tasks` | Y ||
|`cf terminate-task` | Y ||
|`cf packages` | Y |     |
|`cf create-package` | Y |     |
|`cf droplets` | N |     |
|`cf set-droplet` | Y |     |
|`cf download-droplet` | N |     |
|`cf events` | N |     |
|`cf logs` | Y |     |
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

| Command             | Supported? | Notes |
|---------------------|------------|-------|
| `cf orgs`           | Y          |   |
| `cf org`            | Y          | Using the `--guid` flag to retrieve the GUID of the org is supported.<br/>Using the command without the `--guid` flag is not supported.  |
| `cf create-org`     | Y          |   |
| `cf delete-org`     | Y          |   |
| `cf rename-org`     | N          |   |
| `cf set-org-role`   | Y     |   |
| `cf unset-org-role` | Y     |   |

### Space operations

| Command                 | Supported? | Notes |
|-------------------------|------------|-------|
| `cf spaces`             | Y |   |
| `cf space`              | Y | Using the `--guid` flag to retrieve the GUID of the space is supported.<br/>Using the command without the `--guid` flag is not supported. |
| `cf create-space`       | Y |   |
| `cf delete-space`       | Y |   |
| `cf rename-space`       | N |   |
| `cf apply-manifest`     | Y |   |
| `cf allow-space-ssh`    | N |   |
| `cf disallow-space-ssh` | N |   |
| `cf space-ssh-allowed`  | N |   |
| `cf set-space-role`     | Y     |   |
| `cf unset-space-role`   | Y     |   |


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
| `cf unmap-route` | Y |   |
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


## <a id="metadata-operations"></a> Metadata operations

This section describes the metadata operations that Application Service Adapter supports.

| Command | Supported? | Notes |
|---------|------------|-------|
| `cf labels` | Y | The `app`, `org`, and `space` resources are supported. |
| `cf set-label` | Y | The `app`, `org`, and `space` resources are supported. |
| `cf unset-label` | Y | The `app`, `org`, and `space` resources are supported. |

## <a id="role-management"></a> User Role Management

This section describes the operations for adding and removing roles that Application Service Adapter supports.
These commands work for both Kubernetes users and Kubernetes service accounts. When assigning roles to [Kubernetes service accounts](https://kubernetes.io/docs/concepts/security/service-accounts/),
specify the user name in the format `system:serviceaccount:<ServiceAccountNamespace>:<ServiceAccountName>`, with
`<ServiceAccountNamespace>` and `<ServiceAccountName>` replaced with the actual values.

| Command               | Supported? | Notes                                                       |
| --------------------- | ---------- |-------------------------------------------------------------|
| `cf set-org-role`     | N          | The command succeeds, but there are no supported org roles. |
| `cf unset-org-role`   | N          | The command succeeds, but there are no supported org roles. |
| `cf set-space-role`   | Y          | Supported roles: `SpaceDeveloper`                           |
| `cf unset-space-role` | Y          | Supported roles: `SpaceDeveloper`                           |
