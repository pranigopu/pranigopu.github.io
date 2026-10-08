[<< **Getting into Observability**](https://pranigopu.github.io/getting-into-observability/)

> **Parent document**: [*OTel*, **Getting into Observability**](./otel.md)

<h1>OTel Collector</h1>

---

**Contents**:

- [What is an OTel Collector?](#what-is-an-otel-collector)
- [Receiver -\> processor -\> exporter pipeline](#receiver---processor---exporter-pipeline)
  - [Flow overview](#flow-overview)
  - [Pipeline definition in config](#pipeline-definition-in-config)
- [Receivers](#receivers)
  - [The OTLP receiver](#the-otlp-receiver)
    - [Overview](#overview)
    - [The OTLP log envelope structure](#the-otlp-log-envelope-structure)
    - [Kubernetes in-cluster DNS + Collector service](#kubernetes-in-cluster-dns--collector-service)
  - [The AWS Container Insights receiver](#the-aws-container-insights-receiver)
  - [The Prometheus receiver](#the-prometheus-receiver)
  - [The Kubernetes Cluster receiver](#the-kubernetes-cluster-receiver)
  - [The Kubelet Stats receiver](#the-kubelet-stats-receiver)
- [Processors](#processors)
  - [`memory_limiter`](#memory_limiter)
  - [`batch`](#batch)
  - [`k8s_attributes`](#k8s_attributes)
- [Exporters](#exporters)
  - [The ClickHouse exporter](#the-clickhouse-exporter)
    - [Transport: native TCP protocol, not HTTP](#transport-native-tcp-protocol-not-http)
    - [`create_schema: false`](#create_schema-false)
    - [Column contract](#column-contract)
    - [Interaction with the `batch` processor](#interaction-with-the-batch-processor)
- [Deployment on Kubernetes](#deployment-on-kubernetes)
  - [Deployment vs. DaemonSet](#deployment-vs-daemonset)
  - [In-cluster DNS name](#in-cluster-dns-name)

---

# What is an OTel Collector?
The OpenTelemetry Collector is a **vendor-neutral telemetry processing agent**: a stateless binary that receives telemetry (logs, metrics, traces), optionally transforms it, and exports it to one or more backends. It is not an SDK, not an observability backend, and does not store data.

As discussed above, OTel itself is a CNCF-graduated open-source framework that standardises how telemetry is collected, processed, and exported across distributed systems. The OTel Collector is an add-on (not core) component of this framework - specifically, it is an add-on component that handles the part of the pipeline tying remote data sources to storage backends.

> **References**:
>
> - [OpenTelemetry Collector, **opentelemetry.io/docs/collector**](https://opentelemetry.io/docs/collector/)
> - [OpenTelemetry CNCF project, **cncf.io/projects/opentelemetry**](https://www.cncf.io/projects/opentelemetry/)

The Collector exists in two distributions: `core` (maintained by the OTel project, fewer components) and `contrib` (maintained by the community, includes the ClickHouse exporter and `k8s_attributes` processor). For EKS + ClickHouse use cases, the `contrib` distribution is required.

> **Reference**: [*opentelemetry-collector-contrib*, **github.com/open-telemetry**](https://github.com/open-telemetry/opentelemetry-collector-contrib)

# Receiver -> processor -> exporter pipeline
## Flow overview
A Collector pipeline is configured explicitly as a chain of named components. Each component type has a defined contract for what it accepts and emits.

```
+-----------------+     +------------------------+     +-----------------+
│    RECEIVER     │────>│      PROCESSOR(S)      │────>│    EXPORTER     │
│                 │     │                        │     │                 │
│ Ingests data    │     │ Transform / filter /   │     │ Sends data to   │
│ from a source   │     │ batch / enrich         │     │ a destination   │
│                 │     │                        │     │                 │
│ e.g. otlp       │     │ e.g. memory_limiter    │     │ e.g. clickhouse │
│                 │     │      batch             │     │      debug      │
│                 │     │      k8s_attributes    │     │      otlp       │
+-----------------+     +------------------------+     +-----------------+
                                (optional)
```

Processors run in the order they are listed in the pipeline definition. The order matters: for example, the `memory_limiter` must come before `batch` so that backpressure is applied before records are buffered (see ["Processors" in this document](#processors) for more details about these processors).

> **Reference**: [OTel Collector - Configuration, **opentelemetry.io/docs/collector/configuration**](https://opentelemetry.io/docs/collector/configuration/)

## Pipeline definition in config
A pipeline is declared under `service.pipelines` in the Collector's YAML manifest. It references named components defined in the `receivers`, `processors`, and `exporters` sections:

```yaml
receivers:
  otlp:
    protocols:
      http:
        endpoint: 0.0.0.0:4318

processors:
  memory_limiter:
    check_interval: 1s
    limit_mib: 400
    spike_limit_mib: 100
  batch:
    send_batch_size: 10000
    timeout: 5s

exporters:
  clickhouse:
    endpoint: tcp://clickhouse-host:9000
    database: otel
    logs_table_name: otel_logs
    create_schema: false

service:
  pipelines:
    logs:
      receivers:  [otlp]
      processors: [memory_limiter, batch]
      exporters:  [clickhouse]
```

Each signal type (logs, metrics, traces) has a separate pipeline. Adding a new signal does not require modifying an existing pipeline.

# Receivers
> **NOTE**: Because I got into OTel Collectors through Kubernetes deployments, I shall focus on the receivers in the context of Kubernetes as the container orchestration platform in which the OTel Collector shall be running. Also, the receivers I shall be talking about are specifically those I engaged with more deeply, for my own project-specific reasons.

## The OTLP receiver
### Overview
The OTLP receiver accepts telemetry over the **OpenTelemetry Protocol (OTLP)**. It is the standard ingress point for any OTel-compliant producer, including Fluent Bit's `opentelemetry` output plugin.

It listens on two ports by default:

| Protocol | Port | Notes |
|---|---|---|
| gRPC | 4317 | Binary, multiplexed, lower overhead at high throughput |
| HTTP | 4318 | JSON or Protobuf; simpler to configure and firewall |

For Kubernetes log ingestion via Fluent Bit, **HTTP (port 4318)** is the standard choice. The endpoint path for logs is `/v1/logs`.

> **Reference**: [*OTLP receiver*, **github.com/open-telemetry/opentelemetry-collector/tree/main/receiver/otlpreceiver**](https://github.com/open-telemetry/opentelemetry-collector/tree/main/receiver/otlpreceiver)

### The OTLP log envelope structure
Every log record arriving at the OTLP receiver has this structure:

```
ExportLogsServiceRequest
└── ResourceLogs[]
    ├── Resource
    │   └── Attributes: Map<string, AnyValue>
    │       ├── k8s.pod.name        -> from Fluent Bit Kubernetes filter
    │       ├── k8s.namespace.name
    │       ├── k8s.node.name
    │       └── service.name
    └── ScopeLogs[]
        └── LogRecord[]
            ├── TimeUnixNano        -> nanosecond timestamp
            ├── Body                -> raw log line (from logs_body_key $log)
            ├── SeverityText        -> e.g. "INFO", "ERROR"
            ├── SeverityNumber      -> OTel numeric scale 0–24
            ├── TraceId             -> 16-byte hex (empty until traces added)
            ├── SpanId              -> 8-byte hex (empty until traces added)
            └── Attributes: Map<string, AnyValue>   -> per-event context
```

`Resource` holds metadata that is **fixed per source** (the same for all logs from the same pod). `LogRecord.Attributes` holds **per-event** context. This distinction is preserved in ClickHouse via the `ResourceAttributes` and `LogAttributes` Map columns respectively.

> **Reference**: [OTel Log Data Model, **opentelemetry.io/docs/specs/otel/logs/data-model**](https://opentelemetry.io/docs/specs/otel/logs/data-model/)

### Kubernetes in-cluster DNS + Collector service
For Fluent Bit pods to reach the OTLP receiver, the Collector is exposed via a Kubernetes **ClusterIP Service**. This Service gets a stable cluster-internal DNS name, resolved by CoreDNS (kube-dns):

```
<service-name>.<namespace>.svc.cluster.local
```

For example, if the Service is named `otel-collector` in the `monitoring` namespace:

```
otel-collector.logging.svc.cluster.local
```

This DNS name is resolved **only from within the cluster**. CoreDNS maps it to the ClusterIP of the Service, which in turn load-balances across Collector pod replicas. Fluent Bit's output plugin `Host` parameter should be set to this DNS name.

> **References**:
>
> - [DNS for Services and Pods, **kubernetes.io/docs/concepts/services-networking/dns-pod-service**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
> - [Service, **kubernetes.io/docs/concepts/services-networking/service**](https://kubernetes.io/docs/concepts/services-networking/service/)

## The AWS Container Insights receiver
> Also referred to as `awscontainerinsight` receiver.
> 
> (Yes, the 's' is not present in `awscontainerinsight`)
>
> This is an AWS EKS-specific receiver, as the name suggests.

**See**: [*AWS Container Insights Receiver*, **Getting into Observability**](./otel-collector-awscontainerinsight-receiver.md)

## The Prometheus receiver
**See**: [*Prometheus Receiver*, **Getting into Observability**](./otel-collector-prometheus-receiver.md)

## The Kubernetes Cluster receiver
> Also referred to as `k8s_cluster`.

The `k8s_cluster` receiver collects cluster-level metrics and entity events from the Kubernetes API server. It uses the K8s API to listen for updates. A single instance of this receiver should be used to monitor a cluster. This receiver provides a mechanism for leader election if multiple replicas of the receiver are running within a cluster.

> **KEY POINT**: `k8s_cluster` receiver collects cluster-level data, hence is ideally to be deployed within a Deployment OTel Collector. Without leader election enabled, it should be deployed with only 1 replica; multiple replicas would cause the duplicate scraping of metrics data.

> **Reference**: [`open-telemetry`/`opentelemetry-collector-contrib`/`receiver`/`k8sclusterreceiver`/`README.md`, **github.com**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/k8sclusterreceiver/README.md)

---

It serves as a modern, unified alternative to Kube-State-Metrics, eliminating the need to run extra, standalone monitoring pods in your cluster.

> **SIDE NOTE**: Kube-State-Metrics (KSM) is an add-on service that listens to the Kubernetes API server and generates metrics about the overall state of your cluster's native objects (e.g. Pods, Nodes, Deployments, and StatefulSets). Unlike the metrics-server (which measures CPU/memory usage), KSM focuses strictly on object health, lifecycle phases, metadata, and configuration.

## The Kubelet Stats receiver
> Also called the `kubeletstats` receiver.

The Kubelet Stats Receiver pulls node, pod, container, and volume metrics from the API server on a kubelet (for more on kubelet, see: ["Kubelet", `technical-reference.md`](./technical-reference.md#kubelet)) and sends it down the metric pipeline for further processing. A kubelet runs on a Kubernetes node and has an API server to which this receiver connects. To configure this receiver, you have to tell it how to connect and authenticate to the API server and how often to collect data and send it to the next consumer. `kubeletstats` receiver supports both secure Kubelet endpoint exposed at port 10250 by default and read-only Kubelet endpoint exposed at port 10255.

> **KEY POINT**: `kubeletstats` receiver collects node-local data, hence is ideally to be deployed within a DaemonSet OTel Collector.

> **Reference**: [`open-telemetry`/`opentelemetry-collector-contrib`/`receiver`/`kubeletstatsreceiver`/`README.md`, **github.com**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/receiver/kubeletstatsreceiver/README.md)

# Processors
> **NOTE**: Because I got into OTel Collectors through Kubernetes deployments, I shall focus on the processors in the context of Kubernetes as the container orchestration platform in which the OTel Collector shall be running. Also, the receivers I shall be talking about are specifically those I engaged with more deeply, for my own project-specific reasons.

## `memory_limiter`
The `memory_limiter` processor is a **backpressure valve**. It monitors the Collector's memory usage and begins refusing incoming records when consumption approaches `limit_mib`. This prevents OOM kills during log spikes (e.g. EKS rolling deployments that produce brief bursts of high log volume).

It must be the **first processor** in the pipeline so that backpressure is applied before records accumulate in the batch buffer.

```yaml
processors:
  memory_limiter:
    check_interval:  1s
    limit_mib:       400
    spike_limit_mib: 100   # soft ceiling; triggers early refusal
```

> **Reference**: [`memory_limiter` processor, **github.com/open-telemetry/opentelemetry-collector/blob/main/processor/memorylimiterprocessor**](https://github.com/open-telemetry/opentelemetry-collector/blob/main/processor/memorylimiterprocessor/README.md)

## `batch`
The `batch` processor accumulates records and flushes them as a single bulk insert. ClickHouse is optimized for large batch inserts; many small individual inserts degrade write throughput significantly.

```yaml
processors:
  batch:
    send_batch_size:     10000   # flush when N records accumulated
    send_batch_max_size: 10000   # hard cap per batch
    timeout:             5s      # flush after this interval even if under size
```

Tuning guidance: if `otelcol_processor_batch_timeout_trigger_send` shows most flushes are triggered by timeout rather than size, `send_batch_size` is set too high for actual throughput - reduce it.

> **Reference**: [*batch processor*, **github.com/open-telemetry/opentelemetry-collector/blob/main/processor/batchprocessor**](https://github.com/open-telemetry/opentelemetry-collector/blob/main/processor/batchprocessor/README.md)

## `k8s_attributes`
The `k8s_attributes` processor enriches records with Kubernetes metadata **at the Collector level**, querying the Kubernetes API directly. It can attach fields that are not available from Fluent Bit's Kubernetes filter alone:

- `k8s.pod.uid`
- `k8s.deployment.name` (resolved from ReplicaSet owner reference)
- `container.image.name` and `container.image.tag`

This processor is optional. If Fluent Bit's Kubernetes filter already provides sufficient metadata, it can be omitted.

> **Reference**: [*k8s_attributes processor README*, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/processor/k8s_attributesprocessor**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/processor/k8s_attributesprocessor/README.md)

# Exporters
> **NOTE**: Because I got into OTel Collectors through Kubernetes deployments, I shall focus on the exporters in the context of Kubernetes as the container orchestration platform in which the OTel Collector shall be running. Also, the receivers I shall be talking about are specifically those I engaged with more deeply, for my own project-specific reasons.

## The ClickHouse exporter
The ClickHouse exporter writes log records from the Collector to a ClickHouse table. It is part of the `contrib` distribution and is co-maintained with ClickHouse.

> **Reference**: [ClickHouse exporter README, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/clickhouseexporter**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/clickhouseexporter/README.md)

### Transport: native TCP protocol, not HTTP
The exporter connects to ClickHouse over the **native TCP protocol on port 9000** (not the HTTP interface on port 8123). It uses **LZ4 compression** for the wire transfer. This is the correct port to use regardless of whether ClickHouse is self-hosted or managed.

```yaml
exporters:
  clickhouse:
    endpoint: tcp://your-clickhouse-host:9000
    username: <username>
    password: <password>
    database: otel
    logs_table_name: otel_logs
    create_schema: false
```

### `create_schema: false`
By default, the exporter attempts to create its own schema on first run. In production, this is undesirable because:

- The auto-generated schema may not match optimized DDL (e.g. codec choices, materialized columns, ORDER BY design)
- Schema creation requires elevated ClickHouse permissions
- If the table already exists, a schema mismatch causes insertion failures

Setting `create_schema: false` tells the exporter to assume the table already exists. The table must be created manually before the first export attempt.

> **Reference**: [ClickHouse exporter README - `create_schema`, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/clickhouseexporter/README.md**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/clickhouseexporter/README.md)

### Column contract
The exporter writes a fixed set of columns. The target table must contain all of them or insertion will fail with `No such column` errors. The canonical reference schema is maintained in the `opentelemetry-collector-contrib` repository:

> **Reference**: [*OTel logs table SQL template*, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/clickhouseexporter/internal/sqltemplates/logs_table.sql**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/clickhouseexporter/internal/sqltemplates/logs_table.sql)

The core columns the exporter always writes:

| Column | Type | Source in OTLP envelope |
|---|---|---|
| `Timestamp` | `DateTime64(9)` | `LogRecord.TimeUnixNano` |
| `TraceId` | `String` | `LogRecord.TraceId` |
| `SpanId` | `String` | `LogRecord.SpanId` |
| `TraceFlags` | `UInt8` | `LogRecord.TraceFlags` |
| `SeverityText` | `LowCardinality(String)` | `LogRecord.SeverityText` |
| `SeverityNumber` | `UInt8` | `LogRecord.SeverityNumber` |
| `ServiceName` | `LowCardinality(String)` | `Resource.Attributes["service.name"]` |
| `Body` | `String` | `LogRecord.Body` |
| `ResourceSchemaUrl` | `LowCardinality(String)` | `ResourceLogs.SchemaUrl` |
| `ResourceAttributes` | `Map(LowCardinality(String), String)` | `Resource.Attributes` (all keys) |
| `ScopeSchemaUrl` | `LowCardinality(String)` | `ScopeLogs.SchemaUrl` |
| `ScopeName` | `String` | `ScopeLogs.Scope.Name` |
| `ScopeVersion` | `LowCardinality(String)` | `ScopeLogs.Scope.Version` |
| `ScopeAttributes` | `Map(LowCardinality(String), String)` | `ScopeLogs.Scope.Attributes` |
| `LogAttributes` | `Map(LowCardinality(String), String)` | `LogRecord.Attributes` |
| `EventName` | `String` | `LogRecord.EventName` |

**The five `Scope*` and `ResourceSchemaUrl` columns are required even if empty**. Omitting any of them causes `No such column` insertion errors at runtime.

The ClickHouse user performing inserts requires two grants: `SHOW COLUMNS ON otel.otel_logs` (for schema detection at startup) and `INSERT ... ON otel.otel_logs` (for writes). Missing either causes repeated retry-and-drop cycles in Collector logs.

### Interaction with the `batch` processor
The ClickHouse exporter and the `batch` processor must not both be used with `async_insert` enabled simultaneously - this causes double-buffering. The `batch` processor handles client-side batching; `async_insert` in ClickHouse handles server-side buffering. Use one or the other, not both.

> **Reference**: [ClickHouse exporter README - `async_insert`, **github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/clickhouseexporter/README.md**](https://github.com/open-telemetry/opentelemetry-collector-contrib/blob/main/exporter/clickhouseexporter/README.md)

# Deployment on Kubernetes
## Deployment vs. DaemonSet
The OTel Collector runs as a **Kubernetes Deployment** (not a DaemonSet). The reasons:

- It is a **central gateway**: all Fluent Bit instances across all nodes send to it. There is no per-node work to do.
- A Deployment with 2 replicas provides high availability. Load balancing across replicas is handled by the ClusterIP Service.
- A DaemonSet would waste resources - the Collector does not need local filesystem access to any node.

```
EKS Cluster
├── Node A: fluent-bit-xxxxx (DaemonSet) ─────────────────────+
├── Node B: fluent-bit-yyyyy (DaemonSet) ──────────────────────┼──▶ otel-collector Service (ClusterIP)
├── Node C: fluent-bit-zzzzz (DaemonSet) ─────────────────────+         │
│                                                                  +─────▼──────+
│                                                                  │ Collector  │
│                                                                  │ pod 1      │
│                                                                  ├────────────┤
│                                                                  │ Collector  │
│                                                                  │ pod 2      │
│                                                                  +─────┬──────+
│                                                                        │
│                                                            native TCP :9000
│                                                                        │
│                                                                  ClickHouse
```

## In-cluster DNS name
When the Collector is deployed as a Kubernetes Service of type `ClusterIP`, it receives a stable DNS name:

```
<service-name>.<namespace>.svc.cluster.local:<port>
```

For example:

```
otel-collector.logging.svc.cluster.local:4318
```

This is the address that Fluent Bit's `opentelemetry` output plugin `Host` parameter should be set to. The DNS name is resolved by CoreDNS inside the cluster and is **not reachable from outside the cluster**. The `:4318` port is the OTLP/HTTP receiver port.

The format `svc.cluster.local` is the default cluster domain suffix in Kubernetes. It can be customised per cluster but rarely is in practice.

> **References**:
>
> - [DNS for Services and Pods, **kubernetes.io/docs/concepts/services-networking/dns-pod-service**](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
> - [CoreDNS, **coredns.io**](https://coredns.io/)