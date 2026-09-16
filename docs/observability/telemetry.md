[<< **Observability**](https://pranigopu.github.io/observability)

<h1>Telemetry</h1>

---

**Contents**:

- [Measurement](#measurement)
- [Record](#record)
- [Remote Source](#remote-source)
- [Telemetry](#telemetry)

---

# Measurement
Measurement is the act or product of relating a known/fixed instance of an attribute to an unknown/variable instance of the same. For example, we can know/fix a "second" by marking it perceptually or via a pendulum swing, and through relating N seconds to a span of events that occur, we can measure the time it took for the events to occur. As another example, we can know/fix a meter perceptually, maybe by reference to a "standard" object or marking, and through relating N meters to a one-dimensional span of space, we can measure the length of this space.

# Record
Persistent encapsulation of an event/set of events, e.g.:

- Notes
- Logs
- Marks
- Measurements (yes, measurements can be a kind for record)

# Remote Source
- "Remote" = Distant and independently existing.
    > Relative to some point of reference.
- "Source" = Origin.
- "Remote source" = Origin that is distant and independently existing.

# Telemetry
The process/practice of **automatically** doing the following: (1) making measurements/records from **remote sources** (the events/data/information to be measured/recorded originate from here) and (2) transmitting these measurements/records to a specified system (receiver/processor/storage/hybrid) - this specified system is the point of reference for observability (relative to which remote sources are "remote").

> **NOTE**: "Specified" => Predetermined by design, not accidentally/coincidentally.

Why consider both measurements and records? Because, in essence, telemetry is about **automatic observation**, and both measurements and records count as kinds of observations that can be automated, i.e. captured and persisted independently of conscious experience.