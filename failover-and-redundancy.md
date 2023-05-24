# Failover and redundancy with Application Service Adapter

This topic covers failover characteristics and redundancy of Application Service Adapter components and applications pushed with Application Service Adapter.

For instructions to edit the scaling characteristics of your Application
Service Adapter installation, see [Scaling Application Service Adapter](scaling.md).

## <a id="cloud-foundry-api"></a>Cloud Foundry-compatible API

Application Service Adapter deploys two replicas of a v3 Cloud
Foundry-compatible API (Korifi API) that clients communicate with. This API is
stateless and is horizontally scaled for increased
availability and performance. See [Scaling the Application Service Adapter API](scaling.md#api).

## <a id="controllers"></a>Controllers and webhooks

Application Service Adapter deploys multiple components known as
controllers to
the cluster. For more information about controllers, see the [Kubernetes documentation](https://kubernetes.io/docs/concepts/architecture/controller/).
These components watch and update state on the cluster in what is
known as a "control loop," and over time, they ensure that the state in the cluster is
consistent. Additionally, these components run admission
webhooks that validate and update Application Service Adapter resources. For more information about dynamic admission control and admission webhooks, see the [Kubernetes documentation](https://kubernetes.io/docs/reference/access-authn-authz/extensible-admission-controllers/).


Application Service Adapter controllers are effectively singletons. They have
leader election turned on by default so only a single controller instance is
active at a time. All instances can serve the webhooks. They are scaled horizontally for faster failover and for higher-availability of the
webhooks. In the event that a controller fails, it is automatically
restarted by the platform. Application Service Adapter controllers are
idempotent and the newly restarted instance carries on where the failed one
left off.

## <a id="applications"></a>Applications

The failover characteristics and redundancy recommendations for applications
that are pushed with the adapter depend on the application itself.
However, there are some common recommendations that are provided for all
applications.

1. Ensure that all applications have at least two instances by using `cf scale` to
   scale up the application or by declaring multiple instances in the app's
   manifest. A single-instance application incurs downtime during cluster
   upgrades and maintenance. When an application is configured to run with two
   or more instances, the Kubernetes pod scheduler attempts to balance the
   instances across nodes and minimize downtime. Additionally, Application
   Service Adapter creates a `PodDisruptionBudget` for multi-instance applications
   that sets the minimum available instances for an
   app to be 50% of the total instances needed to maintain availability during
   these events. For more information about Protecting an Application with a `PodDisruptionBudget`,
   see the [Kubernetes documentation](https://kubernetes.io/docs/tasks/run-application/configure-pdb/).

2. Ensure that all applications have the appropriate health
   checks configured to accurately verify the readiness and liveness of your apps.
   For more information about app health checks, see the
   [Cloud Foundry documentation](https://docs.cloudfoundry.org/devguide/deploy-apps/healthchecks.html).

   Application Service Adapter represents Cloud Foundry app health checks using
   `startupProbes` and `livenessProbes` on the underlying pods running the
   application. By default, a `port` health check is set to verify whether
   the app can accept TCP connections, but you can configure more advanced `http` health
   checks to better detect readiness of the application.
