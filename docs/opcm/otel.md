[<< **OPCM**](https://pranigopu.github.io/opcm)

<h1>OTel (OpenTelemetry)</h1>

---

**Contents**:

- [Preliminary concepts](#preliminary-concepts)
  - [Externally referenced](#externally-referenced)
  - [Telemetry](#telemetry)
  - [Signal](#signal)
  - [Context](#context)
  - [Cross-cutting concerns](#cross-cutting-concerns)
- [What is OTel?](#what-is-otel)
- [Intent](#intent)
  - [Lineage](#lineage)
  - [Conclusion](#conclusion)
- [Additions to the OTel ecosystem](#additions-to-the-otel-ecosystem)
  - [OTel Collector](#otel-collector)

---

# Preliminary concepts
## Externally referenced
- [Event](./common-terms.md#event)
- [Log](./common-terms.md#log)
- [Span](./common-terms.md#span)
- [Trace](./common-terms.md#trace)

## Telemetry
**See**: [*Telemetry*, **Observability**](https://pranigopu.github.io/observability/telemetry)

## Signal
Category of telemetry, e.g.:

- Metrics
- Logs
- Traces
- Profiles

Essentially, "signal" => "telemetry product of a specific kind".

> Hence, signal => specialised form of telemetry.

> **Reference**: [Signals, **opentelemetry.io/docs/specs/otel/glossary**](https://opentelemetry.io/docs/specs/otel/glossary/#signals)

## Context
A `Context` is a mechanism (standard + implementation) which carries execution-scoped values (i.e. values that emerge from and are specific to an execution context) (1) across API boundaries (i.e. across API-specific contexts) and (2) between logically associated execution units (logical association => association as per some broader process/operation/program/application).

> **CONCEPT: Execution unit**: An umbrella term for the smallest unit of sequential code execution, used in different concepts of multitasking. Examples are threads, coroutines or fibers.
>
> **Reference**: [Execution Unit, **opentelemetry.io/docs/specs/otel/glossary**](https://opentelemetry.io/docs/specs/otel/glossary/#execution-unit)

In other words, a `Context` is a way to share data/information about a particular execution (e.g. a process, operation, program, or application) in a way that allows a consumer to interpret and trace the data/information, be it a consumer in another execution context or as another execution unit. It is a standardised and sufficiently informative way to convey execution-scoped data/information.

> **Reference**: [Context, **opentelemetry.io/docs/specs/otel/context**](https://opentelemetry.io/docs/specs/otel/context/)

## Cross-cutting concerns
Cross-cutting concerns are **aspects** of a program that affect two or more modules, without the possibility of being encapsulated in any of them. These concerns often cannot be cleanly decomposed from the rest of the system in both the design and implementation.

> **CONCEPT: Aspect**: A feature of a program that is not related to the program's primary function. Examples of aspects are logging, data persistence, resilience against system failures, security, etc. An aspect applies across the program's core concerns in a way that cannot be separated from these concerns.

# What is OTel?
OTel is a context propagation framework, consisting of:

- Requirements
- Standards

---

These are concretised by:

- Protocols
- Data models

These are, in turn, exposed and implemented via:

- APIs (expose protocols and data models) **(contract)**
- SDKs (implement APIs) **(implementation of contract)**

Levels of abstraction:

```
(Most abstract / least concrete)
- requirements
- standards
- protocols + data models
- APIs
- SDKs
(Least abstract / most concrete)
```

"OTel" refers to all the above, the requirements and standards at its core.

---

Note that, as a set of requirements and standards, OTel becomes a cross-cutting concern in any application that chooses to adhere to it. The context propagation mechanism of OTel in turn becomes a mechanism for enabling cross-cutting concerns (e.g. monitoring) to share context. In other words, it is a cross-cutting concern that facilitates cross-cutting concerns.

---

> **Reference**: [What is OpenTelemetry?, **opentelemetry.io/docs**](https://opentelemetry.io/docs/what-is-opentelemetry/)

# Intent
## Lineage
I think an overview of OTel's lineage will help here:

```
Dapper (Google, 2010)
-> OpenTracing + Zipkin/Jaeger
-> Merged with OpenCensus (2019)
-> OTel (OpenTelemetry)
```

---

**Dapper (Google, 2010)**:

The motivating problem was the opacity in the workings of large-scale distributed systems; in other words, the problem was scale-induced opacity, i.e. the inability to correlate and causally link signals across a distributed system (often with nested subsystems). Such systems are relevant (as they were in 2010) because large collections of small servers are a cost-efficient platform for a variety of workloads, including Internet service workloads. Understanding the behavior of such a system and reasoning about its performance requires observing activities across many different programs and machines, especially related activities (i.e. activities related to the same search-engine query, activities underlying a service's high availability such as replication and load balancing, etc.).

> **Examples of such systems**:
> 
> - A data ingestion pipeline:
>   - ... from source
>   - ... to proxy servers
>   - ... to distributed streaming
>   - ... to distributed storage
> - An agentic workflow that involves:
>   - A plan
>   - Tool calls
>   - Rerouting to other agents
> - A web search fanning into requests to many:
>   - Query servers
>   - Spell-checking
>   - Subsystems to manage image and video searches

2 fundamental requirements for Dapper to address its motivating problem:

1. Ubiquitous deployment
  > The usefulness of tracing depends on all parts of the system being monitored.
2. Continuous monitoring
  > To ensure unusual (hard-to-replicate) behaviour is always captured.

3 concrete design goals emerge from these requirements:

1. Low overhead
2. Application-level transparency (not code/program-level)
  > Tracing happens without programmers needing to be aware of the tracing system.
3. Scalability

Additional goal: high availability of trace data.

> Freshness enables more responsive monitoring.

Dapper is a tracing *infrastructure*, i.e. it is a platform, not just a framework (it was initially conceived as a tracing tool but eventually evolved into a platform). To quote from a review of Dapper's original paper, [Dapper: Google's Large-Scale Distributed Tracing Infrastructure, **darshshah.org/blog/2024/11/04/dapper-distributed-tracing-infrastructure**](https://darshshah.org/blog/2024/11/04/dapper-distributed-tracing-infrastructure/): "Dapper is Google’s production distributed tracing system that provides low-overhead, application-transparent instrumentation across Google’s massive infrastructure. Originally conceived as a tracing tool, it evolved into a general-purpose monitoring platform – enabling engineers to understand the behavior of complex distributed workloads, diagnose performance issues, and discover faults across systems where a single query may touch thousands of backend services."

> **References**:
>
> - ["Dapper, a Large-Scale Distributed Systems Tracing Infrastructure" by Benjamin H. Sigelman, Luiz Andre Barroso, Mike Burrows, et al. (Google Technical Report, 2010)](https://research.google/pubs/dapper-a-large-scale-distributed-systems-tracing-infrastructure/)
> - [Dapper: Google's Large-Scale Distributed Tracing Infrastructure, **darshshah.org/blog/2024/11/04/dapper-distributed-tracing-infrastructure**](https://darshshah.org/blog/2024/11/04/dapper-distributed-tracing-infrastructure/)

---

**OpenTracing + Zipkin/Jaeger**:

The Dapper paper described Google's internal system but was never released as software. In other words, to people outside Google, it was a blueprint, not an artifact/codebase. Zipkin (Twitter, 2012) was the first released production-grade implementation of Dapper's ideas (Zipkin's design is explicitly based on the Google Dapper paper). It is a a distributed tracing system, and its motivation is to trace timing data needed to troubleshoot latency problems in microservice architectures (Zipkin manages both the collection and the lookup of this data).

> **Reference**: [ZipkinProposal, **cwiki.apache.org/confluence/spaces/INCUBATOR/pages/110694505/ZipkinProposal**](https://cwiki.apache.org/confluence/spaces/INCUBATOR/pages/110694505/ZipkinProposal)

Jaeger (Uber, open-sourced 2016-17) is another distributed tracing platform created by Uber Technologies and donated to Cloud Native Computing Foundation (CNCF), explicitly inspired by Google Dapper paper and the OpenZipkin community.

> **Reference**: [CNCF hosts Jaeger, **www.cncf.io/blog/2017/09/13/cncf-hosts-jaeger**](https://www.cncf.io/blog/2017/09/13/cncf-hosts-jaeger/)

Now, the problem was no longer the lack of tooling but fragmentation at the instrumentation layer (instrumentation layer => the set/ecosystem of standards, formats, and mechanisms to collect, process, and store tracing/telemetry). Before a shared standard, tracing solutions like Jaeger and Zipkin had their own instrumentation formats that were not interoperable. OpenTracing (launched 2016) was a response to this problem: a vendor-neutral API specification that let developers instrument code once and reuse it across backends ("instrumenting code" => adding statements to a program's code that record what happened and when, purely for observability; they do not change the program's logic or output, they only emit data about its execution separately from its intended output).

OpenTracing core specification is intentionally *agnostic* about the specifics of the downstream tracing or monitoring systems, because OpenTracing is meant to describe the semantics of transactions (i.e. the information-encoding/interpretation structure) in distributed systems, independent of how a given backend chooses to process or store them. As per [OpenTracing Overview: Distributed Tracing’s Emerging Industry Standard, **sematext.com/blog/opentracing-distributed-tracing-emerging-industry-standard**](https://sematext.com/blog/opentracing-distributed-tracing-emerging-industry-standard/): *"OpenTracing is a set of standards and techniques that allow for distributed tracing in a way that’s free of vendor lock-in. For that purpose, a coherent API specification is provided for numerous programming languages and frameworks."*

> **References**:
>
> - [What Is OpenTracing? A Walk Down Tracing History, **www.honeycomb.io/blog/what-is-opentracing**](https://www.honeycomb.io/blog/what-is-opentracing)
> - [OpenTracing Overview: Distributed Tracing’s Emerging Industry Standard, **sematext.com/blog/opentracing-distributed-tracing-emerging-industry-standard**](https://sematext.com/blog/opentracing-distributed-tracing-emerging-industry-standard/)

Structurally, OpenTracing modeled a **Trace** as a directed acyclic graph of **Spans**, connected by typed **References**, the two reference types being *ChildOf* (a strict parent-child call relationship) and *FollowsFrom* (a causal but non-blocking relationship, e.g. fire-and-forget async work). This is a generalisation of Zipkin's native span data model, which was a tree, not a DAG: a flat structure with each `span` carries a `traceId`, its own `id`, and a single `parentId` (null for the root) (there was no way in Zipkin to express "this span is causally related to another span but does not strictly nest under it). OTel generalised this further using the mechanism of **Span Links**, a typed pointer from one span to one or more other spans (each carrying the target's `SpanContext` (an encapsulation of any information needed to refer to a distinct **Span** across a process boundary, potentially with additional information) plus optional attributes), which can point across trace boundaries, something OpenTracing's references could not do, since ChildOf/FollowsFrom only described relationships within a single trace.

> **References**:
>
> - [The OpenTracing Semantic Specification, **github.com/opentracing/specification/blob/master/specification.md**](https://github.com/opentracing/specification/blob/master/specification.md) (contains reference for OpenTracing's data model)
> - [Span Links - Connect Async and Batch OpenTelemetry Spans, **signoz.io/docs/traces-management/guides/span-links**](https://signoz.io/docs/traces-management/guides/span-links/) (contains reference for **Span Links** in OTel)
> - [Port 9411: Zipkin — The Watcher of Distributed Systems, **ww.connected.app/ports/9411**](https://ww.connected.app/ports/9411) (contains reference for Zipkin's data model)

---

OpenCensus is a set of libraries for various languages that allow you to collect application metrics and distributed traces, then transfer the data to a backend of your choice in real time. OpenCensus and OpenTracing were later merged into a single project, OTel (OpenTelemetry) (with a supported migration path provided).

> **References**:
>
> - [Merging OpenTracing and OpenCensus: A Roadmap to Convergence, **medium.com/opentracing/a-roadmap-to-convergence-b074e5815289**](https://medium.com/opentracing/a-roadmap-to-convergence-b074e5815289)
> - [OpenTelemetry: The Merger of OpenCensus and OpenTracing, **opensource.googleblog.com/2019/05/opentelemetry-merger-of-opencensus-and.html**](https://opensource.googleblog.com/2019/05/opentelemetry-merger-of-opencensus-and.html)

## Conclusion
OTel comes from a lineage of distributed tracing solutions, from tools and platforms to API specifications to libraries and, finally, to a framework that combines requirements, standards, specifications, and libraries, along with tooling (e.g. OTel Collector, which shall be discussed below). The goal was always this: **ensure a complex, distributed system does not remain a blackbox**. The intent of OTel (following from OpenTracing's and OpenCensus' goals) was to ensure such transparency could be achieved in a ubiquitous (i.e. arbitrarily scalable and granular), continuous, and vendor-neutral manner, from the level of specifications to libraries to tooling. Furthermore, OTel generalises beyond traces, serving as a framework for all levels of telemetry (logs, events, metrics, traces, etc.) while also serving the intent of sharing context in a traceable and interpretable manner across components and cross-cutting concerns in a distributed system.

# Additions to the OTel ecosystem
## OTel Collector
The OpenTelemetry Collector is a **vendor-neutral telemetry processing agent**: a stateless binary that receives telemetry (logs, metrics, traces), optionally transforms it, and exports it to one or more backends. It is not an SDK, not an observability backend, and does not store data.

As discussed above, OTel itself is a CNCF-graduated open-source framework that standardises how telemetry is collected, processed, and exported across distributed systems. The OTel Collector is an add-on (not core) component of this framework - specifically, it is an add-on component that handles the part of the pipeline tying remote data sources to storage backends.

> **References**:
>
> - [OpenTelemetry Collector, **opentelemetry.io/docs/collector**](https://opentelemetry.io/docs/collector/)
> - [OpenTelemetry CNCF project, **cncf.io/projects/opentelemetry**](https://www.cncf.io/projects/opentelemetry/)

The Collector exists in two distributions: `core` (maintained by the OTel project, fewer components) and `contrib` (maintained by the community, includes the ClickHouse exporter and `k8s_attributes` processor). For EKS + ClickHouse use cases, the `contrib` distribution is required.

> **Reference**: [*opentelemetry-collector-contrib*, **github.com/open-telemetry**](https://github.com/open-telemetry/opentelemetry-collector-contrib)
