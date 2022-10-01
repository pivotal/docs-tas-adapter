# Failover and redundancy

This topic describes the failover characteristics and redundancy of Application Service Adapter components and applications that are pushed with Application Service Adapter. It includes the following sections:

* [Cloud Foundry-compatible API](#cloud-foundry-api)
* [Controllers](#controllers)
* [Applications](#applications)

## <a id="cloud-foundry-api"></a>Cloud Foundry-compatible API

Application Service Adapter deploys two replicas of a v3 Cloud Foundry-compatible API (Korifi API) that clients communicate with. This API is stateless and can be horizontally scaled for increased availability and performance.

## <a id="controllers"></a>Controllers and Webhooks

Application Service Adapter deploys multiple components known as [controllers](https://kubernetes.io/docs/concepts/architecture/controller/) to the cluster. These components watch and update state on the cluster in what is known as a "control loop" and over time ensure that the state in the cluster is consistent. Additionally, these components run [admission webhooks](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/) that validate and update Application Service Adapter resources.

Application Service Adapter controllers are effectively singletons. They have leader election turned on by default so only a single controller instance can be active at a given time. All instances are able to serve the webhooks. They can be scaled horizontally for faster failover and for higher-availability of the webhooks. In the event that a controller crashes it will be automatically restarted by the platform. Application Service Adapter controllers are idempotent and the newly restarted instance will carry on where the failed one left off.

## <a id="applications"></a>Applications

The failover characteristics and redundancy recommendations for applications that are pushed with the adapter are highly dependent on the application itself. However, there are some common recommendations that can be provided for all applications.

1. Ensure all applications have at least two instances by using `cf scale` to scale up the application or by declaring multiple instances in the app's manifest. A single-instance application will incur downtime during cluster upgrades and maintenance. When an application is configured to run with two or more instances the Kubernetes Pod scheduler will attempt to balance the instances across nodes and minimize downtime. Additionally, Application Service Adapter will create a [`PodDisruptionBudget`](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) for multi-instance applications that sets the min-available instances for an app to be %50 of the total desired instances to maintain availability during these events.
1. Ensure all applications have the appropriate [health checks](https://docs.cloudfoundry.org/devguide/deploy-apps/healthchecks.html) configured to accurately determine the readiness and liveness of your apps. Application Service Adapter represents Cloud Foundry app health checks using `startupProbes` and `livenessProbes` on the underlying Pods running the application. By default, a `port` health check will be set to check whether the app is able to accept TCP connections, but more advanced `http` health checks can be configured to better detect readiness of the application.
