# Preregistration: Comparative Fidelity of Information-Processing Lenses

*Pre-registered before any data collection. Timestamped by commit.*

## Study Information

**Title:** Comparative Fidelity of Eight Information-Processing Decompositions Applied to Public System Failures and ML Ablations

**Authors:** June Kim

**Date:** 2026-03-17

**Framework reference:** [The Natural Framework](https://june.kim/the-natural-framework)

**Formal specification:** [Lean 4 proof](https://github.com/kimjune01/natural-framework) (zero sorry)

---

## Background

Multiple frameworks decompose information-processing systems into named roles or stages. Each is a lens that compresses a system description into role labels. The question is which lens preserves the most information through the compression. That is fidelity.

A high-fidelity lens lets you reconstruct what a component does from its label alone. A low-fidelity lens loses the signal. The difference is measurable: map a component to a role, then try to recover the original from just the label. The lens that enables the best reconstruction is the highest-fidelity lens.

To control for granularity (more bins = more bits by construction), we compare lenses at matched bin counts: five lenses at 5-6 bins plus three at 6 bins. If the Natural Framework beats other 6-bin lenses, the advantage is the decomposition, not the granularity.

---

## Design

LLM-automated comparative fidelity study. Two frontier models (GPT-5.4 via Codex CLI, Claude Sonnet 4.5 via Claude Code) independently map the same data through eight lenses. Fidelity is measured by reconstruction accuracy: a separate LLM judge, blind to lens identity, scores how well each role label enables recovery of the original component description.

No human coders. LLMs as coders eliminates recruitment, fatigue, and training confounds. Model bias is constant across lenses within each model, and cross-model comparison detects it.

---

## Lenses Under Comparison

Eight lenses. Selection criteria: (1) claims to decompose information-processing systems generally, (2) has 5-7 named stages/roles, (3) comes from a distinct intellectual tradition. Lenses were selected from a [catalog of 80+ frameworks](https://github.com/kimjune01/natural-framework-fidelity/blob/master/lens_catalog.md) enumerated by GPT-5.4 before any data was collected.

### Viable System Model (5 bins) — cybernetics

| Role | Definition |
|------|-----------|
| **System 1 (Operations)** | Primary activities that produce the organization's output |
| **System 2 (Coordination)** | Manages interactions and conflicts between operational units |
| **System 3 (Control)** | Monitors and optimizes operations as a whole |
| **System 4 (Intelligence)** | Scans the environment, models the future, plans adaptation |
| **System 5 (Policy)** | Sets identity, values, and ultimate purpose |

Source: [Beer (1972)](https://en.wikipedia.org/wiki/Viable_system_model). Recursive: each viable system contains viable subsystems. Designed to diagnose organizational viability — close to what NF claims.

### MAPE-K (5 bins) — autonomic computing

| Role | Definition |
|------|-----------|
| **Monitor** | Collect data from the managed system and environment |
| **Analyze** | Correlate and model monitored data to detect issues |
| **Plan** | Decide on actions to achieve objectives |
| **Execute** | Carry out planned changes to the managed system |
| **Knowledge** | Shared persistent store that all other stages read and write |

Source: [IBM (2003)](https://en.wikipedia.org/wiki/Autonomic_computing). Self-managing systems. Knowledge is a persistent store similar to Remember/Consolidate.

### Intelligence Cycle (6 bins) — military/intelligence

| Role | Definition |
|------|-----------|
| **Direction** | Define information requirements |
| **Collection** | Gather raw data from sources |
| **Processing** | Convert raw data into usable format |
| **Analysis** | Evaluate processed data, produce assessments |
| **Dissemination** | Distribute finished intelligence to consumers |
| **Feedback** | Consumers inform the next cycle's direction |

Source: [Intelligence community](https://en.wikipedia.org/wiki/Intelligence_cycle). Six stages with a feedback loop.

### Shannon Communication Model (6 bins) — information theory

| Role | Definition |
|------|-----------|
| **Information Source** | Produces the message to be communicated |
| **Transmitter/Encoder** | Converts the message into a signal suitable for the channel |
| **Channel** | The medium through which the signal travels |
| **Noise** | Interference that corrupts the signal during transmission |
| **Receiver/Decoder** | Reconstructs the message from the received signal |
| **Destination** | The intended recipient of the message |

Source: [Shannon (1948)](https://en.wikipedia.org/wiki/Shannon%E2%80%93Weaver_model). Mathematically grounded. No feedback loop.

### Immune Response Schema (6 bins) — immunology

| Role | Definition |
|------|-----------|
| **Recognition** | Detect foreign antigen via pattern-matching receptors |
| **Activation** | Antigen-presenting cells trigger the adaptive response |
| **Clonal Expansion** | Selected lymphocytes proliferate rapidly |
| **Effector Response** | Activated cells neutralize or destroy the threat |
| **Contraction** | Excess effector cells are culled after the threat subsides |
| **Memory** | Surviving cells persist as long-lived memory for future encounters |

Source: [Immunology textbooks](https://en.wikipedia.org/wiki/Adaptive_immune_system). Biological pipeline with memory. NF's home turf — many of the framework's examples come from biology.

### F3EAD (6 bins) — military operations

| Role | Definition |
|------|-----------|
| **Find** | Identify targets through intelligence and surveillance |
| **Fix** | Locate and track the target precisely enough to act |
| **Finish** | Execute the operation against the target |
| **Exploit** | Gather intelligence from the operation's results |
| **Analyze** | Process exploited material to generate new understanding |
| **Disseminate** | Share findings to feed subsequent cycles |

Source: [US Special Operations](https://en.wikipedia.org/wiki/F3EAD). Six stages, operationally oriented, feedback via Disseminate→Find.

### CRISP-DM (6 bins) — data science

| Role | Definition |
|------|-----------|
| **Business Understanding** | Define objectives and requirements from a business perspective |
| **Data Understanding** | Collect initial data and identify quality issues |
| **Data Preparation** | Construct the final dataset from raw data |
| **Modeling** | Apply modeling techniques to prepared data |
| **Evaluation** | Assess the model against business objectives |
| **Deployment** | Put the model into production use |

Source: [CRISP-DM consortium (1999)](https://en.wikipedia.org/wiki/Cross-industry_standard_process_for_data_mining). Widely used workflow. Cycles back from Evaluation to Business Understanding.

### Natural Framework (6 bins) — formal specification

| Role | Definition |
|------|-----------|
| **Perceive** | Ingest external input, convert to internal representation |
| **Cache** | Temporarily hold data between processing steps |
| **Filter** | Gate, threshold, or reject data that fails a criterion |
| **Attend** | Rank, score, or select among items that passed filtering |
| **Remember** | Write processed data to persistent storage |
| **Consolidate** | Read past outcomes from storage, update system parameters |

Source: [The Natural Framework](https://june.kim/the-natural-framework). [Lean 4 proof](https://github.com/kimjune01/natural-framework). Six roles with a backward pass (Consolidate).

---

## Data Sources

All pre-specified and mechanically enumerable.

### Source 1: RL Framework Bug Issues

**Repos:** Top 5 RL frameworks by GitHub stars:

```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=5
```

**Issues:** Closed, labeled "bug", >= 10 comments, created 2022-2025.

### Source 2: Public Post-Mortems

**Source:** All entries on [github.com/danluu/post-mortems](https://github.com/danluu/post-mortems) at a snapshot commit hash (recorded in `DATA_SOURCES.md`).

**Exclusion:** Hardware-only root cause, natural disaster, physical security breach.

### Source 3: ML Ablation Tables

**Papers:** NeurIPS 2024 oral/spotlight papers containing "ablation" in the text.

**Rows:** Every row that removes or disables a named component.

---

## Procedure

### Phase 1: Data Enumeration

Run all queries mechanically. Commit raw results before any mapping begins.

### Phase 2: Mapping

For each data point (bug issue, post-mortem, or ablation row) and each lens:

**Mapper prompt** (used verbatim for each model × lens × data point):

> Here is a component or failure description from a real system:
>
> [component description inserted here]
>
> Here is a decomposition framework with [N] roles:
>
> [lens rubric inserted here — role names and definitions only]
>
> 1. Which role does this component best fit? Pick exactly one, or say "unmapped" if none fit.
> 2. In one sentence, what does a component with this role label typically do?

Each data point is mapped 3 times per model per lens (to measure consistency). Total per data point: 3 runs × 2 models × 8 lenses = 48 mapping calls.

### Phase 3: Reconstruction

For each mapping result, a separate judge model reconstructs:

**Reconstruction prompt** (used verbatim):

> A component in an information-processing system was labeled with the role "[ROLE_LABEL]" in a framework called "[LENS_NAME]".
>
> The framework defines this role as: "[ROLE_DEFINITION]"
>
> Based only on this label and definition, write one sentence describing what this component most likely does. Be as specific as possible.

### Phase 4: Judging

A judge model (blind to lens identity — lens name replaced with "Lens A", "Lens B", etc., randomized per data point) scores reconstruction fidelity:

**Judge prompt** (used verbatim):

> Here is what a system component actually does:
> [original description]
>
> Here is a reconstruction of what it does, based only on a role label from a decomposition framework:
> [reconstruction]
>
> How much of the original description is recoverable from the reconstruction?
>
> Score:
> - 3: The reconstruction identifies the component's specific function
> - 2: The reconstruction identifies the general category but not the specific function
> - 1: The reconstruction is too vague to distinguish this component from others
>
> Return: {"score": N, "rationale": "one sentence"}

Each judgment is run 3 times per judge model. Majority vote determines the score. Both GPT-5.4 and Sonnet 4.5 judge every reconstruction independently.

### Phase 5: Actionability (Sources 1 and 2 only)

For system failures, an additional reconstruction task:

**Repair prompt** (used verbatim):

> A system failure was categorized under the role "[ROLE_LABEL]" in a framework called "[LENS_NAME]".
>
> The framework defines this role as: "[ROLE_DEFINITION]"
>
> Based only on this label and definition, write one sentence suggesting how to fix the system.

Judge scores the repair against the actual resolution (from the bug report or post-mortem) on the same 1-3 scale.

---

## Hypotheses

### H1: Reconstruction Fidelity (primary)

The Natural Framework produces higher reconstruction scores than every other lens at the same bin count (6-bin lenses) and overall.

**Success criterion:** NF mean reconstruction score > every other 6-bin lens's mean score, p < 0.05 (Wilcoxon signed-rank, paired by data point). Separately: NF > every 5-bin lens.

**Failure criterion:** Any other 6-bin lens scores >= NF. The decomposition is not uniquely high-fidelity.

### H2: Mapping Consistency

The Natural Framework produces higher self-consistency (agreement across 3 runs of the same model) than other lenses.

**Metric:** Fleiss' kappa across 3 runs per model, computed per lens.

**Success criterion:** NF kappa > every other lens's kappa for both models.

**Failure criterion:** NF kappa < any other 6-bin lens. The role boundaries create inconsistency.

### H3: Diagnostic Actionability

For system failures, NF role labels produce repair suggestions that match actual resolutions better than other lenses.

**Success criterion:** NF mean actionability > every other lens, p < 0.05 (Wilcoxon signed-rank).

**Failure criterion:** No significant differences. The lens doesn't matter for diagnosis.

---

## Analysis Plan

### H1 Analysis

```
For each data point d and each lens L:
  fidelity(d, L) = median score across 3 mapper runs × 2 judge models

For each 6-bin lens pair (NF, other):
  W, p = wilcoxon_signed_rank(scores_NF, scores_other)

For each 5-bin lens pair (NF, other):
  W, p = wilcoxon_signed_rank(scores_NF, scores_other)

├─ NF > all seven others at p < 0.05 (Holm-Bonferroni corrected)?
│   └─ CONFIRMED: NF is highest-fidelity lens
├─ NF > all 6-bin lenses but not all 5-bin lenses?
│   └─ PARTIAL: NF wins at matched granularity; 5-bin lenses may
│      sacrifice resolution for coherence
├─ Another 6-bin lens >= NF?
│   └─ DISCONFIRMED: NF decomposition is not uniquely better
└─ Report full pairwise matrix with effect sizes
```

### H2 Analysis

```
For each lens L and each model M:
  kappa(L, M) = Fleiss' kappa across 3 mapping runs

├─ NF kappa highest for both models?
│   └─ CONFIRMED: NF roles are most consistently applied
├─ NF kappa highest for one model only?
│   └─ MODEL-DEPENDENT: consistency depends on the mapper
├─ Another 6-bin lens > NF for both models?
│   └─ DISCONFIRMED: NF boundaries are ambiguous
└─ Report per-role consistency to identify which NF boundaries are hardest
```

### H3 Analysis

```
For each failure f and each lens L:
  actionability(f, L) = median score across runs × judge models

For each lens pair (NF, other):
  W, p = wilcoxon_signed_rank(action_NF, action_other)

├─ NF > all others at p < 0.05?
│   └─ CONFIRMED: NF produces most actionable diagnoses
├─ No significant differences?
│   └─ DISCONFIRMED: lens doesn't help diagnosis
└─ Report all pairwise comparisons
```

### Multiple Comparisons

Three hypotheses × seven pairwise tests = 21 primary tests. Apply Holm-Bonferroni correction. Report both corrected and uncorrected p-values.

### Cross-Model Agreement

For each lens, compare GPT-5.4 and Sonnet mappings. If the two models disagree systematically by lens (one model favors NF, the other doesn't), the result is model-dependent. Report as a limitation.

### Exploratory Analyses (labeled as such)

- Which NF role boundaries cause the most inconsistency? (per-role kappa)
- Does fidelity vary by data source? (bug issues vs post-mortems vs ablations)
- Do any lenses cluster? (if Intelligence Cycle and NF always agree, they're the same lens in practice)
- Which lens produces the most "unmapped" cases?
- Role correspondence matrix: which roles across lenses map to the same components?

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (fidelity) | NF > Immune > Intelligence Cycle > F3EAD > Shannon > CRISP-DM > MAPE-K > VSM | NF distinguishes Cache/Remember and Filter/Attend. Immune Response is close because it has biological memory. Shannon has no feedback. VSM is organizational, not operational. |
| H2 (consistency) | Shannon > CRISP-DM > NF > Intelligence Cycle > Immune > F3EAD > MAPE-K > VSM | Shannon's roles are mathematically crisp. NF's Filter/Attend boundary will cause splits. VSM's "System 3 vs 4" is notoriously ambiguous. |
| H3 (actionability) | NF > MAPE-K > Intelligence Cycle > F3EAD > Immune > CRISP-DM > Shannon > VSM | "Filter is broken" tells you to fix the gate. "Channel has noise" tells you... to reduce noise? NF and MAPE-K roles map closest to implementation. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF wins H1 and H3, any bin count | NF is the best available lens. Ship it. |
| NF wins H1 among 6-bin only | NF is best at this granularity. The proof constrains the decomposition well. |
| Immune Response ties or beats NF on H1 | The biological decomposition is equally high-fidelity. NF may have been derived from biology all along. |
| Intelligence Cycle beats NF on H1 | A familiar vocabulary beats a novel one, even if the novel one is formally derived. |
| Shannon beats NF on H2 but loses H1 | Mathematical crispness aids consistency but not fidelity. Crisp categories can be consistently wrong. |
| MAPE-K beats NF on H3 | An engineering-native vocabulary is more actionable than a theory-native one. |
| All 6-bin lenses cluster together on H1 | The specific decomposition doesn't matter. Six bins with any reasonable definitions is enough. The proof is valid but the vocabulary is not load-bearing. |
| NF loses all three | The six roles are a formal specification with no practical advantage. |

---

## Budget

No human coders. Each trial is a CLI call.

Per data point: 48 mapping calls + 48 reconstruction calls + 48 judge calls + 16 repair calls (Sources 1 & 2) = ~160 calls.

Estimated dataset: ~100 data points. Total: ~16,000 CLI calls.

At ~10s per call: ~44 hours wall clock. Parallelizable across models and lenses.

---

## Stopping Rules

Fixed dataset. Map everything that qualifies. No adaptive stopping.

**Minimum viable dataset:**
- Source 1: At least 20 qualifying bug issues
- Source 2: At least 30 qualifying post-mortems
- Source 3: At least 50 ablation rows

---

## Logging

### Directory structure

```
data/
├── enumeration/          # Raw query results, committed before mapping
├── mappings/             # {datapoint}_{lens}_{model}_{run}.json
├── reconstructions/      # {datapoint}_{lens}_{model}_{run}_recon.json
├── judgments/            # {datapoint}_{lens}_{model}_{run}_judge{1,2,3}.json
├── repairs/              # {datapoint}_{lens}_{model}_{run}_repair.json
├── scores/               # Aggregated scores per data point per lens
└── analysis/             # Final results, posteriors, plots
```

### Per-mapping record (JSON)

```json
{
  "datapoint_id": "stable-baselines3_issue_1234",
  "source": "rl_bugs",
  "lens": "natural_framework",
  "model": "gpt-5.4",
  "run": 1,
  "role_assigned": "Filter",
  "mapper_description": "This component gates experiences by priority before they enter the replay buffer.",
  "unmapped": false,
  "timestamp": "2026-03-20T14:32:00Z"
}
```

### Commit policy

- Enumeration results: committed before any mapping begins.
- Mapping results: committed after each lens × model batch.
- Judgments: committed after each batch.
- No data is overwritten. Append-only.

---

## Transparency

- All prompts committed verbatim before Phase 2 begins.
- Lens anonymization key committed in a sealed file, opened only during Phase 5 analysis.
- All raw data published as JSON/CSV.
- This preregistration is timestamped by commit. No amendments after Phase 2 begins without a dated commit explaining the change.

---

*Preregistration designed collaboratively with Claude Opus 4.6 (via Claude Code) and GPT-5.4 (via Codex CLI, lens enumeration). Eight competing lenses selected from a catalog of 80+ frameworks to ensure fair comparison at matched granularity.*
