# System logs and metrics

This topic provides guidance on accessing system component logs and metrics.

## <a id="system-logs"></a>Gather system logs

Logs for Application Service Adapter system components flow through standard
Kubernetes logging channels. You can retrieve them by using the tooling of your
choice. For ad-hoc log retrieval, VMware recommends the `kapp logs` command in
the kapp cli as the most
convenient method to gather the logs from pods in one or more deployments. See the [Carvel documentation](https://carvel.dev/kapp/docs/latest/install/).

To fetch recent logs from all components of an Application Service Adapter installation:

```bash
kapp logs --app tas-adapter.app -n tap-install
```

To fetch recent logs from a specific deployment:

```bash
kapp logs --app tas-adapter.app -n tap-install --pod-name DEPLOYMENT-NAME%
```

Where `DEPLOYMENT-NAME` is the name of the Kubernetes Deployment, such as
`korifi-api-deployment`.

To stream logs instead of fetching the most recent logs, add the `--follow` flag to the earlier `kapp logs` commands.
For additional details and options, see the `kapp logs --help` help text.

## <a id="metrics"></a>Query performance metrics

All of the controller-managers deployed by Application Service Adapter
provide standard Prometheus performance
metrics generated
by the Kubernetes controller-runtime library. For more information, see the
[Kubebuilder documentation](https://book.kubebuilder.io/reference/metrics-reference.html).

Application Service Adapter controller managers are deployed with annotations
that makes them discoverable by a Prometheus server deployed to the same
cluster.

After you have Prometheus deployed, you can query any of the available
metrics. See [Default Exported Metrics
References](https://book.kubebuilder.io/reference/metrics-reference.html) in the
Kubebuilder documentation.
In particular, queries for `workqueue_depth` or `workqueue_queue_duration_seconds`
can help to indicate when a controller manager is resource constrained and must be scaled up.

To query the queue depth for the CFApp controller:

```bash
workqueue_depth{name="cfapp"}
```

To list the queue depths for all Application Service Adapter controllers:

```bash
workqueue_depth{namespace="tas-adapter-system"}
```

To query the average queue time for CFApp objects before reconciliation:

```bash
workqueue_queue_duration_seconds_sum{name="cfapp"}/workqueue_queue_duration_seconds_count
```
