[<< **Observability**](https://pranigopu.github.io/observability)

<h1>Prometheus</h1>

---

**Contents**:

- [Preliminary concepts](#preliminary-concepts)
  - [Externally referenced](#externally-referenced)
  - [Label in Prometheus](#label-in-prometheus)
  - [Time series in Prometheus](#time-series-in-prometheus)
  - [Sample in Prometheus](#sample-in-prometheus)
- [Prometheus](#prometheus)

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

# Prometheus
Prometheus is an end-to-end opinionated dimensional time series metrics monitoring system.

> **NOTE**: Prometheus is an open-source project.

---

- "End-to-end" means that, as a system, Prometheus handles/can handle monitoring from metrics collection and storage to downstream use (e.g. querying and alerting). Collection is handled by its service discovery (for scrape targets) and scraping mechanism, storage is handled by its custom time series database (TSDB), and downstream use is enabled by its query engine that uses a custom-built language, PromQL.
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