# Using the Docker lifecycle with Application Service Adapter

By default, Cloud Foundry and Application Service Adapter uses the Buildpack lifecycle to push applications. To use a pre-built Docker image for an app, specify the Docker lifecycle for the app.

To deploy a Docker image from a container image registry repository, such as Docker Hub, run:

```bash
cf push APP_NAME --docker-image REPO/IMAGE:TAG
```

## <a id="differences-from-tas"></a>Differences from Tanzu Application Service

### <a id="platform-config"></a>Platform configuration

At the platform level, use of the Docker lifecycle is always available for Application Service Adapter and is not restricted by a feature flag configuration option as in Tanzu Application Service.

### <a id="docker-limitations"></a>Docker app limitations

As with any workload on Kubernetes, running a process as the privileged `root` user (and conventionally any user with a UID less than 1000) is restricted by default. If a Dockerfile specifies a `USER`, it must have a defined UID. You can either specify a numeric value for the `USER` or `RUN` the `useradd` command with the `--uid` option.

For example, to set the user to "appuser" while still ensuring the UID is both defined and unprivileged, you could use the following Dockerfile:

```dockerfile
FROM ubuntu:latest
RUN useradd -r -u 1001 -g appuser appuser
USER appuser
...
```

### <a id="port-config"></a>Port configuration

Tanzu Application Service expects that applications listen on the port specified by the `PORT` environment variable. Application Service Adapter, however, expects that all applications listen on port `8080` to handle HTTP requests. Docker images can configure alternative ports through the `EXPOSE` directive in the Dockerfile. Application Service Adapter discovers this port configuration from the image and configures the default route accordingly.

When the `EXPOSE` directive is missing from the Dockerfile, and the app is listening on a port other than `8080`, you can use the following procedure to configure the app route with the appropriate port:

```bash
APP_NAME=<your app name>
DOMAIN=<the domain name>
IMAGE=<repo/image:tag>
DESIRED_PORT=<app port>

cf push $APP_NAME --no-route --docker-image $IMAGE
cf create-route $DOMAIN --hostname $APP_NAME
route_guid=$(cf curl /v3/routes | jq -r ".resources[] | select(.host==\"$APP_NAME\").guid")
read -r -d '' destination <<EOF
{
   "destinations": [
   {
     "app": {
       "guid": "$(cf app $APP_NAME --guid)",
       "process": {
         "type": "web"
       }
     },
     "port": $DESIRED_PORT,
     "protocol": "http1"
   }
 ]
}
EOF
cf curl -XPOST "/v3/routes/$route_guid/destinations" -d "$destination"
```
