[<< **Getting into Observability**](https://pranigopu.github.io/getting-into-observability/)

**[TECHNICAL FOCUS]**

> **Parent document**: [*OTel Collector*, **Getting into Observability**](./otel-collector.md)

<h1>AWS Container Insights Receiver</h1>

> Also referred to as `awscontainerinsight` receiver.
> 
> (Yes, the 's' is not present in `awscontainerinsight`)

---

**Contents**:

- [Overview](#overview)
- [What it collects](#what-it-collects)
- [Data sources and metric decoration](#data-sources-and-metric-decoration)
  - [Data sources](#data-sources)
    - [1. cAdvisor (node/pod/container metrics)](#1-cadvisor-nodepodcontainer-metrics)
    - [2. `k8sapiserver` (cluster-level metrics)](#2-k8sapiserver-cluster-level-metrics)
  - [Metric Decoration](#metric-decoration)
    - [`host`](#host)
    - [`stores`](#stores)
- [Deployment constraint: DaemonSet only](#deployment-constraint-daemonset-only)
- [Configuration](#configuration)
- [RBAC requirements](#rbac-requirements)
  - [ClusterRole](#clusterrole)
    - [Kubernetes object metadata](#kubernetes-object-metadata)
    - [Kubelet / cAdvisor data access](#kubelet--cadvisor-data-access)
    - [Receiver operational state](#receiver-operational-state)
    - [EndpointSlice Watch](#endpointslice-watch)
    - [Leader election via ConfigMap lock](#leader-election-via-configmap-lock)
    - [EndpointSlice watch](#endpointslice-watch-1)
  - [Leader election and Kubernetes leases](#leader-election-and-kubernetes-leases)
    - [About Kubernetes Leases](#about-kubernetes-leases)
- [The Use of Kubernetes lease for `awscontainerinsight` receiver](#the-use-of-kubernetes-lease-for-awscontainerinsight-receiver)
  - [Why leader election is needed](#why-leader-election-is-needed)
  - [RBAC requirements for using Kubernetes lease](#rbac-requirements-for-using-kubernetes-lease)
  - [Diagnosing the `leaderelection` permission error](#diagnosing-the-leaderelection-permission-error)
    - [Issue description](#issue-description)
    - [Resolution](#resolution)
- [Relationship to the OTLP receiver](#relationship-to-the-otlp-receiver)

---

# Overview
> **Useful context**: `awscontainerinsight` receiver works as a performance metrics collection agent, in a manner that is similar to a CloudWatch agent. For more clarity on this aspect, see ["The Precise Nature of Container Insights as a Managed Observability Layer", *CloudWatch Container Insights for EKS*, **Observability in AWS**](../observability-in-aws/cloudwatch-container-insights-for-eks.md#the-precise-nature-of-container-insights-as-a-managed-observability-layer). It is important to note that the `awscontainerinsight` receiver supports (i.e. enables or feeds into) CloudWatch Container Insights; i.e. this receiver is the component you use when you want the ADOT Collector to act as the data collection layer for the CloudWatch Container Insights product. However, even if your pipeline does not involve Container Insights and/or CloudWatch, the `awscontainerinsight` receiver can still serve as a data source for metrics.

---

The `awscontainerinsight` receiver is a Collector component in the `contrib` distribution that **directly scrapes container and node metrics from the EKS node's local sources** without requiring a separate metrics agent such as CloudWatch Agent or kube-state-metrics. It is designed specifically for Kubernetes workloads running on AWS infrastructure.

> **Reference**: [AWS Container Insights receiver README, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/README.md**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/README.md)

# What it collects
The receiver collects metrics at 4 levels of granularity, all from node-local sources:

| Metric level | Source | Example metrics |
|---|---|---|
| Node | `/sys/fs/cgroup`, `/proc` | CPU, memory, network, filesystem |
| Pod | `kubelet` Summary API | Per-pod CPU/memory, restart count |
| Container | `kubelet` Summary API | Per-container CPU/memory limits and usage |
| GPU (optional) | DCGM exporter sidecar | GPU utilisation, memory, temperature |

All metrics are emitted in **OTLP format**, so they flow through the same `processors` -> `exporters` pipeline chain as any other signal.

> **KEY POINT**: `awscontainerinsight` receiver is designed to collect only `Gauge` metrics (see: ["Gauge", *OTel*, **Getting into Observability**](./otel.md#gauge)), i.e. point-in-time metrics.

# Data sources and metric decoration
> **Key reference**: [`opentelemetry-collector-contrib`/`receiver`/`awscontainerinsight` receiver/`design.md`, **github.com**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md)

## Data sources
### 1. cAdvisor (node/pod/container metrics)
> **Context**: ["cAdvisor (Container Advisor)", *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-notes#cadvisor-container-advisor)

A customized, embedded cAdvisor library collects only the metrics and cgroups (Linux control groups; see: ["cgroup (Control Group)", *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#cgroup-control-group)) relevant to Container Insights. Raw cAdvisor data is shaped into infrastructure-layer metrics:

- **Node**: node, node filesystem, node disk I/O, node network
- **Pod/Container**: pod, pod network, container, container filesystem

Pod/container labels (`podName`, `podId`, `namespace`, `containerName`) are extracted from the cAdvisor container spec and attached as resource attributes, which the AWS Container Insights processor depends on for further processing.

In the context of the OTel Collector being deployed as a DaemonSet, with `awscontainerinsight` receiver running in each DaemonSet pod, because cAdvisor reads node-local sources (`/sys/fs/cgroup`, `/proc`, kubelet port `10250`) via the node-local kubelet (see: ["Kubelet", *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#kubelet)), each DaemonSet pod scrapes only its own node. There is no cross-node coordination concern here - every pod independently produces its node-, pod-, and container-level metrics without risk of duplication.

### 2. `k8sapiserver` (cluster-level metrics)
Collects cluster-level metrics from the Kubernetes API server.

Unlike cAdvisor, the Kubernetes API server is a shared, cluster-wide endpoint reachable from any pod on any node. If every DaemonSet pod queried it and emitted cluster-level metrics, those metrics would be duplicated proportional to the number of nodes in the cluster.

To prevent this, the receiver uses **leader election**: one DaemonSet pod is elected as the cluster leader, and only that pod queries the API server and emits cluster-scoped metrics. All other pods continue collecting their local node/pod/container metrics from cAdvisor unaffected. Leader election is implemented using a **ConfigMap as a distributed lock** - the elected leader continuously heartbeats to hold it; other instances periodically retry. If the leader fails, a new one is elected quickly.

> **Reference**: [awscontainerinsightreceiver design, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md)

## Metric Decoration
### `host`
Enriches metrics with host-level context:

- CPU and memory capacity via `gopsutil`
- EBS volume, Auto Scaling group, and cluster name via EC2 metadata and EC2 APIs

### `stores`
**Pod store**:

- Lists and caches all Pod objects from the local Kubelet
- Decorates pod and container metrics with:
  - Owner references, container resource limits/requests
  - Container ID and status, pod status, pod labels

**Service store**:

- Adds a `Service` attribute to metrics for pods that belong to a Kubernetes Service
- Uses `List & Watch` on `Endpoint` objects from the API server, so Service-to-pod mapping stays current with minimal API server impact

# Deployment constraint: DaemonSet only

Unlike the central OTel Collector Deployment used for log ingestion, an OTel Collector instance running the Container Insights receiver **must run as a DaemonSet**. The receiver is explicitly designed for this: running as a DaemonSet guarantees exactly one receiver instance per node, which is necessary because its data sources - an embedded cAdvisor library, node-local cgroup/proc filesystems, and the kubelet stats endpoint - are per-node and not reachable from a centralized pod.

> **References**:
>
> - [awscontainerinsightreceiver design, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md)
> - [awscontainerinsightreceiver README, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/README.md**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/README.md)

```
EKS Cluster
├── Node A
│   ├── fluent-bit-xxxxx        (DaemonSet, log agent)
│   └── otel-ci-agent-aaaaa     (DaemonSet, Container Insights receiver)
│       └── scrapes kubelet :10250, /sys/fs/cgroup, /proc
│
├── Node B
│   ├── fluent-bit-yyyyy
│   └── otel-ci-agent-bbbbb
│       └── scrapes kubelet :10250, /sys/fs/cgroup, /proc
│
...
```

For context, if we want to combine this with the Fluent Bit-to-OTel Collector pipeline:

```
EKS Cluster
├── Node A
│   ├── fluent-bit-xxxxx        (DaemonSet, log agent)
│   └── otel-ci-agent-aaaaa     (DaemonSet, Container Insights receiver)
│       └── scrapes kubelet :10250, /sys/fs/cgroup, /proc
│
├── Node B
│   ├── fluent-bit-yyyyy
│   └── otel-ci-agent-bbbbb
│       └── scrapes kubelet :10250, /sys/fs/cgroup, /proc
│
└── Central Collector Deployment
    └── receives OTLP logs from fluent-bit (separate pipeline)
```

The DaemonSet Collector instances can export directly to a backend (e.g. AMP, CloudWatch, ClickHouse) or forward to the central Collector Deployment via OTLP - the latter is useful when you want a single exporter configuration.

# Configuration

```yaml
receivers:
  awscontainerinsight:
    collection_interval: 30s
    container_orchestrator: eks
    add_service_as_source: true
    # Optional: send to a central Collector instead of directly to backend
    # Leave exporters here if forwarding; otherwise export directly.

service:
  pipelines:
    metrics:
      receivers:  [awscontainerinsight]
      processors: [memory_limiter, batch]
      exporters:  [otlp]        # forward to central Collector, or swap for direct backend
```

Key config fields:

| Field | Default | Notes |
|---|---|---|
| `collection_interval` | `30s` | Scrape cadence; lower values increase kubelet load |
| `container_orchestrator` | `eks` | Must be `eks` on EKS; affects label set emitted |
| `add_service_as_source` | `false` | Adds `service.name` resource attribute from pod labels |
| `prefer_full_pod_name` | `false` | If `true`, uses full generated pod name rather than base name |

# RBAC requirements
The DaemonSet's ServiceAccount needs cluster-level read permissions to resolve pod and node metadata, access the kubelet API, and manage the leader-election ConfigMap lock.

## ClusterRole

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: otel-awscontainerinsight-receiver-role
rules:

# Kubernetes object metadata - read-only
- apiGroups: [""]
  resources: ["nodes", "pods", "namespaces", "services", "endpoints"]
  verbs: ["get", "list", "watch"]

- apiGroups: ["apps"]
  resources: ["replicasets", "daemonsets", "deployments", "statefulsets"]
  verbs: ["get", "list", "watch"]

- apiGroups: ["batch"]
  resources: ["jobs", "cronjobs"]
  verbs: ["get", "list", "watch"]

# Kubelet / cAdvisor data access - read-only
- apiGroups: [""]
  resources: ["nodes/proxy"]
  verbs: ["get"]

- apiGroups: [""]
  resources: ["nodes/stats"]
  verbs: ["get"]

# Receiver operational state - read + create
- apiGroups: [""]
  resources: ["configmaps", "events"]
  verbs: ["create", "get"]

# Leader election audit events - write
- apiGroups: [""]
  resources: ["events"]
  verbs: ["patch"]

# Leader election ConfigMap lock - unscoped update
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["update"]

# Leader election ConfigMap lock - scoped
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["otel-container-insight-clusterleader"]
  verbs: ["get", "update", "create"]

# EndpointSlice watch
- apiGroups: ["discovery.k8s.io"]
  resources: ["endpointslices"]
  verbs: ["get", "list", "watch"]
```

The above configuration is explained below.

### Kubernetes object metadata
These rules are used by the receiver's `client-go` informers to decorate raw cAdvisor and kubelet metrics with Kubernetes metadata (pod name, namespace, owner references, node conditions, etc.).

> **Reference**: [awscontainerinsightreceiver README, **github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/awscontainerinsightreceiver**](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/awscontainerinsightreceiver)

| Resource | API group | Why required |
|---|---|---|
| `nodes` | `""` (core) | Required for the node reflector to `list` and `watch` node objects at the cluster scope. `nodes/proxy` (a subresource) does **not** grant this - they are separate permissions. Absence causes: `failed to list *v1.Node: nodes is forbidden` |
| `pods` | `""` (core) | Required for the pod reflector to `list` and `watch` pod objects at the cluster scope, used to correlate container metrics with pod metadata (name, labels, owner references). Absence causes: `failed to list *v1.Pod: pods is forbidden` |
| `namespaces` | `""` (core) | Required to resolve namespace metadata for pod-to-namespace mapping in resource attributes (`k8s.namespace.name`) |
| `services` | `""` (core) | Required by the service store to map pods to their parent Kubernetes Services for service-level metric aggregation |
| `endpoints` | `""` (core) | Required to resolve pod IP to service mappings. Distinct from `endpointslices` (`discovery.k8s.io`) - both are used by the receiver's k8s client |
| `replicasets` | `apps` | Intermediate owner reference between Pod and Deployment. Required to resolve the Pod -> ReplicaSet -> Deployment ownership chain, which is how `k8s.deployment.name` is obtained for a pod (a pod does not directly reference its Deployment) |
| `daemonsets`, `deployments`, `statefulsets` | `apps` | Complete the owner-reference chain for pods managed by those workload types. Without these, pods owned by a DaemonSet, Deployment, or StatefulSet cannot have their workload name resolved |
| `jobs`, `cronjobs` | `batch` | Resolves owner references for pods created by batch workloads, enabling job-level metric aggregation |

### Kubelet / cAdvisor data access
> **References**:
>
> - [awscontainerinsightreceiver README, **github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/awscontainerinsightreceiver**](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/receiver/awscontainerinsightreceiver)
> - [Node metrics data, **kubernetes.io/docs/reference/instrumentation/node-metrics**](https://kubernetes.io/docs/reference/instrumentation/node-metrics/)

| Resource | API group | Why required |
|---|---|---|
| `nodes/proxy` | `""` (core) | Allows the receiver to reach the kubelet HTTPS API (port `10250`) via the API server - specifically the `/metrics/cadvisor` and `/metrics` endpoints. The API server acts as a reverse proxy: `GET /api/v1/nodes/<node>/proxy/...` |
| `nodes/stats` | `""` (core) | Queries the kubelet Summary API at `/stats/summary`, which exposes CPU, memory, filesystem, and network stats for pods and containers as reported by the embedded cAdvisor |

> **NOTE: `nodes/proxy` breadth**: `nodes/proxy` is a broad subresource - it grants access to the entire kubelet API, not just metrics endpoints. It is required here because the upstream `awscontainerinsightreceiver` does not yet use the fine-grained kubelet subresources (`nodes/metrics`, `nodes/stats`) introduced by KEP-2862. When the receiver adopts those subresources, `nodes/proxy` can be replaced with the narrower alternatives.
>
> **Reference**: [Kubernetes v1.36: Fine-Grained Kubelet API Authorization Graduates to GA, **kubernetes.io/blog**](https://kubernetes.io/blog/2026/04/24/kubernetes-v1-36-fine-grained-kubelet-authorization-ga/)

### Receiver operational state

| Resource | API group | Verbs | Why required |
|---|---|---|---|
| `configmaps`, `events` | `""` (core) | `create`, `get` | Created by the receiver for its own operational state |
| `events` | `""` (core) | `patch` | Required to emit leader election audit events (e.g. `"X became leader"`). Split into a dedicated rule to avoid applying write verbs to `configmaps` via this block - `configmaps` write access is handled separately and more narrowly under leader election below |

### EndpointSlice Watch

| Resource | API group | Why required |
|---|---|---|
| `endpointslices` | `discovery.k8s.io` | Prevents the client-go reflector error: `cannot list resource "endpointslices" ... at the cluster scope` |

> **Reference**: [EndpointSlice, **docs.redhat.com/en/documentation/openshift_container_platform/4.15/html/network_apis/endpointslice-discovery-k8s-io-v1**](https://docs.redhat.com/en/documentation/openshift_container_platform/4.15/html/network_apis/endpointslice-discovery-k8s-io-v1)

### Leader election via ConfigMap lock
The receiver uses a ConfigMap as a distributed lock primitive to elect a single cluster leader among all DaemonSet pods. Only the leader emits cluster-scoped metrics (e.g. cluster-level CPU/memory aggregates), preventing duplication proportional to node count. See ["Leader election and kubernetes leases" in this document](#leader-election-and-kubernetes-leases) for a full explanation.

> **Reference**: [awscontainerinsightreceiver design, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md)

| Resource | API group | Scope | Why required |
|---|---|---|---|
| `configmaps` | `""` (core) | Unscoped `update` | Required alongside the scoped rule below. Kubernetes RBAC `resourceNames` filtering does not apply to `create` operations - it only constrains operations on already-existing named objects - so an unscoped `update` is necessary |
| `configmaps` (`otel-container-insight-clusterleader`) | `""` (core) | Scoped `get`, `update`, `create` | Constrains the above to the specific leader-election lock ConfigMap, limiting blast radius |

### EndpointSlice watch

| Resource | API group | Why required |
|---|---|---|
| `endpointslices` | `discovery.k8s.io` | Prevents the client-go reflector error: `cannot list resource "endpointslices" ... at the cluster scope` |

> **Reference**: [EndpointSlice, **docs.redhat.com/en/documentation/openshift_container_platform/4.15/html/network_apis/endpointslice-discovery-k8s-io-v1**](https://docs.redhat.com/en/documentation/openshift_container_platform/4.15/html/network_apis/endpointslice-discovery-k8s-io-v1)

## Leader election and Kubernetes leases
This section explains what Kubernetes Leases are, why they appear in many community RBAC templates for this receiver, and why they are not actually required.

### About Kubernetes Leases
> **Additional conceptual context**: ["Kubernetes Lease", *MASTR*, **Untitled**](../untitled/miscellaneous-alphabetically-sorted-technical-references.md#kubernetes-lease)

A `Lease` is a Kubernetes resource in the `coordination.k8s.io/v1` API group, purpose-built for distributed coordination primitives such as leader election. Kubernetes itself uses Leases for system-critical coordination including node heartbeats and control-plane component leader election (e.g. `kube-scheduler`, `kube-controller-manager`).

For leader election, a Lease acts as a lightweight distributed lock stored in the API server. Competing instances attempt to acquire the lock by writing their identity to the Lease object; the holder periodically renews it. If the holder fails to renew within the lease duration, other candidates may acquire it.

Leases are the modern, purpose-built replacement for ConfigMap-based or Endpoint-based locks, which were historically used for the same purpose before `coordination.k8s.io` was introduced.

> **References**:
>
> - [Leases, **kubernetes.io/docs/concepts/architecture/leases**](https://kubernetes.io/docs/concepts/architecture/leases/)
> - [Coordinated Leader Election, **kubernetes.io/docs/concepts/cluster-administration/coordinated-leader-election**](https://kubernetes.io/docs/concepts/cluster-administration/coordinated-leader-election)

# The Use of Kubernetes lease for `awscontainerinsight` receiver
> **Relevant context**: ADOT => AWS Distro for Open Telemetry
>
> **For information on this, see**:
> 
> - ["AWS Distro for OpenTelemetry (ADOT)", `metrics-collection-in-eks.md`](./metrics-collection-in-eks.md#aws-distro-for-opentelemetry-adot)
> - ["ADOT Collector", `metrics-collection-in-eks.md`](./metrics-collection-in-eks.md#adot-collector)

---

As per available documentation:

"It leverages k8s configmap resource as some sort of LOCK primitive. The deployment will create a dedicated configmap as the lock resource. If one receiver is required to elect a leader, it will try to lock (via Create/Update) the configmap."

> **Reference**: [awscontainerinsightreceiver design, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/awscontainerinsightreceiver/design.md)

The lock primitive is a ConfigMap named `otel-container-insight-clusterleader`, and while there is no mention of `coordination.k8s.io/leases` in the design document, the official ADOT deployment manifest (`otel-container-insights-infra.yaml`) grants RBAC permissions for **both** resource types under the same name. The ClusterRole definition includes separate rules for `configmaps` (core API group) and `leases` (`coordination.k8s.io`), each scoped to the resource name `otel-container-insight-clusterleader` with verbs `get`, `update`, and `create`. This reflects the evolution of the Kubernetes leader election library (`k8s.io/client-go/tools/leaderelection`): older versions used ConfigMaps as the lock object; modern versions default to Leases. The dual-resource grant in the manifest ensures compatibility across both behaviours.

> **Reference**: [otel-container-insights-infra.yaml, **github.com/aws-observability/aws-otel-collector/blob/main/deployment-template/eks/otel-container-insights-infra.yaml**](https://github.com/aws-observability/aws-otel-collector/blob/main/deployment-template/eks/otel-container-insights-infra.yaml)

## Why leader election is needed
When the ADOT collector runs as a DaemonSet, one pod is scheduled per cluster node. This means every pod has access to the Kubernetes API and could independently scrape cluster-level resources (namespace metadata, all pod and node objects). Without coordination, this would produce duplicate metrics and place unnecessary load on the API server. Leader election solves this by designating a single pod - the leader - as solely responsible for cluster-scoped metric collection. All other pods (workers) restrict themselves to node-local metrics only.

## RBAC requirements for using Kubernetes lease
The following ClusterRole rules are required for the leader election mechanism to function. Both are scoped to the specific resource name to limit blast radius:

```yaml
# ConfigMap-based lock (legacy / fallback)
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["otel-container-insight-clusterleader"]
  verbs: ["get", "update", "create"]

# Lease-based lock (modern default)
- apiGroups: ["coordination.k8s.io"]
  resources: ["leases"]
  verbs: ["create"]  # required to create the Lease object if it does not yet exist
- apiGroups: ["coordination.k8s.io"]
  resources: ["leases"]
  resourceNames: ["otel-container-insight-clusterleader"]
  verbs: ["get", "update", "create"]
```

> **NOTE**: The unscoped `create` rule on `leases` is necessary because `resourceNames` filters only apply to existing objects - a `create` request for a not-yet-existing resource cannot be matched by name at admission time.

> **Reference**: [otel-container-insights-infra.yaml, **github.com/aws-observability/aws-otel-collector/blob/main/deployment-template/eks/otel-container-insights-infra.yaml**](https://github.com/aws-observability/aws-otel-collector/blob/main/deployment-template/eks/otel-container-insights-infra.yaml)

## Diagnosing the `leaderelection` permission error
### Issue description
The following error pattern indicates that the collector pod's service account lacks the RBAC permissions described above:

```log
I0530 12:11:20.912103       1 leaderelection.go:258] "Attempting to acquire leader lease..." lock="metrics-monitoring/otel-container-insight-clusterleader"
...
E0530 12:11:29.844186       1 leaderelection.go:452] "Error retrieving lease lock" err="leases.coordination.k8s.io "otel-container-insight-clusterleader" is forbidden: User "system:serviceaccount:metrics-monitoring:default" cannot get resource "leases" in API group "coordination.k8s.io" in the namespace "metrics-monitoring"" lock="metrics-monitoring/otel-container-insight-clusterleader"
```

2 issues are evident from this output:

**1. Wrong service account.** The pod is running as `system:serviceaccount:metrics-monitoring:default` - the namespace's bare `default` service account - rather than a dedicated service account with the necessary ClusterRole bound to it (e.g. `aws-otel-sa` as defined in the official manifest, or an equivalent). The `default` service account carries no ADOT-specific permissions.

**2. Missing or misbound RBAC.** Even if a dedicated service account exists in the cluster, the ClusterRoleBinding must reference the service account and namespace that the DaemonSet pods actually run under. A mismatch between the namespace in the binding and the namespace where the pods are deployed (`metrics-monitoring` here, vs. `aws-otel-eks` in the upstream template) is a common cause of this failure.

> **Reference**: [Failed to update lock: leases.coordination.k8s.io is forbidden - Issue #63, **github.com/aws-observability/aws-otel-helm-charts/issues/63**](https://github.com/aws-observability/aws-otel-helm-charts/issues/63)

### Resolution
Either adapt the official deployment manifest to your namespace, or apply a targeted fix:

1. Create a dedicated `ServiceAccount` in the `metrics-monitoring` namespace.
2. Bind the ADOT ClusterRole (containing the rules above) to that service account via a `ClusterRoleBinding`.
3. Update the DaemonSet `spec.serviceAccountName` to reference the new service account.

Using the `default` service account for workload-specific RBAC is not recommended, as permissions granted to it apply to all pods in the namespace that do not explicitly declare a service account.

# Relationship to the OTLP receiver
> **OTLP receiver discussed here**: ["The OTLP Receiver", *OTel Collector*, **Getting into Observability**](./otel-collector.md#the-otlp-receiver)

---

These 2 receivers serve different purposes and are not alternatives to each other:

| | OTLP receiver | Container Insights receiver |
|---|---|---|
| **Signal type** | Logs (primary), metrics, traces | Metrics only |
| **Data source** | Push from agents (Fluent Bit, SDKs) | Pull from node-local APIs |
| **Deployment** | Central Deployment | DaemonSet (per-node) |
| **AWS-specific** | No | Yes (EKS-aware labelling) |

They can run in the same Collector binary (as separate pipelines) or in separate Collector instances - the latter is preferred in production to isolate resource limits and failure domains.