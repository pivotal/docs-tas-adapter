# Application environment variables

This topic describes the environment variables that the Application Service Adapter for Tanzu Application Platform set when building and running an application.

## <a id='overview'></a> Overview

Environment variables are the means by which Application Service Adapter communicates with a deployed app about its environment.

For information about setting your own app-specific environment variables, see the [Environment Variable](https://docs.cloudfoundry.org/devguide/deploy-apps/manifest-attributes.html#env-block) section of the _Deploying with App Manifests_ topic.

## <a id='view-env'></a> View environment variables

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


## <a id='app-system-env'></a> App-specific system variables

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

For example: `HOME=/home/cnb`

### <a id='PORT'></a> PORT

The port on which the app should listen for requests. Application Service Adapter allocates a port dynamically for each instance of the app, so code that obtains or uses the app port should refer to it using the `PORT` environment variable.

For example: `PORT=8080`

### <a id='PWD'></a> PWD

The present working directory where the buildpack that processed the app ran.

For example: `PWD=/workspace`

### <a id='VCAP-APP-HOST'></a> VCAP_APP_HOST

Deprecated. Always set to `0.0.0.0`.

### <a id='VCAP-APP-PORT'></a> VCAP_APP_PORT

Deprecated name for the `PORT` variable.

### <a id='VCAP-SERVICES'></a> VCAP_SERVICES

Application Service Adapter has support for user-provided service instances and
adds their binding details to the `VCAP_SERVICES` environment variable.
For more information, see [User-Provided Service
Instances](https://docs.cloudfoundry.org/devguide/services/user-provided.html)
in the Cloud Foundry documentation.

Application Service Adapter returns the results as a JSON document that contains an object for each service for which one or more instances are bound to the app. The service object contains a child object for each instance of the service that is bound to the app.

The table below defines the attributes that describe a bound service. The key for each service in the JSON document is the same as the value of the `label` attribute.

> **Note** Application Service Adapter does not support managed services, so the `label` for a user-provided service instance is always `user-provided`. VMware recommends that apps find connection details through the user-settable `tags` field when parsing `VCAP_SERVICES`.

| Attribute          | Description                                                                                      |
| ------------------ | ------------------------------------------------------------------------------------------------ |
| `binding_guid`     | The GUID of the service binding.                                                                 |
| `binding_name`     | The name assigned to the service binding by the user.                                            |
| `instance_guid`    | The GUID of the service instance.                                                                |
| `instance_name`    | The name assigned to the service instance by the user.                                           |
| `name`             | The `binding_name`, if it exists. Otherwise, the `instance_name`.                                |
| `label`            | The name of the service offering.                                                                |
| `tags`             | An array of strings an app can use to identify a service instance.                               |
| `credentials`      | A JSON object containing the service-specific credentials needed to access the service instance. |
| `syslog_drain_url` | Not supported.                                                                                   |
| `volume_mounts`    | Not supported.                                                                                   |


The following example shows the value of the `VCAP_SERVICES` environment variable for bound instances of user-provided service instances.

~~~
VCAP_SERVICES=
{
  "user-provided": [
    {
      "binding_guid": "65ec345e-4f19-4499-ae70-a32b55c7f1cf",
      "binding_name": null,
      "credentials": {
        "hostname": "ca6efe83-8a9b-4395-98d0-124145d4e97a.mysql.service.internal",
        "jdbcUrl": "jdbc:mysql://ca6efe83-8a9b-4395-98d0-124145d4e97a.mysql.service.internal:3306/service_instance_db?user=441123dedf5d4b7ab988d7fae43bc452&password=P4$$W0RD&useSSL=false",
        "name": "service_instance_db",
        "password": "P4$$W0RD",
        "port": "3306",
        "type": "user-provided",
        "uri": "mysql://441123dedf5d4b7ab988d7fae43bc452:P4$$W0RD@ca6efe83-8a9b-4395-98d0-124145d4e97a.mysql.service.internal:3306/service_instance_db?reconnect=true",
        "username": "441123dedf5d4b7ab988d7fae43bc452"
      },
      "instance_guid": "0622de7e-2437-4a39-8048-a7df324c35df",
      "instance_name": "mysql",
      "label": "user-provided",
      "name": "mysql",
      "syslog_drain_url": null,
      "tags": [
        "p.mysql",
        "mysql",
        "database"
      ],
      "volume_mounts": []
    },
    {
      "binding_guid": "be7aba4d-a465-4e9d-9c01-9ce9861e68e7",
      "binding_name": "custom-binding-name",
      "credentials": {
        "some-credential": "some-value",
        "type": "user-provided"
      },
      "instance_guid": "f97f96d7-62f2-43db-866a-175f5a8e95bc",
      "instance_name": "custom-user-provided-service",
      "label": "user-provided",
      "name": "custom-binding-name",
      "syslog_drain_url": null,
      "tags": [
        "user-defined",
        "arbitrary",
        "tags"
      ],
      "volume_mounts": []
    }
  ]
}
~~~
