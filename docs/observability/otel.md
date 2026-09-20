[<< **Observability**](https://pranigopu.github.io/observability)

<h1>OTel (OpenTelemetry)</h1>

---

**Contents**:

- [Preliminary concepts](#preliminary-concepts)
  - [Telemetry](#telemetry)
  - [Signal](#signal)
  - [Context](#context)
  - [Cross-cutting concerns](#cross-cutting-concerns)
- [What is OTel?](#what-is-otel)

---

# Preliminary concepts
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