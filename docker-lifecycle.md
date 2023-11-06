# Using the Docker Lifecycle with Application Service Adapter

By default, Cloud Foundry and Application Service Adapter will push applications using the Buildpack lifecycle. To use a pre-built Docker image for an app, specify the Docker lifecycle for the app.

To deploy a Docker image from a container registry repository (such as Dockerhub), run:

```bash
cf push APP_NAME --docker-image REPO/IMAGE:TAG
```

## Differences from Tanzu Application Service for VMs

### Platform Configuration
At the platform level, use of the Docker lifecycle is always available for Application Service Adapter and may not be restricted by a feature flag configuration option as in Tanzu Application Service.

### Docker App Limitations
As with any workload on Kubernetes, running a process as the privileged `root` user (and conventionally any user with a uid less than 1000) is restricted by default. If a Dockerfile specifies a `USER`, it must have a defined uid. You can either specify a numeric value for the `USER` or `RUN` the `useradd` command with the `--uid` option.

For example, to set the user to 'appuser' while still ensuring the uid is both defined and unprivileged, you could use the following Dockerfile:

```dockerfile
FROM ubuntu:latest
RUN useradd -r -u 1001 -g appuser appuser
USER appuser
...
```

### Port Configuration
While Tanzu Application Service for VMs expects that applications listen on the port specified by the `PORT` environment variable, Application Service Adapter expects that all applications listen on port `8080` to handle HTTP requests. Docker images may configure alternative ports via the `EXPOSE` directive in the Dockerfile. Application Service Adapter discovers this port configuration from the image and configures the default route accordingly.

When the `EXPOSE` directive is missing from the Dockerfile and the app is listening on a port other than `8080`, the following procedure can be used to configure the app route with the appropriate port:

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