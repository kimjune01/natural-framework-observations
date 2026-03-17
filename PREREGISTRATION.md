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

**Central question:** Which level of description is more useful for diagnosing software systems from their static artifacts (source code, bug reports, ablation tables)? NF doesn't claim to replace Shannon any more than chemistry replaces physics. The question is which level matches the instruments we have.

---

## The Two Lenses

Both lenses have six roles, formal contracts, and domain-general aspirations. Full specifications in [`rubric.md`](rubric.md) and [`lenses/`](lenses/).

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

### Controlled Comparison

| Dimension | Shannon | NF | Same? |
|-----------|---------|-----|-------|
| Role count | 6 | 6 | **Yes** |
| Formal contracts | Yes (mathematical theorems) | Yes (Lean 4 pre/postconditions) | **Yes** |
| Domain-general claim | Yes | Yes | **Yes** |
| Proven | Yes (1948, information theory) | Yes (2026, dependent types) | **Yes** |
| Contract type | Continuous inequalities (R ≥ H(X)) | Binary predicates (postcondition holds or doesn't) | **No** |
| Contract content | Limits on rates and capacities | Data state at each handoff | **No** |
| Stage ordering | No inherent order (all co-instantiated) | Pipeline with explicit ordering (handshake) | **No** |
| Origin domain | Communication engineering | Cognition / software | **No** |

The four "No" rows are the independent variables. Everything this study measures is the effect of those differences.

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

For each data point and each lens:

**Mapper prompt** (used verbatim for each model × lens × data point):

> Here is a component or failure description from a real system:
>
> [component description inserted here]
>
> Here is a decomposition framework with 6 roles:
>
> [lens rubric inserted here — full specification including contracts]
>
> 1. Which role does this component best fit? Pick exactly one, or say "unmapped" if none fit.
> 2. In one sentence, what does a component with this role label typically do?

3 runs × 2 models × 2 lenses = 12 mapping calls per data point.

### Phase 3: Reconstruction

**Reconstruction prompt** (used verbatim):

> A component in an information-processing system was labeled with the role "[ROLE_LABEL]" in a framework called "[LENS_NAME]".
>
> The framework defines this role as: "[ROLE_DEFINITION]"
>
> This role has the following contract: [CONTRACT_TEXT]
>
> Based only on this label, definition, and contract, write one sentence describing what this component most likely does. Be as specific as possible.

### Phase 4: Judging

Judge model scores reconstruction fidelity, blind to lens identity ("Lens A"/"Lens B", randomized per data point):

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

3 runs per judge model, majority vote. Both GPT-5.4 and Sonnet 4.5 judge every reconstruction independently.

### Phase 5: Actionability (Sources 1 and 2 only)

**Repair prompt** (used verbatim):

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

3 runs × 2 models × 2 lenses = 12 diagnosis calls per repo.

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

Diagnose current codebase (HEAD) using both lenses. Count roles rated present, partial, or missing. Correlate role completeness with SourceRank.

3 runs × 2 models × 2 lenses = 12 calls per repo. 50 repos = 600 calls.

---

## Hypotheses

Five hypotheses, one comparison each. No multiple comparisons correction needed.

### Why Not Mapping Consistency?

Any well-defined 6-bin taxonomy produces consistent LLM mappings. Consistency measures how crisp the bins are, not how useful. Shannon's roles are defined by mathematical theorems — "channel or noise?" is unambiguous. That crispness could produce high consistency while preserving zero diagnostic information. We report per-lens Fleiss' kappa as an exploratory check, not as a hypothesis.

### H1: Reconstruction Fidelity (primary)

NF produces higher reconstruction scores than Shannon.

**Test:** Wilcoxon signed-rank, paired by data point.

**Success:** NF mean > Shannon, p < 0.05. Behavioral contracts describe real systems better than information-theoretic limits.

**Failure:** Shannon >= NF. Mathematical constraints are sufficient. The Lean proof adds nothing over 1948.

### H2: Diagnostic Actionability

NF role labels produce repair suggestions that match actual resolutions better than Shannon.

**Test:** Wilcoxon signed-rank, paired by failure.

**Success:** NF mean > Shannon, p < 0.05. "Filter's postcondition is violated" points to fixes better than "channel capacity exceeded."

**Failure:** Shannon >= NF. Information-theoretic diagnosis is equally or more actionable.

### H3: Predictive Validity

NF diagnoses of early-stage codebases predict subsequent development better than Shannon.

**Test:** Wilcoxon signed-rank, paired by repo × prediction.

**Success:** NF mean > Shannon, p < 0.05. The handshake (each postcondition enables the next precondition) predicts what gets built next.

**Failure:** Shannon >= NF. Rate/capacity constraints predict development equally well.

### H4: Health Correlation

NF role completeness correlates more strongly with project health (SourceRank) than Shannon role completeness.

**Test:** Steiger's test comparing Spearman's rho (NF vs SourceRank) against rho (Shannon vs SourceRank), n=50.

**Success:** NF rho > Shannon rho, p < 0.05.

**Failure:** Shannon rho >= NF. Information-theoretic completeness tracks health equally well.

**Power note:** n=50 detects rho difference >= 0.28 at p < 0.05.

### H5: Domain Independence

NF has lower variance in fidelity scores across the three data sources than Shannon. Both lenses claim generality. The more domain-independent lens performs uniformly; the more domain-specific one spikes on its home turf.

**Test:** Levene's test comparing cross-source variance of NF fidelity vs Shannon fidelity.

**Success:** NF variance < Shannon variance. NF captures cross-domain structure that Shannon misses.

**Failure:** Shannon variance <= NF. Information theory is already domain-independent.

---

## Analysis Plan

```
For each data point d and each lens L ∈ {NF, Shannon}:
  fidelity(d, L) = median score across 3 mapper runs × 2 judge models

H1: W, p = wilcoxon_signed_rank(fidelity_NF, fidelity_Shannon)
H2: W, p = wilcoxon_signed_rank(action_NF, action_Shannon)
H3: W, p = wilcoxon_signed_rank(predict_NF, predict_Shannon)
H4: z, p = steiger_test(rho_NF, rho_Shannon, n=50)
H5: F, p = levene_test(fidelity_NF by source, fidelity_Shannon by source)

Five tests. Report effect sizes and confidence intervals for all.
Exploratory: per-lens Fleiss' kappa (mapping consistency check, not a hypothesis).
```

### Cross-Model Agreement

Compare GPT-5.4 and Sonnet 4.5 mappings for each lens. If the models disagree (one favors NF, the other Shannon), the result is model-dependent.

### Exploratory Analyses (labeled as such)

- Per-lens and per-role Fleiss' kappa (mapping consistency)
- Per-source breakdown: does either lens spike on a particular data source?
- Role correspondence: which NF roles map to which Shannon roles on the same components?
- Do the lenses agree on "unmapped" cases?

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (fidelity) | NF > Shannon | NF distinguishes Cache/Remember and Filter/Attend behaviorally. Shannon collapses these: Cache and Channel overlap; Filter has no analog. |
| H2 (actionability) | NF > Shannon | "Filter's postcondition violated" points to a fix. "Channel capacity exceeded" names the limit, not the repair. |
| H3 (prediction) | NF > Shannon | NF's handshake predicts ordering: earlier stages get built first. Shannon has no ordering prediction. |
| H4 (health) | NF > Shannon | NF roles map to implementation concerns (buffering, filtering, persistence). Shannon roles map to communication architecture. Implementation gaps predict health more specifically. |
| H5 (domain independence) | NF flatter | Shannon spikes on signal-processing-adjacent failures, drops on organizational post-mortems. NF's data-content contracts should perform uniformly. |

### Discrete vs. Continuous Asymmetry

NF's contracts are binary predicates: a postcondition holds or it doesn't. Shannon's contracts are inequalities over continuous quantities (R ≥ H(X), R < C). This asymmetry cuts differently per source:

- **Sources 1-2 (bugs, post-mortems):** Something broke. "Which step failed?" (discrete) is a natural fit. Prediction: favors NF on H1 and H2 for these sources.
- **Source 3 (ablation tables):** Performance degraded by a measured amount. "How far from the limit?" (continuous) is a natural fit. Prediction: Shannon may close the gap on H1 for ablation data.

If NF wins on Sources 1-2 but Shannon wins on Source 3, the discrete/continuous distinction — not contract quality — may explain the difference.

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF wins all five | Chemistry is more useful at the lab bench. Behavioral contracts are load-bearing for static analysis. |
| NF wins H1, H2; loses H3 | Chemistry describes and diagnoses, but doesn't predict. The handshake is a verification tool, not a crystal ball. |
| Shannon wins H1 | **The surprising result.** Physics-level descriptions are useful even without physics-level instruments. The LLM is inferring information-theoretic structure from code. |
| Tie on H1, NF wins H3 | Same descriptive power, but the handshake predicts. The ordering theory is load-bearing; the role names are not. |
| Tie on everything | Physics and chemistry describe the same structure. Two levels of description, same substance. NF's contribution is making the chemistry explicit. |
| Shannon wins H5 | Physics is already domain-independent. The universal substrate was described in 1948. |
| NF loses all five | Chemistry adds nothing over physics, even on chemistry's home turf. The Lean proof is a verbose restatement of Shannon. |

---

## Budget

No human coders. Two lenses, ~4,700 CLI calls.

| Phase | Per unit | Units | Calls |
|-------|----------|-------|-------|
| Phases 2-5 | ~40 calls/data point | ~100 data points | ~4,000 |
| Phase 6 | ~24 calls/repo | 5 repos | ~120 |
| Phase 7 | ~12 calls/repo | 50 repos | ~600 |
| **Total** | | | **~4,700** |

At ~10s per call: ~13 hours wall clock. Parallelizable across models.

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

- Enumeration: committed before any mapping begins.
- Mappings: committed after each lens × model batch.
- Judgments: committed after each batch.
- Append-only. No data is overwritten.

---

## Limitations

**Survivorship bias (H3, partially addressed by H4).** H3 uses the top 5 RL repos. H4 expands to 50 with natural health variance. Projects that died before gaining traction are absent.

**Tech-domain bias.** All three sources are software/ML. Both frameworks claim broader generality.

**LLM bias.** Shannon appears far more in training data than NF. If fluency helps, Shannon has an unfair advantage. Cross-model comparison partially controls for this.

**Contract research asymmetry.** The researcher designed NF's contracts and researched Shannon's from textbooks. Mitigation: both rubrics are committed verbatim and auditable. [Shannon contract research →](lenses/shannon.md)

**Discrete/continuous confound.** NF's contracts are binary (postcondition holds or doesn't); Shannon's are continuous (inequalities over rates and capacities). Source 3 (ablation tables) reports continuous degradation, which may favor Shannon's contract type independent of framework quality. Per-source breakdown in exploratory analyses will test this.

**Observability skew.** We have code (procedures), not data (streams). NF's contracts point at procedures; Shannon's point at data properties. An NF win may reflect this asymmetry rather than intrinsic framework quality. A fair Shannon test would use running systems with measurable information-theoretic quantities.

---

## Transparency

- All prompts committed verbatim before Phase 2.
- Lens anonymization key sealed until analysis.
- All raw data published as JSON/CSV.
- Timestamped by commit. No amendments after Phase 2 without a dated commit explaining the change.

---

*Shannon (1948) is the physics of information processing: laws about rate, capacity, and distortion. NF (2026) is the chemistry: what happens to the data at each step. Both have six roles and formal proofs. This study asks which level of description is more useful for diagnosing software systems from their artifacts.*
