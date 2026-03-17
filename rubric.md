# Coding Rubrics

*Four lenses, equivalent detail. Each lens gets a decision tree, boundary cases, and training examples.*

---

## Lens 1: IPO (Input, Process, Output)

For each component, ask these questions in order. Take the **first** "yes."

```
1. Does it receive data from outside the system?
   YES → Input

2. Does it produce data that leaves the system?
   YES → Output

3. Everything else?
   → Process
```

### Boundary cases

| Boundary | Rule |
|----------|------|
| Input vs Process | If it receives AND transforms, code as Input (entry point takes priority) |
| Process vs Output | If it transforms AND emits, code as Output (exit point takes priority) |

---

## Lens 2: OODA (Observe, Orient, Decide, Act)

For each component, ask these questions in order. Take the **first** "yes."

```
1. Does it gather information from the environment?
   YES → Observe

2. Does it analyze, synthesize, or build a model from observations?
   YES → Orient

3. Does it choose between alternatives or select a course of action?
   YES → Decide

4. Does it execute a decision or produce an effect?
   YES → Act
```

### Boundary cases

| Boundary | Distinguishing question |
|----------|----------------------|
| Observe vs Orient | Does it just receive, or does it also interpret? Observe = raw intake. Orient = interpretation. |
| Orient vs Decide | Does it analyze to understand, or analyze to choose? Orient = building the picture. Decide = picking from the picture. |
| Decide vs Act | Does it select, or does it execute? Decide = choosing what to do. Act = doing it. |

---

## Lens 3: Intelligence Cycle (Direction, Collection, Processing, Analysis, Dissemination, Feedback)

For each component, ask these questions in order. Take the **first** "yes."

```
1. Does it define what information the system needs?
   YES → Direction

2. Does it gather raw data from sources?
   YES → Collection

3. Does it convert raw data into a usable format (parse, clean,
   normalize)?
   YES → Processing

4. Does it evaluate processed data and produce assessments or
   conclusions?
   YES → Analysis

5. Does it distribute results to downstream consumers?
   YES → Dissemination

6. Does it use consumer responses to inform the next cycle's
   requirements?
   YES → Feedback
```

### Boundary cases

| Boundary | Distinguishing question |
|----------|----------------------|
| Collection vs Processing | Does it gather from a source, or transform what's already gathered? Collection = acquisition. Processing = format conversion. |
| Processing vs Analysis | Does it clean/normalize, or does it evaluate/conclude? Processing = format. Analysis = meaning. |
| Dissemination vs Feedback | Does it send results out, or does it receive reactions back? Dissemination = push. Feedback = pull. |
| Direction vs Feedback | Direction defines requirements at the start. Feedback refines requirements based on outcomes. First cycle = Direction. Subsequent adjustment = Feedback. |

---

## Lens 4: Natural Framework (Perceive, Cache, Filter, Attend, Remember, Consolidate)

For each component, ask these questions in order. Take the **first** "yes."

```
1. Does it receive external input and convert it into the system's
   internal format?
   YES → Perceive

2. Does it buffer, batch, or temporarily hold data for downstream
   processing?
   YES → Cache

3. Does it gate, threshold, reject, or discard data based on a
   criterion?
   YES → Filter

4. Does it rank, score, weight, or select among competing items?
   YES → Attend

5. Does it write data to persistent storage (disk, database, log)?
   YES → Remember

6. Does it read past outcomes and update the system's parameters,
   weights, or policies based on what worked?
   YES → Consolidate
```

### Boundary cases

| Boundary | Distinguishing question |
|----------|----------------------|
| Cache vs Remember | Is the data lost when the process restarts? Cache = yes (temporary). Remember = no (persistent). |
| Filter vs Attend | Does it reject outright, or does it rank? Filter = binary pass/fail. Attend = relative ordering. |
| Attend vs Consolidate | Does it select from current data, or update behavior based on past data? Attend = current cycle. Consolidate = cross-cycle learning. |
| Perceive vs Cache | Does it transform format (parsing, tokenizing), or hold data unchanged? Perceive = format change. Cache = same format, held in buffer. |

