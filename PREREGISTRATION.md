# Preregistration: Physics and Chemistry of Information Processing

*Pre-registered before any data collection. Timestamped by commit.*

## Study Information

**Title:** Physics and Chemistry: Comparing Information-Theoretic and Behavioral Contracts for Software System Decomposition

**Authors:** June Kim

**Date:** 2026-03-17

**Framework reference:** [The Natural Framework](https://june.kim/the-natural-framework)

**Formal specification:** [Lean 4 proof](https://github.com/kimjune01/natural-framework) (zero sorry)

---

## Background

The Shannon Communication Model (1948) and the Natural Framework (NF, 2026) both decompose information-processing systems into six named roles with formal inter-stage contracts. They operate at different levels of description — like physics and chemistry examining the same substance.

Shannon is physics: mathematical theorems about rate, capacity, and distortion. Laws that hold regardless of implementation. You need instruments to measure them. NF is chemistry: behavioral pre/postconditions about data content at each handoff, proven in Lean 4. Observable from the artifacts. You can read them off the bench.

**Central question:** Do behavioral contracts help? NF's six roles can be stated with or without pre/postconditions. With contracts, you need the Lean proof. Without, you need TypeScript at most. This study compares three conditions — Shannon (physics-level contracts), NF with contracts (chemistry-level contracts), and NF without contracts (labels only) — to isolate whether contracts are load-bearing, and if so, which type.

---

## The Three Conditions

Full specifications in [`rubric.md`](rubric.md) and [`lenses/`](lenses/).

### Shannon Communication Model (1948)

| Role | Definition | Contract |
|------|-----------|---------|
| **Source** | Produces the message | Output has entropy H(X) |
| **Encoder** | Converts message into transmittable signal | Rate R ≥ H(X) for lossless; R < C for reliable transmission |
| **Channel** | Medium the signal travels through | Capacity C = max I(X;Y); defines the bottleneck |
| **Noise** | Interference corrupting the signal | Modeled by p(y\|x); decoder must know this |
| **Decoder** | Reconstructs message from received signal | Must share codebook with encoder; error → 0 as block length → ∞ |
| **Destination** | Intended recipient | Receives reconstructed message |

Contracts are **mathematical theorems** (source coding, channel capacity, rate-distortion, separation). [Full details →](lenses/shannon.md)

### Natural Framework (2026)

| Role | Definition | Precondition | Postcondition |
|------|-----------|-------------|--------------|
| **Perceive** | Ingest external input, convert to internal format | External data exists | Data is in internal format |
| **Cache** | Temporarily hold data between steps | Data is in internal format | Data is buffered, retrievable |
| **Filter** | Gate, threshold, or reject by criterion | Buffered data available | Failing items rejected; passing items remain |
| **Attend** | Rank, score, or select among items | Items have been filtered | Items ranked by priority |
| **Remember** | Write to persistent storage | Items ranked/selected | Data persisted, retrievable across cycles |
| **Consolidate** | Read past outcomes, update parameters | Store contains past outcomes | Parameters updated; next cycle reflects past |

Contracts are **behavioral pre/postconditions** proven in Lean 4. Each postcondition guarantees the next precondition. [Full details → The Natural Framework](https://june.kim/the-natural-framework)

### NF-bare (labels only)

| Role | Definition |
|------|-----------|
| **Perceive** | Ingest external input, convert to internal format |
| **Cache** | Temporarily hold data between steps |
| **Filter** | Gate, threshold, or reject by criterion |
| **Attend** | Rank, score, or select among items |
| **Remember** | Write to persistent storage |
| **Consolidate** | Read past outcomes, update parameters |

Same six roles and definitions as NF. **No pre/postconditions.** This is NF as TypeScript interfaces — the role vocabulary without the Lean proof.

### Controlled Comparison

| Dimension | Shannon | NF | NF-bare |
|-----------|---------|-----|---------|
| Role count | 6 | 6 | 6 |
| Role definitions | Communication-theoretic | Behavioral | Behavioral (same as NF) |
| Formal contracts | Mathematical theorems | Lean 4 pre/postconditions | **None** |
| Contract type | Continuous inequalities | Binary predicates | — |
| Stage ordering | No inherent order | Pipeline (handshake) | Pipeline (implied by definitions) |
| Origin domain | Communication engineering | Cognition / software | Cognition / software |

**Delta 1 (NF vs NF-bare):** Do contracts help? Same roles, same definitions, same ordering. The only difference is the pre/postconditions in the prompt. This is the sharp test. If contracts don't help, the Lean proof is ceremony.

**Delta 2 (NF vs Shannon):** Which level of description? Different roles, different contract type, different origin. This is context — physics vs chemistry with all confounds intact.

### Observability Skew

Shannon describes speeding. NF describes the speeding ticket. We're reading the filing cabinet.

An RL repo gives us procedures (functions, classes, data flow) but not the training data flowing through them. NF's contracts point at procedures: "does this function gate items by a criterion?" Shannon's contracts point at the data: "what is the entropy of the input stream?" You can answer the first by reading source code. You can't answer the second without running the system.

This skews every hypothesis toward chemistry. If NF wins, it may be because we brought a microscope to a problem that needs a microscope, not because chemistry is "better than" physics. Shannon might outperform on running systems with instrumentation — its natural habitat.

Shannon is the stronger formalism: mathematical impossibility results, universally taught, 78-year track record. This study tests whether its level of description is useful for a task where its quantities aren't directly measurable.

---

## Data Sources

All pre-specified and mechanically enumerable.

### Source 1: RL Framework Repos

**Repos:** Top 50 RL frameworks by GitHub stars:

```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=50
```

RL repos have explicit perceive/act/learn loops where all six NF roles are architecturally visible. Neither lens's home turf.

**Bug issues (Phases 2-5):** Closed, labeled "bug", >= 10 comments, created 2022-2025.

**Predictive validity (Phase 6):** Top 5 repos only (richest commit history).

**Health correlation (Phase 7):** All 50 repos. Health measured by [Libraries.io SourceRank](https://docs.libraries.io/overview.html#sourcerank).

### Source 2: Public Post-Mortems

**Source:** All entries on [github.com/danluu/post-mortems](https://github.com/danluu/post-mortems) at a snapshot commit hash (recorded in `DATA_SOURCES.md`). Exclude hardware-only root cause, natural disaster, physical security breach.

### Source 3: ML Ablation Tables

**Papers:** NeurIPS 2024 oral/spotlight papers containing "ablation" in the text.

**Rows:** Every row that removes or disables a named component.

---

## Procedure

### Phase 1: Data Enumeration

Run all queries mechanically. Commit raw results before any mapping begins.

### Phase 2: Mapping

For each data point and each condition:

**Mapper prompt** (used verbatim for each model × condition × data point):

> Here is a component or failure description from a real system:
>
> [component description inserted here]
>
> Here is a decomposition framework with 6 roles:
>
> [condition rubric inserted here — full specification for Shannon and NF; definitions only for NF-bare]
>
> 1. Which role does this component best fit? Pick exactly one, or say "unmapped" if none fit.
> 2. In one sentence, what does a component with this role label typically do?

3 runs × 2 models × 3 conditions = 18 mapping calls per data point.

### Phase 3: Reconstruction

**Reconstruction prompt** (used verbatim; for NF-bare, the contract line is omitted):

> A component in an information-processing system was labeled with the role "[ROLE_LABEL]" in a framework called "[LENS_NAME]".
>
> The framework defines this role as: "[ROLE_DEFINITION]"
>
> This role has the following contract: [CONTRACT_TEXT]
>
> Based only on this label, definition, and contract, write one sentence describing what this component most likely does. Be as specific as possible.

### Phase 4: Judging

Judge model scores reconstruction fidelity, blind to condition identity ("Lens A"/"Lens B"/"Lens C", randomized per data point):

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

3 runs per judge model, majority vote. GPT-5.4 and Sonnet 4.5 judge every reconstruction independently.

### Phase 5: Actionability (Sources 1 and 2 only)

**Repair prompt** (used verbatim; for NF-bare, contract lines are omitted):

> A system failure was categorized under the role "[ROLE_LABEL]" in a framework called "[LENS_NAME]".
>
> The framework defines this role as: "[ROLE_DEFINITION]"
>
> This role's contract is: [CONTRACT_TEXT]. The failure means this contract was violated.
>
> Based only on this label, definition, and contract violation, write one sentence suggesting how to fix the system.

Judge scores the repair against the actual resolution on the same 1-3 scale.

### Phase 6: Predictive Validity (top 5 Source 1 repos)

**Step 1: Snapshot.** Check out the earliest tagged release (or 25th-percentile commit). Record the hash.

**Step 2: Diagnose.**

**Diagnosis prompt** (used verbatim):

> Here is the source code of an information-processing system at an early stage of development:
>
> [source code inserted here]
>
> Here is a decomposition framework with 6 roles:
>
> [lens rubric inserted here]
>
> 1. For each role, is it present, partially implemented, or missing in this codebase?
> 2. For each missing or partial role, predict what will happen if development continues: what will be added, what bugs will arise, and in what order?
>
> Be specific. Name files, functions, or behaviors.

3 runs × 2 models × 3 conditions = 18 diagnosis calls per repo.

**Step 3: Check against history.**

**History judge prompt** (used verbatim):

> Here is a prediction made about a software project based on its early source code:
> [prediction]
>
> Here is what actually happened in the project's subsequent development:
> [summary of commits/issues from snapshot to HEAD]
>
> Score the prediction:
> - 3: The predicted change or bug actually occurred
> - 2: Something related occurred but not exactly as predicted
> - 1: The prediction did not materialize
>
> Return: {"score": N, "rationale": "one sentence"}

### Phase 7: Health Correlation (all 50 Source 1 repos)

Diagnose current codebase (HEAD) using all three conditions. Count roles rated present, partial, or missing. Correlate role completeness with SourceRank.

3 runs × 2 models × 3 conditions = 18 calls per repo. 50 repos = 900 calls.

---

## Hypotheses

Two planned contrasts, three secondary hypotheses. No multiple comparisons correction needed — the two contrasts test different questions (contracts vs vocabulary).

### Why Not Mapping Consistency?

Any well-defined 6-bin taxonomy produces consistent LLM mappings. Consistency measures how crisp the bins are, not how useful. We report per-condition Fleiss' kappa as an exploratory check, not as a hypothesis.

### H1: Do Contracts Help? (primary)

NF-with-contracts produces higher reconstruction scores than NF-bare.

**Test:** Wilcoxon signed-rank, paired by data point. Same roles, same definitions. The only difference is pre/postconditions in the prompt.

**Success:** NF > NF-bare, p < 0.05. Contracts are load-bearing. The Lean proof was necessary.

**Failure:** NF-bare >= NF. Contracts don't help. The six role labels carry the information; the pre/postconditions are ceremony. You don't need Lean for this.

### H2: Which Level of Description? (secondary)

NF-with-contracts produces higher reconstruction scores than Shannon.

**Test:** Wilcoxon signed-rank, paired by data point.

**Success:** NF > Shannon, p < 0.05. Chemistry beats physics at the lab bench.

**Failure:** Shannon >= NF. Physics-level descriptions are useful even without physics-level instruments.

### H3: Do Contracts Help Diagnosis?

NF-with-contracts produces repair suggestions that match actual resolutions better than NF-bare.

**Test:** Wilcoxon signed-rank, paired by failure.

**Success:** NF > NF-bare, p < 0.05. "Filter's postcondition violated" points to fixes better than "Filter" alone.

**Failure:** NF-bare >= NF. The role label is enough to diagnose; the contract adds nothing.

### H4: Do Contracts Help Prediction?

NF-with-contracts predicts subsequent development better than NF-bare.

**Test:** Wilcoxon signed-rank, paired by repo × prediction.

**Success:** NF > NF-bare, p < 0.05. The handshake predicts what gets built next.

**Failure:** NF-bare >= NF. The pipeline ordering implicit in the definitions is sufficient.

### H5: Health Correlation

NF role completeness correlates more strongly with project health (SourceRank) than Shannon role completeness and NF-bare role completeness.

**Test:** Steiger's test comparing three Spearman's rho values pairwise, n=50.

**Success:** NF rho > NF-bare rho > Shannon rho. Contracts help; NF vocabulary helps more than Shannon vocabulary.

**Failure:** All three rho values are similar. Role completeness tracks health regardless of framework.

**Power note:** n=50 detects rho difference >= 0.28 at p < 0.05.

---

## Analysis Plan

```
For each data point d and each condition C ∈ {NF, NF-bare, Shannon}:
  fidelity(d, C) = median score across 3 mapper runs × 2 judge models

Primary (contracts test):
  H1: W, p = wilcoxon_signed_rank(fidelity_NF, fidelity_NF_bare)
  H3: W, p = wilcoxon_signed_rank(action_NF, action_NF_bare)
  H4: W, p = wilcoxon_signed_rank(predict_NF, predict_NF_bare)

Secondary (level of description):
  H2: W, p = wilcoxon_signed_rank(fidelity_NF, fidelity_Shannon)
  H5: z, p = steiger_test(rho_NF, rho_NF_bare, rho_Shannon, n=50)

Report effect sizes and confidence intervals for all.
Exploratory: per-condition Fleiss' kappa, NF-bare vs Shannon comparisons.
```

### Cross-Model Agreement

Compare GPT-5.4 and Sonnet 4.5 mappings for each condition. If the models disagree on the contracts test (one says contracts help, the other says they don't), the result is model-dependent.

### Exploratory Analyses (labeled as such)

- Per-condition and per-role Fleiss' kappa (mapping consistency)
- Per-source breakdown: does any condition spike on a particular data source?
- NF-bare vs Shannon: are NF's labels alone better than Shannon's labels + theorems?
- Role correspondence: which NF roles map to which Shannon roles on the same components?
- Do the conditions agree on "unmapped" cases?
- Domain independence: Levene's test comparing cross-source variance across all three conditions

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (contracts: fidelity) | NF > NF-bare | "Filter's postcondition: failing items rejected" is more specific than "Filter: gate by criterion." The contract narrows the reconstruction. |
| H2 (level: fidelity) | NF > Shannon | NF distinguishes Cache/Remember and Filter/Attend behaviorally. Shannon collapses these. But this comparison has the observability skew. |
| H3 (contracts: actionability) | NF > NF-bare | "Postcondition violated" points to the fix. "This role broke" doesn't. |
| H4 (contracts: prediction) | NF > NF-bare | The handshake (each postcondition enables the next precondition) predicts ordering. Definitions alone imply ordering but don't guarantee it. |
| H5 (health) | NF > NF-bare > Shannon | Contracts help; NF vocabulary helps more than Shannon vocabulary. |

### What H1 Actually Tests

H1 is the clean test. NF and NF-bare have identical roles, identical definitions, identical pipeline ordering. The only difference is the pre/postconditions. If NF > NF-bare, the Lean proof is load-bearing — those specific contracts carry information that the role labels don't. If NF-bare >= NF, the proof is ceremony.

H2 (NF vs Shannon) has every confound: different roles, different contract types, discrete vs continuous, observability skew. H1 has none of these. H1 is the experiment; H2 is context.

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF > NF-bare on H1, H3, H4 | **Contracts are load-bearing.** The Lean proof was necessary. Pre/postconditions carry information that role labels don't. |
| NF = NF-bare, both > Shannon | **Vocabulary is load-bearing, contracts are not.** NF's six roles are better labels than Shannon's six roles. The Lean proof is ceremony, but the role decomposition is real. You need TypeScript, not Lean. |
| NF = NF-bare = Shannon | **Everything is interchangeable.** Any well-defined 6-role taxonomy works equally well. The decomposition matters; the specific roles and contracts don't. |
| NF > NF-bare, NF = Shannon | **Contracts help, but only to parity with Shannon.** Behavioral contracts bring NF up to Shannon's level. Without them, NF is worse. The Lean proof earns table stakes, not an advantage. |
| NF-bare > Shannon | **NF's vocabulary alone beats Shannon.** The role definitions (perceive/cache/filter/attend/remember/consolidate) are more useful than (source/encoder/channel/noise/decoder/destination) even without contracts. |
| Shannon > NF > NF-bare | **Physics beats chemistry beats labels.** Information-theoretic descriptions are most useful. Contracts help but not enough. |
| Shannon > NF = NF-bare | **Physics beats chemistry. Contracts don't help.** The Lean proof is a verbose restatement of Shannon in dependent types. |

---

## Budget

No human coders. Three conditions, ~7,000 CLI calls.

| Phase | Per unit | Units | Calls |
|-------|----------|-------|-------|
| Phases 2-5 | ~60 calls/data point | ~100 data points | ~6,000 |
| Phase 6 | ~36 calls/repo | 5 repos | ~180 |
| Phase 7 | ~18 calls/repo | 50 repos | ~900 |
| **Total** | | | **~7,100** |

At ~10s per call: ~20 hours wall clock. Parallelizable across models and conditions.

---

## Stopping Rules

Fixed dataset. Map everything that qualifies. No adaptive stopping.

**Minimum viable dataset:**
- Source 1 (bugs): >= 20 qualifying bug issues
- Source 1 (health): all 50 repos with valid SourceRank scores
- Source 2: >= 30 qualifying post-mortems
- Source 3: >= 50 ablation rows

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
├── diagnoses/            # {repo}_{lens}_{model}_{run}_diag.json
├── health/               # {repo}_sourcerank.json
├── scores/               # Aggregated scores per data point per lens
└── analysis/             # Final results, plots
```

### Per-mapping record (JSON)

```json
{
  "datapoint_id": "stable-baselines3_issue_1234",
  "source": "rl_bugs",
  "condition": "nf_with_contracts",
  "model": "gpt-5.4",
  "run": 1,
  "role_assigned": "Filter",
  "mapper_description": "This component gates experiences by priority before they enter the replay buffer.",
  "unmapped": false,
  "timestamp": "2026-03-20T14:32:00Z"
}
```

### Commit policy

- Enumeration: committed before any mapping begins.
- Mappings: committed after each condition × model batch.
- Judgments: committed after each batch.
- Append-only. No data is overwritten.

---

## Limitations

**Survivorship bias (H4).** Predictive validity uses the top 5 RL repos. H5 expands to 50 with natural health variance. Projects that died before gaining traction are absent.

**Tech-domain bias.** All three sources are software/ML. All three conditions claim broader generality.

**LLM bias.** Shannon appears far more in training data than NF. NF and NF-bare appear equally often (same role names). This biases H2 (NF vs Shannon) but not H1 (NF vs NF-bare).

**Observability skew (H2 only).** We have code (procedures), not data (streams). NF's contracts point at procedures; Shannon's point at data properties. This confounds H2 but not H1 — NF and NF-bare have the same observability.

**Discrete/continuous confound (H2 only).** NF's contracts are binary; Shannon's are continuous. This confounds H2 but not H1 — NF and NF-bare have the same contract type (binary, or none).

**Contract research asymmetry (H2 only).** The researcher designed NF's contracts and researched Shannon's from textbooks. Does not affect H1. Mitigation: all rubrics committed verbatim. [Shannon contract research →](lenses/shannon.md)

---

## Transparency

- All prompts committed verbatim before Phase 2.
- Lens anonymization key sealed until analysis.
- All raw data published as JSON/CSV.
- Timestamped by commit. No amendments after Phase 2 without a dated commit explaining the change.

---

*Three conditions: Shannon (physics), NF with contracts (chemistry), NF without contracts (labels only). The sharp test is NF vs NF-bare — do pre/postconditions help, or are the six role names enough? Shannon provides context: which level of description matches the instruments we have?*
