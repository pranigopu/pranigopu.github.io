[<< **Observability**](https://pranigopu.github.io/observability)

<h1>Common Terms</h1>

---

**Contents**:

- [Observability building-blocks](#observability-building-blocks)
  - [Event](#event)
  - [Log](#log)
  - [Span](#span)
  - [Trace](#trace)
- [Measurement](#measurement)
- [Metric](#metric)
- [Monitoring](#monitoring)
- [Record](#record)
- [Time series](#time-series)
- [Transmission](#transmission)

---

# Observability building-blocks
## Event
Events are structured logs (see: ["Log"](#log)). They follow a standardised format (e.g. JSON).

> **Reference**: [Unpacking Observability: Understanding Logs, Events, Traces, and Spans, **medium.com/dzerolabs/observability-journey-understanding-logs-events-traces-and-spans-836524d63172**](https://medium.com/dzerolabs/observability-journey-understanding-logs-events-traces-and-spans-836524d63172)

## Log
Logs are human-readable flat text data that are used by developers to capture useful data that may be unstructured (e.g. error messages, console outputs for the program's progress information, etc.). Logs messages occur at a single point in time. Log formats are not standardised across languages or frameworks, so they can be hard to parse and challenging to query.

> **Reference**: [Unpacking Observability: Understanding Logs, Events, Traces, and Spans, **medium.com/dzerolabs/observability-journey-understanding-logs-events-traces-and-spans-836524d63172**](https://medium.com/dzerolabs/observability-journey-understanding-logs-events-traces-and-spans-836524d63172)

## Span
A span represents a unit of work. They can be thought of as the work being done during an operation's execution. Logs (see: ["Log"](#log)) represent occurrences at a specific point in time. Events (see: ["Event"](#event)) are not that much more useful, other than being easier to read and query. A span, on the other hand, is captured information for a given block of time.

> **NOTE**: A span is the basic building block of a trace (see: ["Trace"](#trace)). A trace is made up of a tree of spans, starting with a root span (i.e. span with no parent), which encapsulates the end-to-end time that it takes to accomplish a task.

> **Reference**: [Unpacking Observability: Understanding Logs, Events, Traces, and Spans, **medium.com/dzerolabs/observability-journey-understanding-logs-events-traces-and-spans-836524d63172**](https://medium.com/dzerolabs/observability-journey-understanding-logs-events-traces-and-spans-836524d63172)

## Trace
Traces (also known as distributed traces) are data are a type of data that captures requests as they flow through a system, capturing key steps across services and components. They help identify errors, slowdowns, and bottlenecks, allowing IT teams to resolve problems quickly and optimise performance.

> **Reference**: [What Are Traces in Observability? Concepts and Examples, **edgedelta.com/company/knowledge-center/what-are-traces-in-observability**](https://edgedelta.com/company/knowledge-center/what-are-traces-in-observability)

A span (see: ["Span"](#span)) is the basic building block of a trace (see: ["Trace"](#trace)). A trace is made up of a tree of spans, starting with a root span (i.e. span with no parent), which encapsulates the end-to-end time that it takes to accomplish a task.

> **Reference**: [Unpacking Observability: Understanding Logs, Events, Traces, and Spans, **medium.com/dzerolabs/observability-journey-understanding-logs-events-traces-and-spans-836524d63172**](https://medium.com/dzerolabs/observability-journey-understanding-logs-events-traces-and-spans-836524d63172)

# Measurement
Measurement is the act or product of relating a known/fixed instance of an attribute to an unknown/variable instance of the same. For example, we can know/fix a "second" by marking it perceptually or via a pendulum swing, and through relating N seconds to a span of events that occur, we can measure the time it took for the events to occur. As another example, we can know/fix a meter perceptually, maybe by reference to a "standard" object or marking, and through relating N meters to a one-dimensional span of space, we can measure the length of this space.

# Metric
A metric is a function that maps measurement(s) (see: ["Measurement"](#measurement)) and/or their results to a value meant to carry a specific kind of information. For example, "CPU usage percentage" is a metric, which defines the kind of information we get from measuring and operating on CPU usage seconds, CPU core usage, etc. 20% is a value this metric can take, not the metric itself.

> **NOTE**: A metric can be a function of other metrics. Furthermore, a metric is either a pure number or is attached to a unit; either way, it conveys a specific kind of information whose semantics are well-defined. A random set of operations on measurements is not a metric.

# Monitoring
Monitoring is the practice/process of regularly seeking data/information about a system (not just *from* a system but *about* a system, i.e. it is regularly repeated observation (direct or indirect) of an object with the object itself being the concern). More concretely, it is the practice/process of collecting, processing, and alerting on data/information about systems.

> **NOTE**: "Regular" here is used broadly to mean "repeatedly and reliably (i.e. within a known interval/range of intervals/trigger(s))". This applies to fixed interval-based repetition, repetition that is regular within a bounded range of intervals (e.g. anytime within 24 hours), or event-driven (e.g. triggered by an event).

> **SIDE NOTE**: This data/information can be [telemetry](./telemetry.md).

# Record
Persistent encapsulation of an event/set of events, e.g.:

- Note
- Log
- Mark
- [Measurement](#measurement) (yes, measurements can be a kind for record)

# Time series
A chronologically ordered sequence data points.

# Transmission
To transmit is to propagate a signal (i.e. an intentional, interpretable disturbance) through a medium. It serves as a means of communicating between entities that can and do access the medium's state. A medium can be any phenomenon for which a baseline state can be defined, with respect to which "disturbances" (i.e. deviations) can be identified. A radio wave has an unmodulated state which can be differentiated from a modulated one. A copper wire has an "unexcited" state which can be differentiated from "excitation" (e.g. electric pulse(s)). What separates a transmission from noise is intentionality; when a disturbance cannot be mapped to a known lexicon, it is probably noise, and when it can, it is probably a signal. I say "probably", because noise can be signal-like (although this is rare, in many domains), and signals can be misinterpreted as noise, or can become corrupted/distorted into noise-like disturbance. 

> **NOTE**: This reframes "intentionality" as an inference from decodability, since the sender's intent (if any) may not be directly observable to the receiver.