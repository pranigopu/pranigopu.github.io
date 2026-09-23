[<< **OPCM**](https://pranigopu.github.io/opcm)

<h1>Telemetry</h1>

---

**Contents**:

- [Preliminary concepts](#preliminary-concepts)
  - [Externally referenced](#externally-referenced)
  - [Remote source](#remote-source)
- [Telemetry](#telemetry)

---

# Preliminary concepts
## Externally referenced
- [Measurement](./common-terms.md#measurement)
- [Record](./common-terms.md#record)
- [Transmission](./common-terms.md#transmission)

## Remote source
- "Remote" = Independently existing.
    > Relative to some point of reference.
- "Source" = Origin.
- "Remote source" = Origin that is independently existing.

> **NOTE**: Initially, I had defined "remote" as "distant and independently existing", but this definition was vague, because "distant" is, on its own, a vague term that implies separation along with some implied magnitude of separation that is "large enough". I think "separation", and specifically, the sense of separation as "independently existing", is what is relevant to consider when considering how to automate observation and transmission (see the next section). Now, what "independent existence" means for an entity is that the entity's operations are not fundamentally defined in relation to a specific system, even if its operations can contribute to this system in some way. For example, a thread of a specific process has no purpose apart from this process; it is fundamentally defined in relation to this process. A sensor, on the other hand, has a purpose of its own, apart from any specific system, and it can contribute to a range of systems without being defined by the system it contributes to.

# Telemetry
The process/practice of **automatically** doing the following: (1) making measurements/records from **remote sources** (the events/data/information to be measured/recorded originate from here) and (2) transmitting these measurements/records to a specified system (receiver/processor/storage/hybrid) - this specified system is the point of reference for observability (relative to which remote sources are "remote").

> **NOTE**: "Specified" => Predetermined by design, not accidentally/coincidentally.

Why consider both measurements and records? Because, in essence, telemetry is about **automatic observation followed by automatic transmission (of this observation)**, and both measurements and records count as kinds of observations that can be automated and automatically transmitted, i.e. captured, persisted, and shared independently of conscious experience.