# Scale Application Service Adapter

This topic provides guidance on scaling Application Service Adapter components
for performance or availability.

## <a id="system-performance"></a>Notes on system performance

Application Service Adapter runs on a Kubernetes cluster and
uses the Kubernetes API to store and manage resources, so the
performance of Application Service Adapter is very dependent on
the underlying Kubernetes control plane. If you observe slow performance or
timeouts when using Application Service Adapter, a good first step is to
ensure that the control plane for your Kubernetes cluster is properly resourced.

For managed clusters that come from public-cloud IaaSes, you generally won't
have direct control over how the control plane is provisioned, so you won't be
able to scale up control place resources. If you are managing your own
Kubernetes clusters, you must ensure you have provisioned adequate
capacity for the `apiserver` and `etcd` components. See the [Kubernetes
documentation](https://kubernetes.io/docs/setup/production-environment/) as a
starting place for understanding production cluster concerns.

## <a id="controllers"></a>Scaling the Application Service Adapter controller managers

### Vertical scaling
Application Service Adapter deploys a set of controller managers responsible
for reconciling various custom resources into running pods on your Kubernetes
cluster. If these controller managers are resource constrained, scaling them up
vertically may improve performance. Controller managers maintain an in-memory
informer cache of the events that they are responsible for tracking, so it is
common for controller managers on busy systems with many objects to require more
memory.

Controller manager deployments in the `tas-adapter-system` namespace are:

- `cartographer-builder-runner-controller-manager`
- `korifi-controllers-controller-manager`
- `korifi-job-task-runner-controller-manager`
- `korifi-kpack-build-controller-manager`
- `korifi-statefulset-runner-controller-manager`

To determine whether your controller managers face CPU or memory
pressure, you can check the Prometheus metrics for your controllers as described
in the [Metrics topic](logging-and-metrics.md#metrics).

You can also inspect the running pods for these deployments using the [`kubectl
top`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)
command.

If you determine that one or more of your controller managers is resource-
constrained, you can increase the available resources by setting the optional
scaling parameters as described in the [Configure installation
settings](install.md#configure-installation-settings) instructions, and then
re-running the [installation step](install.md#install-adapter).

### Horizontal scaling

As described in the [Failover and redundancy
topic](failover-and-redundancy.md#controllers), controller managers use
leader-election to designate a single active instance, so horizontal scaling is
not an effective strategy to improve performance of a slow controller. Since the
same deployments also serve webhook requests, you may choose to add horizontal scale
to improve webhook performance. You may also choose to scale your
deployments to improve controller availability. Multi-instance deployments
allow rolling deployments so that controllers and webhooks don't become
unresponsive during upgrades. Horizontal scaling can be achieved by using the
`replicas` scaling configuration.

## <a id="api"></a>Scaling the Application Service Adapter API

If you have confirmed that the Kubernetes control plane and the controller
managers both have sufficient resources, but you are still observing slow
response times or timeouts from the Cloud Foundry API, scaling up the
`korifi-api-deployment` in the `tas-adapter-system` namespace may help.

Unlike the controller managers, requests to the pods in this deployment are load-
balanced, so either horizontal or vertical scaling should improve performance.
By default, `korifi-api-deployment` [runs with two
replicas](failover-and-redundancy.md#cloud-foundry-api), so it should already
maintain availability during upgrades unless it is scaled down.

Similar to the controller managers, both vertical (memory and CPU) and
horizontal (replicas) scaling can be achieved by changing the [installation
settings](install.md#configure-installation-settings), and then
re-running the [installation step](install.md#install-adapter).
