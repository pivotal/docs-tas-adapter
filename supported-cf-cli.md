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
|`--buildpack, -b`| Y | Support autodetection of the buildpack (flag omitted, or set to `null` or `default`); <br> Support a single user-specified buildpack by name (flag set to name of buildpack);<br> Not supported: multiple named buildpacks; <br> Not supported: custom buildpacks as URLs |
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
|`--no-route`| Y | The application is deployed without a route with or without this flag, due to limitations of Kubernetes. You will need to use `cf map-route` to manually assign the route the app once it's deployed.|
|`--no-start`| Y |  |
|`--no-wait`| N |  |
|`--path, -p`| N |  |
|`--random-route`| N |  |
|`--stack, -s`| N |  |
|`--start-command, -c`| N |  |
|`--strategy`| N |  |
|`--task`| N |  |
|`--var`| N | We don't plan to support setting environment variables during `cf push`. The environment variables should be set in the app manifest file.  |
|`--vars-file`| N |  |

### <a id="supported-manifest-attributes"></a> Supported App Manifest Attributes





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
|`cf restart` | N |     |
|`cf stage-package` | N |     |
|`cf restage` | N |     |
|`cf restart-app-instance` | N |     |
|`cf run-task` | N |     |
|`cf tasks` | N |     |
|`cf terminate-task` | N |     |
|`cf packages` | N |     |
|`cf create-package` | N |     |
|`cf droplets` | N |     |
|`cf set-droplet` | N |     |
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
|`cf get-health-check` | N |     |
|`cf set-health-check` | N |     |
|`cf enable-ssh` | N |     |
|`cf disable-ssh` | N |     |
|`cf ssh-enabled` | N |     |
|`cf ssh` | N |     |

## <a id="service-operations"></a> Service Operations
Service operations is not supported as of this release. We plan to implement services related features after GA.

## <a id="org-space-operations"></a> Orgs and Spaces Operations

### Orgs Operations
| Command | Supported? | Notes |
|---------|------------|-------|
|`cf orgs`| Y |   | 
|`cf org`| Y |   | 
|`cf create-org`| Y |   | 
|`cf delete-org`| Y |   | 
|`cf rename-org`| Y |   | 

### Space Operations

| Command | Supported? | Notes |
|---------|------------|-------|
|`cf spaces`| Y |   | 
|`cf space`| Y |   | 
|`cf create-space`| Y |   | 
|`cf delete-space`| Y |   | 
|`cf rename-space`| Y |   | 
|`cf apply-manifest`| Y |   | 
|`cf allow-space-ssh`| N |   | 
|`cf disallow-space-ssh`| N |   | 
|`cf space-ssh-allowed`| N |   | 

## Routes and Domains

...