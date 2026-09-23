[<< **OPCM**](https://pranigopu.github.io/opcm)

<h1>Prometheus</h1>

---

**Contents**:

- [Preliminary concepts](#preliminary-concepts)
  - [Externally referenced](#externally-referenced)
  - [Label in Prometheus](#label-in-prometheus)
  - [Time series in Prometheus](#time-series-in-prometheus)
  - [Sample in Prometheus](#sample-in-prometheus)
- [What is Prometheus?](#what-is-prometheus)
- [Intent](#intent)
- [Design-level details](#design-level-details)
  - [Prometheus server as the core](#prometheus-server-as-the-core)
  - [Prometheus data model](#prometheus-data-model)

---

# Preliminary concepts
## Externally referenced
- [Measurement](./common-terms.md#measurement)
- [Metric](./common-terms.md#metric)
- [Monitoring](./common-terms.md#monitoring)
- [Record](./common-terms.md#record)
- [Time series](./common-terms.md#time series)

## Label in Prometheus
Key-value pairs attached to a metric.

> **NOTE**: A label is a specific key-value pair. Hence:
>
> - `container_name` is not a label
> - `{container_name: "my-precious"}` is a label

## Time series in Prometheus
Metric + labels (or rather, labels attached to a metric).

> **NOTE**: 1 metric can be associated with 1+ time series.

## Sample in Prometheus
A specific metric value + specific labels.

> **NOTE**: Sample here => Prometheus-formatted record.

# What is Prometheus?
Prometheus is an end-to-end opinionated dimensional time series metrics monitoring system.

> **NOTE**: Prometheus is an open-source project.

---

- "End-to-end" means that, as a system, Prometheus handles/can handle monitoring from metrics collection and storage to downstream use (e.g. querying and alerting). Collection is handled by its service discovery (to discover scrape targets) and scraping mechanism, storage is handled by its custom time series database (TSDB), and downstream use is enabled by its query engine that uses a custom-built language, PromQL.
- "Opinionated" => Committing to specific choices about its monitoring process.
    > Trading flexibility for reliability.
  1. Pull (target scraping) over push (writes from target) as the default
  2. Focus on metrics only, no logs or traces
  3. Time series structure (see below)
  4. No built-in distributed storage or clustering
        > **KEY IDEA**: The monitoring system must survive when everything else is failing, and long-term global storage, cross-cluster querying, and durability are treated as separate concerns to be solved by other tools (e.g. Thanos for distributed storage).
        > 
        > "Prometheus is designed for reliability, to be the system you go to during an outage to allow you to quickly diagnose problems. Each Prometheus server is standalone, not depending on network storage or other remote services. You can rely on it when other parts of your infrastructure are broken, and you do not need to set up extensive infrastructure to use it."
        > 
        > **Reference**: ["When does it fit?", Overview, **prometheus.io/docs/introduction/overview**](https://prometheus.io/docs/introduction/overview/#when-does-it-fit)
     1. Prometheus server is self-contained and autonomous
     2. Operates as a standalone time series database
  5. Low-cardinality label optimisation
        > **NOTE**: "Cardinality" => "Number of unique values"
     1. High-cardinality labels cause issues:
        1. Proliferation of time series
        2. Low compression ratio for stored samples
        3. Worse query performance
     2. Prometheus prefers reliability over detail-richness
  6. Reliability over detailed accuracy
        > Ties to low-cardinality label optimisation.
        > 
        > "Prometheus values reliability. You can always view what statistics are available about your system, even under failure conditions. If you need 100% accuracy, such as for per-request billing, Prometheus is not a good choice, as the collected data will likely not be detailed and complete enough."
        > 
        > **Reference**: ["When does it not fit?", Overview, **prometheus.io/docs/introduction/overview**](https://prometheus.io/docs/introduction/overview/#when-does-it-not-fit)
  7. Built-in query language (PromQL)
     1. Custom DSL built around custom data model
     2. Centers on aggregation-first dimensional filtering

- "Dimensional" as applied to time series metrics means that each time series metric is associated with one or more dimensions (i.e. attributes, or labels in Prometheus' terminology) besides the value itself, which allows for dimensional querying (i.e. querying by one or more dimensions, e.g. querying CPU usage in Kubernetes by container name).
- "Time series metrics" is a key constraint: Prometheus is purpose-built for time series metrics, and the time series structure is enforced at ingestion itself, not downstream. For example, by default, the Prometheus server's processor rejects out-of-order samples, requiring samples to be ingested in chronological order (as per their respective time series) before they are processed further.
    > **SIDE NOTE**: Now, however, Prometheus does have support (as a non-default configuration) for out-of-order samples, which involves a dedicated out-of-order sample-handling logic and separation from in-order samples.
    > 
    > **References**:
    > 
    > - [Add out-of-order sample support to the TSDB #11075, **github.com/prometheus/prometheus/pull/11075**](https://github.com/prometheus/prometheus/pull/11075)
    > - [*New in Grafana Mimir: Introducing out-of-order sample ingestion, **grafana.com/blog/new-in-grafana-mimir-introducing-out-of-order-sample-ingestion**](https://grafana.com/blog/new-in-grafana-mimir-introducing-out-of-order-sample-ingestion/) (despite appearances, this is about Prometheus' out-of-order sample ingestion feature as well)

---

> **References**:
>
> - [Prometheus design and philosophy, **https://www.slideshare.net/slideshow/prometheus-design-and-philosophy/66970367**](https://www.slideshare.net/slideshow/prometheus-design-and-philosophy/66970367)
> - [Overview, **prometheus.io/docs/introduction/overview**](https://prometheus.io/docs/introduction/overview/)
> - [Storage, **prometheus.io/docs/prometheus/latest/storage**](https://prometheus.io/docs/prometheus/latest/storage/)
> - [Comparison to alternatives, **prometheus.io/docs/introduction/comparison**](https://prometheus.io/docs/introduction/comparison/)

# Intent
Prometheus was developed at SoundCloud (2012) to address the need to monitor a dynamic cloud environment. As stated in [Service Discovery Integration, **training.promlabs.com/training/introduction-to-prometheus/prometheus-an-overview/service-discovery-integration**](https://training.promlabs.com/training/introduction-to-prometheus/prometheus-an-overview/service-discovery-integration/), modern dynamic cloud environments create new challenges for monitoring systems:

- On-demand VMs are scaled up and down as required
- Service instances are <br> - dynamically scheduled onto hosts <br> - by container orchestrators <br> - such as Kubernetes, Docker Swarm, or Mesos
   > **NOTE**: The context here is a compute cluster (i.e. multiple physical/virtual machines working in a coordinated manner to address a common need or set of needs). "Service instance" is a copy of a service (i.e. an application or request-serving program). "Hosts" are the actual physical/virtual machines in the cluster in which workloads (processes, programs, and services) can run. "Dynamical scheduling" means that the decision of which service instance runs on which host is made automatically, during runtime, not by a fixed assignment before runtime.
- Microservices lead to a growing number of individual services to operate and monitor

These lead to a few concerns a monitoring system must address:

1. **Dynamic components/attributes** (i.e. target/topology churn): For a monitoring system to work in such an environment, it must know - dynamically, during runtime (matching the dynamic scheduling described above as well as the potential proliferation of microservices) - which machines and/or service instances currently exist, what their identity is, and how to fetch metrics from them. Given the dynamic, automated nature of service instance scheduling as well as a non-static microservices architecture, monitoring targets cannot be statically configured.
2. **Unknown dimensional hierarchy** (i.e. label/schema churn): We may know what metrics we want to capture, but since a dynamic cloud environment as such has no fixed architecture and has a number of components with dynamically defined attributes (e.g. service instances -> host mapping, host actually available, microservices actually available, etc.), the labelling (i.e. defining the dimensions) of these metrics (for downstream querying) must be flexible rather than adhering to a known hierarchy/structure of dimensions. The metrics are known, the dimensions are not.

---

Prometheus addresses these concerns as follows:

**1. Dynamic components/attributes**:

- Service discovery as a first-class subsystem
   > Rather than treating "keep the target list current" as an added requirement on top of the core architecture, Prometheus has dynamic target discovery built into its core architecture, serving three distinct purposes: (1) building a view of what targets should exist (so it can detect and alert when one is unexpectedly missing, not just silently stop scraping it), (2) gaining the technical information needed to pull metrics from a target over HTTP, and (3) enriching the collected series with labelled metadata about the target's identity (this ties to the modelling of dimensions via labels discussed below: service discovery does not just find targets, it auto-populates the labels (pod name, namespace, node, etc.) that make those targets queryable and distinguishable once scraped).
   > 
   > **Reference**: [Service Discovery Integration, **training.promlabs.com/training/introduction-to-prometheus/prometheus-an-overview/service-discovery-integration**](https://training.promlabs.com/training/introduction-to-prometheus/prometheus-an-overview/service-discovery-integration/)
- Pull-based metrics collection as the core default:
   > By default, time series collection in Prometheus happens via a pull model over HTTP. A pull-based system with continuous service discovery can actively verify what is currently running and treat an expected-but-missing target as a monitoring signal in its own right, whereas a push-based model has no such built-in mechanism to notice absence (it only knows about things that are actively reporting in). Thus, Prometheus, by default (a strongly supported default), adopts a proactive approach to metrics collection, rather than a passive/reactive approach.

**2. Unknown dimensional hierarchy**:

- Rather than encoding dimensions within a metric's name (e.g. `stats.api-server.tracks.post.500` for tracking the number of HTTP requests to API servers with the response code 500 and the method POST to the /tracks endpoint, an encoding seen in [Graphite](https://graphite.readthedocs.io/en/latest/)) and/or aggregating over instances (to gloss over the dynamic scheduling/creation/destruction), Prometheus encodes dimensions as key-value pairs (labels) (including instance identifiers, potentially) attached to a metric name (e.g. `api_server_http_requests_total{method="POST",handler="/tracks",status="500",instance="<sample1>"} `).
   > => This approach uses flat labels for dimensions rather than dimensional hierarchy.
   > 
   > **References**:
   > 
   > - [Comparison to alternatives, **prometheus.io/docs/introduction/comparison**](https://prometheus.io/docs/introduction/comparison/)
   > - [Prometheus design and philosophy, **https://www.slideshare.net/slideshow/prometheus-design-and-philosophy/66970367**](https://www.slideshare.net/slideshow/prometheus-design-and-philosophy/66970367)
- To leverage this flexible multi-dimensional labelling approach for querying, Prometheus ships with its own query engine and query language (PromQL), which is a non-SQL query language that is more efficient for aggregations and multi-dimensional filtering/grouping.
   > **References**:
   > 
   > - [Prometheus design and philosophy, **https://www.slideshare.net/slideshow/prometheus-design-and-philosophy/66970367**](https://www.slideshare.net/slideshow/prometheus-design-and-philosophy/66970367)
   > - [Querying basics, **prometheus.io/docs/prometheus/latest/querying/basics**](https://prometheus.io/docs/prometheus/latest/querying/basics/)

---

On top of this, Prometheus was intended to serve as a self-contained monitoring system, with no in-built dependencies and with an efficient, low-footprint metrics processing and storage system. As stated in [Prometheus design and philosophy, **https://www.slideshare.net/slideshow/prometheus-design-and-philosophy/66970367**](https://www.slideshare.net/slideshow/prometheus-design-and-philosophy/66970367), a key goal was for Prometheus to be an operational monitoring system, with a key pain point to address being the unsatisfactory efficiency of the existing monitoring solutions. As stated in its official overview:

> "Prometheus is designed for reliability, to be the system you go to during an outage to allow you to quickly diagnose problems. Each Prometheus server is standalone, not depending on network storage or other remote services. You can rely on it when other parts of your infrastructure are broken, and you do not need to set up extensive infrastructure to use it."
>
> **Reference**: ["When does it fit?", Overview, **prometheus.io/docs/introduction/overview**](https://prometheus.io/docs/introduction/overview/#when-does-it-fit)

---

All-in-all, Prometheus was intended to be a reliable and lightweight monitoring system for a dynamic cloud environment, with a focus on self-reliance (via service discovery, the proactive pull-based metrics collection model, single-binary shipping (no external dependencies)) and a flexible multi-dimensional model + query language to account for the inherent dynamism and the range of architectures that may be used in a dynamic cloud environment.

---

To support this intent, Prometheus has some important features:

- "Time series metrics" constraint applied from ingestion
   > Ensures that downstream ordering of metrics by time and time-range selection are efficient, which is an important feature for a continuous monitoring system to have (because the TSDB can then rely on strict chronological append-order per series, enabling contiguous storage/indexing rather than needing to re-sort samples or handle interleaving time ranges among samples at query time).
- Target relabelling
   > Allows the selection/filtering/altering of scrape targets after the initial service discovery, ensuring that the scrape targets can be narrowed down to what is desired rather than whatever the service discovery brings up. This helps prevent unintended bloat in the metrics collection and storage, which is important for reliability and efficiency.
- Metrics relabelling
   > Allows for the selection/filtering/altering of metrics after their collection. This is another mechanism to allow for selectivity and prevent unintended bloat, just further downstream compared to target relabelling.

# Design-level details
## Prometheus server as the core
The core component of a Prometheus deployment is the Prometheus server, which is a process (shipped as a single binary, without dependencies, true to the self-reliant, self-contained design philosophy) that performs the core functions of Prometheus:

1. Metrics retrieval, which involves:
   1. Service discovery of scrape targets
   2. Scraping the targets for metrics
2. Metrics storage in the TSDB
   > This TSDB is persisted on disk.
3. Query engine (that uses PromQL as its query language)

Additionally, the Prometheus server also contains:

- Prometheus web UI, which includes:
  - PromQL querying
  - Basic graphing based on queries
  - Viewing active targets
  - Viewing configured alerts
- A mechanism to define alert rules and fire alerts

Other functions are shipped as separate processes, e.g.:

- Pushgateway that enables:
  - Data sources to push metrics to it
  - Prometheus to scrape these via Pushgateway's endpoints
   > Intended for metrics pushed by short-lived jobs that could disappear before a scrape can happen.
- Prometheus web UI
- Alertmanager (to which Prometheus can send its alerts)
   > Manages downstream use-cases (e.g. emailing notifications).
- Dashboarding and graphing via Grafana

## Prometheus data model
Time series are frequently identified using this notation:

```
<metric name>{<label name>="<label value>", ...}
```

For example, given:

- Metric name: `api_http_requests_total`
- Labels: `method="POST"` and `handler="/messages"`

This is written as:

```
api_http_requests_total{method="POST", handler="/messages"}
```

> **NOTE**:
> 
> - Each unique combination of metric name and label values defines a distinct **series**
> - This identity is created at write time and persists in memory

---

> **Reference**: [Data model, **prometheus.io/docs/concepts/data_model**](https://prometheus.io/docs/concepts/data_model/)

