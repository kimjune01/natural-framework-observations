# Lens Rubrics

*Eight lenses. Each gets its full specification — role tables, definitions, decision criteria, and whatever inter-stage contracts the original source defines. Detailed contract research in [`lenses/`](lenses/).*

---

## Viable System Model (5 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Operations (S1)** | Primary activities that produce the system's output | Does it do the core work? |
| **Coordination (S2)** | Manages interactions and conflicts between units | Does it mediate between subsystems? |
| **Control (S3)** | Monitors and optimizes operations as a whole | Does it oversee and regulate? |
| **Intelligence (S4)** | Scans the environment, models the future | Does it look outward and plan? |
| **Policy (S5)** | Sets identity, values, and ultimate purpose | Does it define what the system is for? |

**Contracts:** Three axioms of management (variety equality constraints). Requisite variety (Ashby's law) at every transition. Channel capacity and transduction requirements. Algedonic emergency channel. Recursive self-similarity. [Full details →](lenses/vsm.md)

## MAPE-K (5 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Monitor** | Collect data from the managed system and environment | Does it observe the system's state? |
| **Analyze** | Correlate and model monitored data to detect issues | Does it interpret observations? |
| **Plan** | Decide on actions to achieve objectives | Does it choose what to do? |
| **Execute** | Carry out planned changes | Does it act on a decision? |
| **Knowledge** | Shared persistent store that all stages read and write | Does it persist data across cycles? |

**Contracts:** Intentionally under-specified. Implicit constraints only: Monitor outputs structured observations, Analyze outputs diagnoses, Plan outputs actionable strategies. No formal handoff schemas, no consistency model for Knowledge base. [Full details →](lenses/mape-k.md)

## Intelligence Cycle (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Direction** | Define information requirements | Does it specify what to look for? |
| **Collection** | Gather raw data from sources | Does it acquire raw data? |
| **Processing** | Convert raw data into usable format | Does it clean or normalize? |
| **Analysis** | Evaluate processed data, produce assessments | Does it draw conclusions? |
| **Dissemination** | Distribute results to consumers | Does it deliver output? |
| **Feedback** | Consumer responses inform next cycle | Does it feed outcomes back? |

**Contracts:** Principle-based. No collection without formal tasking (PIRs). Metadata/provenance preserved through pipeline. Analysis must address original PIRs with confidence levels and alternative hypotheses (ICD 203). [Full details →](lenses/intelligence-cycle.md)

## Shannon Communication Model (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Source** | Produces the message to be communicated | Does it originate the signal? |
| **Encoder** | Converts the message into a transmittable signal | Does it encode or compress? |
| **Channel** | Medium through which the signal travels | Is it the pathway, not the signal? |
| **Noise** | Interference that corrupts the signal | Does it degrade the signal? |
| **Decoder** | Reconstructs the message from the received signal | Does it decode or decompress? |
| **Destination** | Intended recipient of the message | Does it consume the final output? |

**Contracts:** Mathematical theorems. Source coding: L ≥ H(X). Channel capacity: R < C. Feasibility: H(Source) < C(Channel). Rate-distortion: R ≥ R(D). Encoder/decoder must share codebook and channel model. Separation theorem. [Full details →](lenses/shannon.md)

## Immune Response Schema (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Recognition** | Detect foreign antigen via pattern-matching | Does it detect something new? |
| **Activation** | Trigger the adaptive response | Does it initiate a response to detection? |
| **Clonal Expansion** | Selected responders proliferate | Does it amplify the response? |
| **Effector Response** | Neutralize or destroy the threat | Does it act on the threat? |
| **Contraction** | Excess responders culled after threat subsides | Does it wind down the response? |
| **Memory** | Surviving cells persist for future encounters | Does it persist for next time? |

**Contracts:** Molecularly precise. Two-signal model (TCR + co-stimulation required; Signal 1 alone → anergy). Cytokine thresholds for expansion. Cell count minimums for effector response. Antigen clearance triggers contraction. IL-7R/Bcl-2 expression determines memory fate. [Full details →](lenses/immune-response.md)

## F3EAD (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Find** | Identify targets through intelligence | Does it discover what to act on? |
| **Fix** | Locate and track precisely enough to act | Does it pin down the target? |
| **Finish** | Execute the operation | Does it carry out the action? |
| **Exploit** | Gather intelligence from results | Does it extract value from outcomes? |
| **Analyze** | Process exploited material for understanding | Does it make sense of what was gathered? |
| **Disseminate** | Share findings to feed subsequent cycles | Does it distribute what was learned? |

**Contracts:** Doctrine-based. Escalating confidence threshold (Find < Fix < Finish). PID must meet ROE standard before Finish. Chain of custody required for Exploit → Analyze. Finished intelligence (not raw data) required for Disseminate. [Full details →](lenses/f3ead.md)

## CRISP-DM (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Business Understanding** | Define objectives and requirements | Does it set the goal? |
| **Data Understanding** | Collect initial data, identify quality issues | Does it survey available data? |
| **Data Preparation** | Construct the dataset from raw data | Does it clean and shape data? |
| **Modeling** | Apply techniques to prepared data | Does it build the model? |
| **Evaluation** | Assess the model against objectives | Does it judge the result? |
| **Deployment** | Put the model into production | Does it ship the output? |

**Contracts:** Documented deliverables at each stage (informal). Evaluation gate: business success criteria, not just technical metrics. Mandatory iteration (process rule, not data contract). Under-specified on format requirements, approval authority, and iteration termination. [Full details →](lenses/crisp-dm.md)

## Natural Framework (6 bins)

The Natural Framework specifies pre/postconditions at each role transition. Each role's postcondition is the next role's precondition (the handshake).

| Role | Definition | Decision criterion | Precondition | Postcondition |
|------|-----------|-------------------|-------------|--------------|
| **Perceive** | Ingest external input, convert to internal format | Does it receive and transform external data? | External data exists | Data is in internal format |
| **Cache** | Temporarily hold data between processing steps | Does it buffer without transforming? | Data is in internal format | Data is held in a buffer, retrievable by next stage |
| **Filter** | Gate, threshold, or reject data by criterion | Does it remove items that fail a test? | Buffered data is available | Items failing the criterion are rejected; only passing items remain |
| **Attend** | Rank, score, or select among competing items | Does it order items by priority? | Items have been filtered | Remaining items are ranked by priority |
| **Remember** | Write processed data to persistent storage | Does it persist data durably? | Items are ranked/selected | Data is written to persistent storage, retrievable across cycles |
| **Consolidate** | Read past outcomes, update system parameters | Does it learn from stored results? | Persistent store contains past outcomes | System parameters are updated; next cycle reflects past results |

**Contracts:** Formal pre/postconditions at each transition. Each postcondition guarantees the next precondition. Proven in Lean 4 (zero sorry). Contracts are about data content at handoffs. [Full details → The Natural Framework](https://june.kim/the-natural-framework)

---

## General Rules (all lenses)

- **One role per component per lens.** If ambiguous, pick the primary function.
- **"Unmapped" is valid.** If no role fits, say so.
- **Use the function, not the name.** A module called "filter" might rank items.
- **Full specification per lens.** Each lens gets its role table, definitions, decision criteria, and whatever contracts its original source defines. No lens gets invented contracts; no lens is denied its own.

---

*Each lens gets its best shot. The competitive core requires it.*