---

## Death Condition Mapping (Sources 1 and 2 only)

For system failures, also code a death condition. This is specific to the Natural Framework lens but applied to all data points from Sources 1 and 2.

```
1. Did a specific component break, crash, produce wrong output,
   or get removed?
   YES → Broken step

2. Did the system stop receiving new input, or start processing
   its own output as if it were new input?
   YES → Closed loop

3. Did the input source degrade over time — distribution shift,
   data corruption, stale references, sensor drift?
   YES → Decaying input
```

**One death condition per failure.** Code the root cause.

**"Doesn't map" is valid.** Code as `unmapped` and note why.

---

## General Rules (all lenses)

- **One role per component per lens.** If a component seems to serve two roles, choose the primary one.
- **"Unmapped" is valid.** If none of the roles clearly apply, code as `unmapped` and note why.
- **Use the component's function, not its name.** A module called "filter" might rank items. Read what it does.
- **Apply the decision tree strictly.** First "yes" wins. Don't deliberate between roles.

---

## Training Examples

Practice on these before coding real data. Map each example under **all four lenses**.

### Example 1: Replay Buffer (RL Bug)

> "The replay buffer fills up and stops accepting new transitions. The agent keeps training on the same old experiences."

| Lens | Role | Rationale |
|------|------|-----------|
| IPO | Process | It's internal to the system, neither input nor output |
| OODA | Orient | It organizes observations for the agent to learn from |
| Intelligence Cycle | Processing | It stores and formats raw experience for analysis |
| Natural Framework | Cache | It temporarily holds transitions for sampling |

### Example 2: Attention Pooling (Ablation)

> "We remove the attention pooling layer. Instead of computing weighted combinations of features, we use mean pooling."

| Lens | Role | Rationale |
|------|------|-----------|
| IPO | Process | It transforms data internally |
| OODA | Orient | It synthesizes features into a representation |
| Intelligence Cycle | Analysis | It evaluates and weights features |
| Natural Framework | Attend | It scores and weights features by relevance |

### Example 3: Health Check (Post-Mortem)

> "The load balancer stopped sending health checks. Unhealthy backends stayed in rotation and served errors."

| Lens | Role | Rationale |
|------|------|-----------|
| IPO | Process | It's internal monitoring |
| OODA | Observe | It gathers information about backend status |
| Intelligence Cycle | Collection | It gathers operational data from backends |
| Natural Framework | Filter | It gates backends by health status, rejecting unhealthy ones |

### Example 4: Distribution Shift

> "Performance gradually degraded as user behavior shifted after a UI redesign."

| Lens | Role | Rationale |
|------|------|-----------|
| IPO | Input | The input data changed character |
| OODA | Observe | The observations no longer match the model |
| Intelligence Cycle | Collection | The collected data's nature shifted |
| Natural Framework | Perceive | The input distribution changed, degrading encoding quality |

### Example 5: Network Partition (Post-Mortem)

> "The outage was caused by a network partition between data centers."

| Lens | Role | Rationale |
|------|------|-----------|
| IPO | Process | Infrastructure failure affecting processing |
| OODA | Act | The system could not execute its decisions |
| Intelligence Cycle | Dissemination | Results could not reach consumers |
| Natural Framework | Remember | The partition severed access to the persistent store |

Note: this case may be coded `unmapped` under some lenses if the coder judges that infrastructure failure is outside the lens's scope. That's valid data.

### Example 6: Government Shutdown

> "The company was shut down after losing a government antitrust lawsuit. All operations ceased."

| Lens | Role | Rationale |
|------|------|-----------|
| IPO | `unmapped` | External force, not an IPO stage failure |
| OODA | `unmapped` | External force, not a loop stage failure |
| Intelligence Cycle | `unmapped` | External force, not a cycle stage failure |
| Natural Framework | `unmapped` | Substrate destruction — a precondition failure, not a role failure. If root cause was the company's own illegal behavior, trace further: Filter (compliance) broke. |

---

*Rubrics finalized before coder training. Clarifications from training will be appended below this line, committed before real coding begins.*
