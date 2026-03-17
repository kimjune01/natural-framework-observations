# Lens Rubrics

*Eight lenses. Each gets a role table with definitions and decision criteria. These are inserted into LLM prompts verbatim.*

---

## Viable System Model (5 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Operations (S1)** | Primary activities that produce the system's output | Does it do the core work? |
| **Coordination (S2)** | Manages interactions and conflicts between units | Does it mediate between subsystems? |
| **Control (S3)** | Monitors and optimizes operations as a whole | Does it oversee and regulate? |
| **Intelligence (S4)** | Scans the environment, models the future | Does it look outward and plan? |
| **Policy (S5)** | Sets identity, values, and ultimate purpose | Does it define what the system is for? |

## MAPE-K (5 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Monitor** | Collect data from the managed system and environment | Does it observe the system's state? |
| **Analyze** | Correlate and model monitored data to detect issues | Does it interpret observations? |
| **Plan** | Decide on actions to achieve objectives | Does it choose what to do? |
| **Execute** | Carry out planned changes | Does it act on a decision? |
| **Knowledge** | Shared persistent store that all stages read and write | Does it persist data across cycles? |

## Intelligence Cycle (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Direction** | Define information requirements | Does it specify what to look for? |
| **Collection** | Gather raw data from sources | Does it acquire raw data? |
| **Processing** | Convert raw data into usable format | Does it clean or normalize? |
| **Analysis** | Evaluate processed data, produce assessments | Does it draw conclusions? |
| **Dissemination** | Distribute results to consumers | Does it deliver output? |
| **Feedback** | Consumer responses inform next cycle | Does it feed outcomes back? |

## Shannon Communication Model (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Source** | Produces the message to be communicated | Does it originate the signal? |
| **Encoder** | Converts the message into a transmittable signal | Does it encode or compress? |
| **Channel** | Medium through which the signal travels | Is it the pathway, not the signal? |
| **Noise** | Interference that corrupts the signal | Does it degrade the signal? |
| **Decoder** | Reconstructs the message from the received signal | Does it decode or decompress? |
| **Destination** | Intended recipient of the message | Does it consume the final output? |

## Immune Response Schema (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Recognition** | Detect foreign antigen via pattern-matching | Does it detect something new? |
| **Activation** | Trigger the adaptive response | Does it initiate a response to detection? |
| **Clonal Expansion** | Selected responders proliferate | Does it amplify the response? |
| **Effector Response** | Neutralize or destroy the threat | Does it act on the threat? |
| **Contraction** | Excess responders culled after threat subsides | Does it wind down the response? |
| **Memory** | Surviving cells persist for future encounters | Does it persist for next time? |

## F3EAD (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Find** | Identify targets through intelligence | Does it discover what to act on? |
| **Fix** | Locate and track precisely enough to act | Does it pin down the target? |
| **Finish** | Execute the operation | Does it carry out the action? |
| **Exploit** | Gather intelligence from results | Does it extract value from outcomes? |
| **Analyze** | Process exploited material for understanding | Does it make sense of what was gathered? |
| **Disseminate** | Share findings to feed subsequent cycles | Does it distribute what was learned? |

## CRISP-DM (6 bins)

| Role | Definition | Decision criterion |
|------|-----------|-------------------|
| **Business Understanding** | Define objectives and requirements | Does it set the goal? |
| **Data Understanding** | Collect initial data, identify quality issues | Does it survey available data? |
| **Data Preparation** | Construct the dataset from raw data | Does it clean and shape data? |
| **Modeling** | Apply techniques to prepared data | Does it build the model? |
| **Evaluation** | Assess the model against objectives | Does it judge the result? |
| **Deployment** | Put the model into production | Does it ship the output? |

## Natural Framework (6 bins)

Unlike the other lenses, the Natural Framework specifies pre/postconditions at each role transition. Each role's postcondition is the next role's precondition. This is the handshake — the formal contract that other lenses do not provide.

| Role | Definition | Decision criterion | Precondition | Postcondition |
|------|-----------|-------------------|-------------|--------------|
| **Perceive** | Ingest external input, convert to internal format | Does it receive and transform external data? | External data exists | Data is in internal format |
| **Cache** | Temporarily hold data between processing steps | Does it buffer without transforming? | Data is in internal format | Data is held in a buffer, retrievable by next stage |
| **Filter** | Gate, threshold, or reject data by criterion | Does it remove items that fail a test? | Buffered data is available | Items failing the criterion are rejected; only passing items remain |
| **Attend** | Rank, score, or select among competing items | Does it order items by priority? | Items have been filtered | Remaining items are ranked by priority |
| **Remember** | Write processed data to persistent storage | Does it persist data durably? | Items are ranked/selected | Data is written to persistent storage, retrievable across cycles |
| **Consolidate** | Read past outcomes, update system parameters | Does it learn from stored results? | Persistent store contains past outcomes | System parameters are updated; next cycle reflects past results |

---

## General Rules (all lenses)

- **One role per component per lens.** If ambiguous, pick the primary function.
- **"Unmapped" is valid.** If no role fits, say so.
- **Use the function, not the name.** A module called "filter" might rank items.

---

*Rubrics used verbatim in LLM prompts. Each lens gets its full specification: role table, definitions, and decision criteria. The Natural Framework additionally gets pre/postconditions because it specifies them. Other lenses do not get invented contracts — they get exactly what they define.*
