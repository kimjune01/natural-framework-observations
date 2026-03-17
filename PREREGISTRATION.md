# Preregistration: Natural Framework vs. Shannon

*Pre-registered before any data collection. Timestamped by commit.*

## Study Information

**Title:** Behavioral Contracts vs. Information-Theoretic Constraints: Head-to-Head Fidelity Comparison of the Natural Framework and Shannon Communication Model

**Authors:** June Kim

**Date:** 2026-03-17

**Framework reference:** [The Natural Framework](https://june.kim/the-natural-framework)

**Formal specification:** [Lean 4 proof](https://github.com/kimjune01/natural-framework) (zero sorry)

---

## Background

Both the Natural Framework (2026) and the Shannon Communication Model (1948) decompose information-processing systems into six named roles with formal inter-stage contracts. Both claim generality beyond their origin domain.

Shannon's contracts are mathematical theorems — impossibility results about rate, capacity, and distortion that no system can violate. NF's contracts are behavioral pre/postconditions — guarantees about data content at each handoff, proven in Lean 4. Shannon's contracts describe limits. NF's contracts describe what must be true for the next stage to function.

Shannon is the champion: the most foundational, universally taught, mathematically rigorous decomposition of information processing. It has been actively applied to deep learning (information bottleneck, Tishby 2015), compression, and signal processing for 78 years.

**Central question:** Does a 2026 behavioral specification beat a 1948 mathematical theory at decomposing real systems? If NF wins, data-content contracts are a better abstraction level than information-theoretic limits. If Shannon wins, the Lean proof reinvented 1948 less elegantly. If they tie, both capture the same structure — the universal substrate is real, and two independent formalizations found it.

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

### Why Shannon is the Champion

Shannon is the strongest possible competitor:
- **Strongest formal contracts** of any decomposition framework (mathematical impossibility results)
- **Most universally known** — taught in every CS/EE curriculum
- **Actively applied** to modern AI (information bottleneck, neural network compression)
- **Domain-general aspirations** — information theory claims to apply wherever information exists
- **78-year track record** of successful application

If NF can't beat Shannon, it can't beat anyone worth beating.

---

## Data Sources

All pre-specified and mechanically enumerable.

### Source 1: RL Framework Repos

**Repos:** Top 50 RL frameworks by GitHub stars:

```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=50
```

RL repos are the strongest test case: they have explicit perceive/act/learn loops where all six NF roles are architecturally visible. Neither lens's home turf.

**Bug issues (Phases 2-5):** Closed, labeled "bug", >= 10 comments, created 2022-2025.

**Predictive validity (Phase 6):** Top 5 repos only (richest commit history).

**Health correlation (Phase 7):** All 50 repos. Health measured by [Libraries.io SourceRank](https://docs.libraries.io/overview.html#sourcerank).

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

Each data point is mapped 3 times per model per lens. Total per data point: 3 runs × 2 models × 2 lenses = 12 mapping calls.

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

A judge model (blind to lens identity — lens name replaced with "Lens A" and "Lens B", randomized per data point) scores reconstruction fidelity:

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

Six hypotheses. One comparison per hypothesis. No multiple comparisons penalty.

### H1: Reconstruction Fidelity (primary)

NF produces higher reconstruction scores than Shannon.

**Test:** Wilcoxon signed-rank, paired by data point.

**Success:** NF mean > Shannon, p < 0.05. Behavioral contracts describe real systems better than information-theoretic limits.

**Failure:** Shannon >= NF. Mathematical constraints are sufficient. The Lean proof adds nothing over 1948.

### H2: Mapping Consistency

NF produces higher self-consistency across 3 runs than Shannon.

**Test:** Fleiss' kappa across 3 runs per model, per lens.

**Success:** NF kappa > Shannon kappa for both models. Behavioral contracts reduce ambiguity.

**Failure:** Shannon kappa >= NF. Mathematical crispness beats behavioral specificity. Shannon's roles are less ambiguous because they're defined by theorems.

### H3: Diagnostic Actionability

NF role labels produce repair suggestions that match actual resolutions better than Shannon.

**Test:** Wilcoxon signed-rank, paired by failure.

**Success:** NF mean > Shannon, p < 0.05. "Filter's postcondition is violated" points to fixes better than "channel capacity exceeded."

**Failure:** Shannon >= NF. Information-theoretic diagnosis is equally or more actionable.

### H4: Predictive Validity

NF diagnoses of early-stage codebases predict subsequent development better than Shannon.

**Test:** Wilcoxon signed-rank, paired by repo × prediction.

**Success:** NF mean > Shannon, p < 0.05. The handshake (each postcondition enables the next precondition) predicts what gets built next.

**Failure:** Shannon >= NF. Rate/capacity constraints predict development equally well.

### H5: Health Correlation

NF role completeness correlates more strongly with project health (SourceRank) than Shannon role completeness.

**Test:** Steiger's test comparing Spearman's rho (NF vs SourceRank) against rho (Shannon vs SourceRank), n=50.

**Success:** NF rho > Shannon rho, p < 0.05.

**Failure:** Shannon rho >= NF. Information-theoretic completeness tracks health equally well.

**Power note:** n=50 detects rho difference >= 0.28 at p < 0.05.

### H6: Domain Independence

NF has lower variance in fidelity scores across the three data sources than Shannon.

NF claims domains are structurally self-similar — all information processing underneath. Shannon claims information theory applies wherever information exists. Both claim generality. The more domain-independent lens will perform uniformly across sources; the more domain-specific one will spike on its home turf.

**Test:** Levene's test comparing cross-source variance of NF fidelity vs Shannon fidelity.

**Success:** NF variance < Shannon variance. NF captures cross-domain structure that Shannon misses.

**Failure:** Shannon variance <= NF. Information theory is already domain-independent. The universal substrate was described in 1948.

---

## Analysis Plan

```
For each data point d and each lens L ∈ {NF, Shannon}:
  fidelity(d, L) = median score across 3 mapper runs × 2 judge models

H1: W, p = wilcoxon_signed_rank(fidelity_NF, fidelity_Shannon)
H2: compare kappa_NF vs kappa_Shannon per model
H3: W, p = wilcoxon_signed_rank(action_NF, action_Shannon)
H4: W, p = wilcoxon_signed_rank(predict_NF, predict_Shannon)
H5: z, p = steiger_test(rho_NF, rho_Shannon, n=50)
H6: F, p = levene_test(fidelity_NF by source, fidelity_Shannon by source)

Six tests. No multiple comparisons correction needed for a single primary
comparison. Report effect sizes and confidence intervals for all.
```

### Cross-Model Agreement

Compare GPT-5.4 and Sonnet 4.5 mappings for each lens. If the models disagree (one favors NF, the other Shannon), the result is model-dependent.

### Exploratory Analyses (labeled as such)

- Which NF role boundaries cause the most inconsistency? (per-role kappa)
- Which Shannon role boundaries cause the most inconsistency?
- Per-source breakdown: does either lens spike on a particular data source?
- Role correspondence: which NF roles map to which Shannon roles on the same components?
- Do the lenses agree on "unmapped" cases?

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (fidelity) | NF > Shannon | NF's contracts distinguish Cache/Remember and Filter/Attend at the behavioral level. Shannon collapses these: Cache and Channel overlap; Filter has no analog. |
| H2 (consistency) | Shannon > NF | Shannon's roles are defined by mathematical theorems. "Is it the channel or the noise?" is crisper than "is it Filter or Attend?" |
| H3 (actionability) | NF > Shannon | "Filter's postcondition is violated — items that should be rejected are reaching Attend" points to a fix. "Channel capacity exceeded" tells you the limit, not the repair. |
| H4 (prediction) | NF > Shannon | NF's handshake predicts ordering: earlier stages get built first. Shannon has no ordering prediction — all stages are instantiated simultaneously in a communication system. |
| H5 (health) | NF > Shannon | NF roles map to implementation concerns (buffering, filtering, persistence). Shannon roles map to communication architecture (encoding, channels). Implementation gaps predict health; architecture gaps are less specific. |
| H6 (domain independence) | NF flatter | Shannon will spike on signal-processing-adjacent failures and drop on organizational post-mortems. NF should perform uniformly because its contracts are about data content, not communication structure. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF wins H1, H3, H4, H6 | Behavioral contracts are load-bearing across all dimensions. The Lean proof was necessary. Ship the framework. |
| NF wins H1, H3; Shannon wins H2 | Behavioral contracts describe and diagnose better, but mathematical contracts are more consistent. Each has its domain. |
| Shannon wins H1 | **The devastating result.** Information-theoretic constraints decompose real systems better than behavioral pre/postconditions. The Lean proof is a verbose restatement of Shannon in dependent types. |
| Shannon wins H2 but loses everything else | Mathematical crispness aids consistency but not utility. Crisp categories can be consistently wrong about the real world. |
| Tie on H1, NF wins H4 | The vocabulary is interchangeable, but the handshake predicts. The ordering theory is load-bearing; the role names are not. |
| Tie on everything | Both formalizations capture the same structure. The universal substrate is real, and two independent approaches found it 78 years apart. NF's contribution is the explicit pipeline ordering, not the decomposition. |
| Shannon wins H6 | Information theory is already domain-independent. The universal substrate was described in 1948. |
| NF loses all six | The six roles are a formal specification with no practical advantage over the most famous framework in information science. |

---

## Budget

No human coders. Each trial is a CLI call. Two lenses instead of eight.

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

**Survivorship bias (H4, partially addressed by H5).** H4 uses the top 5 RL repos — projects that survived. H5 expands to 50 repos with natural health variance. Projects that died before gaining traction are absent.

**Tech-domain bias (all hypotheses).** All three sources are software/ML. Both frameworks claim broader generality. This study tests tech systems only.

**LLM bias (all hypotheses).** Shannon appears far more in training data than NF. If training-data fluency helps, Shannon has an unfair advantage. Cross-model comparison partially controls for this.

**Contract research quality.** The researcher designed NF's contracts and researched Shannon's from canonical sources. Shannon's contracts are well-documented (textbook-level); NF's are the researcher's own work. Mitigation: both rubrics are committed verbatim and auditable. [Shannon contract research →](lenses/shannon.md)

---

## Transparency

- All prompts committed verbatim before Phase 2 begins.
- Lens anonymization key committed in a sealed file, opened only during analysis.
- All raw data published as JSON/CSV.
- This preregistration is timestamped by commit. No amendments after Phase 2 begins without a dated commit explaining the change.

---

*Head-to-head comparison: the Natural Framework (2026, Lean 4) vs. the Shannon Communication Model (1948, information theory). Champion vs. challenger. Both have six roles and formal contracts. The question is which type of contract decomposes real systems with higher fidelity.*
