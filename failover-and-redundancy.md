# Failover and redundancy

This topic describes the failover characteristics and redundancy of Application
Service Adapter components and applications that are pushed with Application
Service Adapter. It includes the following sections:

- [Failover and redundancy](#failover-and-redundancy)
  - [<a id="cloud-foundry-api"></a>Cloud Foundry-compatible API](#cloud-foundry-compatible-api)
  - [<a id="controllers"></a>Controllers and Webhooks](#controllers-and-webhooks)
  - [<a id="applications"></a>Applications](#applications)

For instructions to edit the scaling characteristics of your Application
Service Adapter installation, see [Scaling Application Service Adapter](scaling.md).

## <a id="cloud-foundry-api"></a>Cloud Foundry-compatible API

Application Service Adapter deploys two replicas of a v3 Cloud
Foundry-compatible API (Korifi API) that clients communicate with. This API is
stateless and is horizontally scaled for increased
availability and performance. See [Scaling the Application Service Adapter API](scaling.md#api).

## <a id="controllers"></a>Controllers and Webhooks

Application Service Adapter deploys multiple components known as
[controllers](https://kubernetes.io/docs/concepts/architecture/controller/) to
the cluster. These components watch and update state on the cluster in what is
known as a "control loop" and over time ensure that the state in the cluster is
consistent. Additionally, these components run [admission
webhooks](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/)
that validate and update Application Service Adapter resources.

Application Service Adapter controllers are effectively singletons. They have
leader election turned on by default so only a single controller instance is
active at a time. All instances can serve the webhooks. They are scaled horizontally for faster failover and for higher-availability of the
webhooks. In the event that a controller fails, it is automatically
restarted by the platform. Application Service Adapter controllers are
idempotent and the newly restarted instance carries on where the failed one
left off.

## <a id="applications"></a>Applications

The failover characteristics and redundancy recommendations for applications
that are pushed with the adapter are highly dependent on the application itself.
However, there are some common recommendations that are provided for all
applications.

1. Ensure that all applications have at least two instances by using `cf scale` to
   scale up the application or by declaring multiple instances in the app's
   manifest. A single-instance application incurs downtime during cluster
   upgrades and maintenance. When an application is configured to run with two
   or more instances the Kubernetes Pod scheduler attempts to balance the
   instances across nodes and minimize downtime. Additionally, Application
   Service Adapter creates a
   [`PodDisruptionBudget`](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)
   for multi-instance applications that sets the min-available instances for an
   app to be %50 of the total instances that you want to maintain availability during
   these events.
2. Ensure that all applications have the appropriate [health
   checks](https://docs.cloudfoundry.org/devguide/deploy-apps/healthchecks.html)
   configured to accurately verify the readiness and liveness of your apps.
   Application Service Adapter represents Cloud Foundry app health checks using
   `startupProbes` and `livenessProbes` on the underlying pods running the
   application. By default, a `port` health check is set to verify whether
   the app can accept TCP connections, but more advanced `http` health
   checks can be configured to better detect readiness of the application.
