# Coding Rubric

*For use by independent coders. Do not read the Natural Framework post or the Lean proof before coding.*

---

## Role Mapping (Sources 1, 2, 3)

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

### Decision rules

- **One role per component.** If a component seems to serve two roles, choose the primary one — the role it would break if removed.
- **"I don't know" is valid.** If none of the six questions clearly applies, code as `unmapped` and note why.
- **Use the component's function, not its name.** A module called "filter" might actually rank items (Attend). A module called "memory" might just buffer (Cache). Read what it does.

### Role definitions (one sentence each)

| Role | Definition |
|------|-----------|
| **Perceive** | Ingests external input and converts it to the system's internal representation. |
| **Cache** | Temporarily holds data between processing steps without transforming it. |
| **Filter** | Removes, gates, or rejects data that fails a criterion — reduces quantity. |
| **Attend** | Ranks, scores, or selects among items that passed filtering — determines priority. |
| **Remember** | Writes processed data to persistent storage for later retrieval. |
| **Consolidate** | Reads past outcomes from storage and updates system parameters or policies. |

### Boundary cases

These are the hardest distinctions. When in doubt, apply the decision tree strictly (first "yes" wins).

| Boundary | Distinguishing question |
|----------|----------------------|
| Cache vs. Remember | Is the data lost when the process restarts? Cache = yes (temporary). Remember = no (persistent). |
| Filter vs. Attend | Does it reject outright, or does it rank? Filter = binary pass/fail. Attend = relative ordering. |
| Attend vs. Consolidate | Does it select from current data, or update behavior based on past data? Attend = current cycle. Consolidate = cross-cycle learning. |
| Perceive vs. Cache | Does it transform format (parsing, tokenizing), or hold data unchanged? Perceive = format change. Cache = same format, held in buffer. |

---

## Death Condition Mapping (Sources 1, 2)

For each documented failure, ask these questions in order. Take the **first** "yes."

```
1. Did a specific component break, crash, produce wrong output,
   or get removed?
   YES → Broken step
   Note which role the broken component serves (use role mapping above).

2. Did the system stop receiving new input, or start processing
   its own output as if it were new input?
   YES → Closed loop

3. Did the input source degrade over time — distribution shift,
   data corruption, stale references, sensor drift?
   YES → Decaying input
```

### Decision rules

- **One death condition per failure.** If multiple things went wrong, code the root cause — the first thing that broke.
- **"Doesn't map" is valid.** If the failure doesn't fit any of the three, code as `unmapped` and note why. These cases are data — they test H1.
- **The failure must be described in the source.** Don't infer failure mechanisms that aren't documented. If the post-mortem says "it broke" but not how, code as `insufficient_detail`.

### Death condition definitions

| Condition | Definition | Example |
|-----------|-----------|---------|
| **Broken step** | A role's implementation failed — removed, crashed, wrong output, misconfigured. | A parser threw an exception on valid input; a logging service went down; a scoring function returned NaN. |
| **Closed loop** | The system stopped receiving fresh external input, or recycled its own output as input. | A crawler that stops fetching new pages and reprocesses cached ones; a feedback loop with no new observations. |
| **Decaying input** | The input source degraded gradually — data quality declined, distribution shifted, references went stale. | A recommendation model trained on 2020 data deployed in 2024; a sensor losing calibration over months. |

---

## Ablation Coding (Source 3)

For each ablation row:

1. **Component name:** The name of the component removed (as stated in the paper).
2. **Component description:** What the paper says it does (one sentence, verbatim or close paraphrase).
3. **Role mapping:** Apply the role decision tree above.
4. **Performance metric:** The paper's reported metric (name and value).
5. **Baseline value:** The full model's performance on that metric.
6. **Ablated value:** The performance with this component removed.
7. **Performance drop:** Baseline minus ablated (positive = removing it hurts).

### Normalization

To compare across papers with different metrics:

```
normalized_drop = (baseline - ablated) / baseline
```

If higher metric = better (accuracy, F1): use formula as-is.
If lower metric = better (loss, error rate): negate the result.

---

## Training Examples

Practice on these before coding real data. Discuss disagreements, clarify the rubric if needed.

### Example 1: Bug Report

> "The replay buffer fills up and stops accepting new transitions. The agent
> keeps training on the same old experiences. Performance plateaus."

- **Death condition:** Closed loop (the system processes recycled data instead of new input)
- **Broken component role:** Cache (the replay buffer holds transitions temporarily for sampling)

### Example 2: Ablation Row

> "We remove the attention pooling layer. Instead of computing weighted
> combinations of features, we use mean pooling."

- **Component:** Attention pooling layer
- **Role:** Attend (it scores and weights features — selecting by priority)
- **Performance drop:** Whatever the table says

### Example 3: Post-Mortem

> "The load balancer stopped sending health checks. Unhealthy backends stayed
> in rotation and served errors for 45 minutes."

- **Death condition:** Broken step (the health-check mechanism — a Filter — stopped working)
- **Broken component role:** Filter (it gates backends by health status, rejecting unhealthy ones)

### Example 4: Ambiguous Case

> "Performance gradually degraded as user behavior shifted after a UI redesign."

- **Death condition:** Decaying input (the data distribution shifted — the model's training data no longer matches reality)

### Example 5: Substrate Destruction (Broken Step on Remember)

> "The outage was caused by a network partition between data centers."

- **Death condition:** Broken step. Remember is the persistent store, and the store IS the substrate. A network partition destroyed the system's ability to write to and read from its store. That's Remember failing — not an unmapped external event.
- **Broken component role:** Remember (the partition severed access to the persistent store)

### Example 6: Substrate Destruction (Catastrophic)

> "The company was shut down after losing a government antitrust lawsuit.
> All operations ceased within 90 days."

- **Death condition:** Broken step. The company's substrate — legal standing, bank accounts, contracts — is what Remember writes to. The lawsuit destroyed the store. The pipeline was functional; the thing it persisted to no longer existed.
- **Broken component role:** Remember (the substrate was removed)
- **Note:** If the root cause was the company's own illegal behavior, trace further: the compliance process (Filter) broke first, which eventually caused the substrate to be destroyed. Code the root cause per the rubric: broken step on Filter.

---

*Rubric finalized before coder training. Clarifications from training will be appended below this line, committed before real coding begins.*
