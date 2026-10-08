[<< **Getting into Observability**](https://pranigopu.github.io/getting-into-observability/)

> **Parent document**: [*OTel Collector*, **Getting into Observability**](./otel-collector.md)

<h1>Prometheus Receiver</h1>

---

**Contents**:

- [About](#about)
- [Prometheus data model -\> OTLP format](#prometheus-data-model---otlp-format)
  - [Overall](#overall)
  - [Special mappings for resource and scope](#special-mappings-for-resource-and-scope)
- [Configuration](#configuration)
  - [`scrape_config`: Defining what targets to scrape](#scrape_config-defining-what-targets-to-scrape)
  - [`metric_relabel_configs`](#metric_relabel_configs)
  - [Additional configurations](#additional-configurations)
  - [Scalability via Target Allocator](#scalability-via-target-allocator)

---

# About

The Prometheus receiver acts as a functional, minimal replacement for the Prometheus server (see: [`_docs/prometheus.md`](./prometheus.md)) that ensures the data it scrapes and collects as Prometheus objects is converted to OTel Collector objects, allowing is to align this data to the OpenTelemetry standard. To put it simply, it lets the OpenTelemetry Collector act like a Prometheus server by scraping any Prometheus-compatible endpoint then converting the metrics into the OpenTelemetry Protocol (OTLP), and sends them through the Collector's pipelines for further processing and export. In other words, you use Prometheus's scraping and discovery mechanisms and at the same time benefit from OpenTelemetry's flexibility and interoperability.

> **Reference**: [*Collecting Prometheus Metrics with the OpenTelemetry Collector*, **www.dash0.com/guides**](https://www.dash0.com/guides/opentelemetry-prometheus-receiver)

# Prometheus data model -> OTLP format
## Overall
The Prometheus receiver converts metrics from the Prometheus data model (see: ["Prometheus data model", *Prometheus*, **Getting into Observability**](./prometheus.md#prometheus-data-model)) into the OTLP format. Essentially, the Prometheus series's labels become attributes: Every label of the Prometheus series is transformed into a key-value attribute on the corresponding OTLP metric data point.

Metric types (see: ["OTel data point types for metrics", *OTel*, **Getting into Observability**](./otel.md#otel-data-point-types-for-metrics)) are translated:

- Prometheus `counter` -> OTLP `Sum` (cumulative and monotonic)
- Prometheus `gauge` -> OTLP `Gauge`
- Prometheus `histogram` -> OTLP `Histogram`
- Prometheus `summary` -> OTLP `Summary`

E.g.: Consider this Prometheus counter metric exposed by the Node Exporter:

> **SIDE NOTE**: Prometheus Node Exporter is a lightweight, open-source agent designed to collect and expose hardware and OS metrics (such as CPU, memory, disk I/O, and network usage) from Linux and Unix-based servers. It runs on the host machine and serves system metrics on port 9100, which are then scraped and stored by Prometheus.
>
> **Source**: [`prometheus`/`node_exporter`, **github.com**](https://github.com/prometheus/node_exporter)

```
node_cpu_seconds_total{cpu="0", mode="system"} 15342.85
```

When scraped by the Prometheus receiver:

- It is converted to an OTLP `Sum` type
- With attributes preserved (as seen via the `debug` exporter)
  > **NOTE**: The `debug` exporter is an exporter for OTel Collector.

```
Metric #63
Descriptor:
Name: node_cpu_seconds_total
     -> Description: Seconds the CPUs spent in each mode.
     -> Unit:
     -> DataType: Sum
     -> IsMonotonic: true
     -> AggregationTemporality: Cumulative
NumberDataPoints #0
Data point attributes:
     -> cpu: Str(0)
     -> mode: Str(system)
StartTimestamp: 2025-10-12 04:39:33.432 +0000 UTC
Timestamp: 2025-10-12 04:44:42.412 +0000 UTC
Value: 15342.85
```

> **Reference**: [Collecting Prometheus Metrics with the OpenTelemetry Collector, **www.dash0.com/guides/opentelemetry-prometheus-receiver**](https://www.dash0.com/guides/opentelemetry-prometheus-receiver)

## Special mappings for resource and scope
The receiver also looks for certain metrics and labels that carry additional context about where the telemetry originated. These are used to populate Resource and Scope attributes in OTLP and enrich your data with metadata about the source and instrumentation. The Resource attributes (`ResourceAttributes`, `ResourceSchemaUrl`, etc.) contain details about the data source itself, whereas the Scope (Instrumentation Scope) attributes (`ScopeName`, `ScopeVersion`, etc.) contain details about the library or component that produced the data.

- `target_info` for Resource attributes (if exposed)
  > **NOTE**: `target_info` often auto-added via service discovery if the target exports it.
- `otel_scope_info` for Scope attributes
  > **NOTE**: May or may not be present in metrics labels.

# Configuration
The Prometheus receiver's configuration closely it mirrors Prometheus's own configuration model. You can take the same configuration that would normally live in a `prometheus.yaml` file and place it under the `config:` key in your OTel Collector setup. Key configuration fields that determine what targets get scraped, how collected metrics data is labelled and what metrics get passed downstream are:

- `scrape_configs`
- `metric_relabel_configs`

> **Reference**: [*Collecting Prometheus Metrics with the OpenTelemetry Collector*, **www.dash0.com/guides/opentelemetry-prometheus-receiver**](https://www.dash0.com/guides/opentelemetry-prometheus-receiver)

## `scrape_config`: Defining what targets to scrape
- Defines what the receiver scrapes and how it does it
- Each entry represents a scrape job with its own targets and parameters

> **Reference**: [Collecting Prometheus Metrics with the OpenTelemetry Collector, **www.dash0.com/guides/opentelemetry-prometheus-receiver**](https://www.dash0.com/guides/opentelemetry-prometheus-receiver)

## `metric_relabel_configs`
- `metric_relabel_configs` operates on the scraped data itself
- It lets you drop or modify metrics:
    - After they have been collected
    - Before they are sent through the pipeline
- This is useful for cleaning up noisy or high-cardinality metrics early

> **Reference**: [Collecting Prometheus Metrics with the OpenTelemetry Collector, **www.dash0.com/guides/opentelemetry-prometheus-receiver**](https://www.dash0.com/guides/opentelemetry-prometheus-receiver)

## Additional configurations
- `trim_metric_suffixes`
- `use_start_time_metric`

> **Reference**: [Collecting Prometheus Metrics with the OpenTelemetry Collector, **www.dash0.com/guides/opentelemetry-prometheus-receiver**](https://www.dash0.com/guides/opentelemetry-prometheus-receiver)

## Scalability via Target Allocator

[TO BE CONTINUED]
