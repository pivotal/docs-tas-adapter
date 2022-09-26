# Managing an Application Service Adapter installation

This topic provides guidance for managing a deployment of Application Service Adapter for Tanzu Application Platform:

* [Gathering system logs](#system-logs)
* [Querying performance metrics](#metrics)

## <a id="system-logs"></a>Gathering system logs

Logs for Application Service Adapter system components flow through standard
Kubernetes logging channels and may be retrieved using the tooling of your
choice. For ad-hoc log retrieval, VMware recommends the `kapp logs` command in
the [kapp cli](https://carvel.dev/kapp/docs/latest/install/) as the most
convenient method to gather the logs from the pods in one or more deployments.
For example:

To fetch recent logs from all components of an Application Service Adapter installation:

    ```bash
    kapp logs --app tas-adapter-ctrl
    ```

To fetch recent logs from a specific deployment:

    ```bash
    kapp logs --app tas-adapter-ctrl --pod-name DEPLOYMENT-NAME%
    ```
    Where DEPLOYMENT-NAME is the name of the Kubernetes Deployment (e.g.
    "korifi-api-deployment").

To stream logs from all components of an Application Service Adapter installation:

    ```bash
    kapp logs --app tas-adapter-ctrl --follow
    ```

To stream logs from a specific deployment:

    ```bash
    kapp logs --app tas-adapter-ctrl --pod-name DEPLOYMENT-NAME% --follow
    ```

    Where DEPLOYMENT-NAME is the name of the Kubernetes Deployment (e.g.
    "korifi-api-deployment").

For additional details and options, refer to the `kapp logs --help` help text.

## <a id="metrics"></a>Querying performance metrics

All of the controller-managers deployed by the Application Service Adapter
provide [standard Prometheus performance
metrics](https://book.kubebuilder.io/reference/metrics-reference.html) generated
by the Kubernetes controller-runtime library.

Application Service Adapter controller managers are deployed with annotations
that makes them discoverable by a Prometheus server deployed to the same
cluster.

Once you have Prometheus deployed, you can query [any of the available
metrics](https://book.kubebuilder.io/reference/metrics-reference.html). In
particular, queries for `workqueue_depth` or `workqueue_queue_duration_seconds`
may help to indicate when a controller manager is resource constrained and needs
to be scaled up.

To query the queue depth for the CFApp controller:

    ```bash
    workqueue_depth{name="cfapp"}
    ```

To list the queue depths for all Application Service Adapter controllers:

    ```bash
    workqueue_depth{namespace="tas-adapter-system"}
    ```

To query the average queue time for CFApp objects prior to reconciliation:

    ```bash
    workqueue_queue_duration_seconds_sum{name="cfapp"}/workqueue_queue_duration_seconds_count
    ```
