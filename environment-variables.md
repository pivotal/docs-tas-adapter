# Application environment variables

This topic describes the environment variables that the Application Service Adapter for Tanzu Application Platform set when building and running an application.

## <a id='overview'></a> Overview

Environment variables are the means by which Application Service Adapter communicates with a deployed app about its environment.

For information about setting your own app-specific environment variables, see the [Environment Variable](https://docs.cloudfoundry.org/devguide/deploy-apps/manifest-attributes.html#env-block) section of the _Deploying with App Manifests_ topic.

## <a id='view-env'></a> View Environment Variables

Using the Cloud Foundry Command Line Interface (cf CLI), you can run the `cf env` command to view the Application Service Adapter environment variables for your app. The `cf env` command displays the following environment variables:

* The user-provided variables set using the `cf set-env` command

For more information about the `cf env` command, see [env](http://cli.cloudfoundry.org/en-US/cf/env.html) in the cf CLI documentation. For more information about the `cf set-env` command, see [set-env](http://cli.cloudfoundry.org/en-US/cf/set-env.html) in the cf CLI documentation.

The following example demonstrates the environment variables `cf env` displays:

<pre class="terminal">
$ cf env my-app
Getting env variables for app my-app in org my-org / space my-space as
admin...
No system-provided env variables have been set

User-Provided:
MY_DRAIN: http://drain.example.com
MY_ENV_VARIABLE: 100

No running env variables have been set

No staging env variables have been set
</pre>


## <a id='app-system-env'></a> App-Specific System Variables

This section describes the environment variables that Application Service Adapter makes available to your app container. Some of these variables are the same across instances of a single app, and some vary from instance to instance.

You can access environment variables programmatically, including variables defined by the buildpack.

The table below lists the app-specific system environment variables available to your app container. See [App-Specific System Variables](https://docs.pivotal.io/application-service/3-0/devguide/deploy-apps/environment-variable.html#app-system-env) in _TAS for VMs Environment Variables_ for more information on each environment variable.

| Environment Variable | Running | Staging |
| -------------------- | ------- | ------- |
| `CF_INSTANCE_ADDR` |  |  |
| `CF_INSTANCE_GUID` | x |  |
| `CF_INSTANCE_INDEX` | x |  |
| `CF_INSTANCE_INTERNAL_IP` | x |  |
| `CF_INSTANCE_IP` | x |  |
| `CF_INSTANCE_PORT` |  |  |
| `CF_INSTANCE_PORTS` |  |  |
| `CF_STACK` |  |  |
| `DATABASE_URL` |  |  |
| `HOME` | x |  |
| `INSTANCE_GUID` |  |  |
| `INSTANCE_INDEX` |  |  |
| `LANG` |  |  |
| `MEMORY_LIMIT` |  |  |
| `PATH` | x |  |
| `PORT` | x |  |
| `PWD` | x |  |
| `TMPDIR` |  |  |
| `USER` |  |  |
| `VCAP_APP_HOST` | x |  |
| `VCAP_APP_PORT` | x |  |
| `VCAP_APPLICATION` |  |  |
| `VCAP_SERVICES` | x |  |

### <a id='CF-INSTANCE-GUID'></a> CF_INSTANCE_GUID

The UUID of the app instance.

For example: `CF_INSTANCE_GUID=41653aa4-3a3a-486a-4431-ef258b39f042`

### <a id='CF-INSTANCE-INDEX'></a> CF_INSTANCE_INDEX

The index number of the app instance.

For example: `CF_INSTANCE_INDEX=0`

### <a id='CF-INSTANCE-INTERNAL-IP'></a> CF_INSTANCE_INTERNAL_IP

The internal IP address of the container running the app instance.

For example: `CF_INSTANCE_INTERNAL_IP=5.6.7.8`

### <a id='CF-INSTANCE-IP'></a> CF_INSTANCE_IP

The external IP address of the host running the app instance.

For example: `CF_INSTANCE_IP=1.2.3.4`

### <a id='HOME'></a> HOME

The root folder for the deployed app.

For example: `HOME=/home/vcap/app`

### <a id='PORT'></a> PORT

The port on which the app should listen for requests. Application Service Adapter allocates a port dynamically for each instance of the app, so code that obtains or uses the app port should refer to it using the `PORT` environment variable.

For example: `PORT=8080`

### <a id='PWD'></a> PWD

The present working directory where the buildpack that processed the app ran.

For example: `PWD=/home/vcap/app`

### <a id='VCAP-APP-HOST'></a> VCAP_APP_HOST

Deprecated. Always set to `0.0.0.0`.

### <a id='VCAP-APP-PORT'></a> VCAP_APP_PORT

Deprecated name for the `PORT` variable.

### <a id='VCAP-SERVICES'></a> VCAP_SERVICES

For bindable services, Application Service Adapter adds connection details to the `VCAP_SERVICES` environment variable when you restart your app, after binding a service instance to your app. For more information about bindable services, see [Services Overview](https://docs.cloudfoundry.org/devguide/services/).

Application Service Adapter returns the results as a JSON document that contains an object for each service for which one or more instances are bound to the app. The service object contains a child object for each instance of the service that is bound to the app.

The table below defines the attributes that describe a bound service. The key for each service in the JSON document is the same as the value of the "label" attribute.

| Attribute | Description |
| --------- | ----------- |
| `binding_guid` | The guid of the service binding. |
| `binding_name` | The name assigned to the service binding by the user. |
| `instance_guid` | The guid of the service instance. |
| `instance_name` | The name assigned to the service instance by the user. |
| `name` | The `binding_name`, if it exists. Otherwise, the `instance_name`. |
| `label` | The name of the service offering. |
| `tags` | An array of strings an app can use to identify a service instance. |
| `plan` | The service plan selected when the service instance was created. |
| `credentials` | A JSON object containing the service-specific credentials needed to access the service instance. |
| `syslog_drain_url` | The service-specific syslog drain url. |
| `volume_mounts` | An array of service-specific volume mounts. |


To see the value of the `VCAP_SERVICES` environment variable for an app pushed to Application Service Adapter, see [View Environment Variable Values](#view-env).

The example below shows the value of the `VCAP_SERVICES` environment variable for bound instances of several services available in the Marketplace.

~~~
VCAP_SERVICES=
{
  "elephantsql": [
    {
      "name": "elephantsql-binding-c6c60",
      "binding_guid": "44ceb72f-100b-4f50-87a2-7809c8b42b8d",
      "binding_name": "elephantsql-binding-c6c60",
      "instance_guid": "391308e8-8586-4c42-b464-c7831aa2ad22",
      "instance_name": "elephantsql-c6c60",
      "label": "elephantsql",
      "tags": [
        "postgres",
        "postgresql",
        "relational"
      ],
      "plan": "turtle",
      "credentials": {
        "uri": "postgres://exampleuser:examplepass@babar.elephantsql.com:5432/exampleuser"
      },
      "syslog_drain_url": null,
      "volume_mounts": []
    }
  ],
  "sendgrid": [
    {
      "name": "mysendgrid",
      "binding_guid": "6533b1b6-7916-488d-b286-ca33d3fa0081",
      "binding_name": null,
      "instance_guid": "8c907d0f-ec0f-44e4-87cf-e23c9ba3925d",
      "instance_name": "mysendgrid",
      "label": "sendgrid",
      "tags": [
        "smtp"
      ],
      "plan": "free",
      "credentials": {
        "hostname": "smtp.sendgrid.net",
        "username": "QvsXMbJ3rK",
        "password": "HCHMOYluTv"
      },
      "syslog_drain_url": null,
      "volume_mounts": []
    }
  ]
}
~~~
