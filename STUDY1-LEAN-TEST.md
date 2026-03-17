# Study 1: The Lean Test

*Does contract composition carry information, or are role labels enough?*

*Pre-registered before any data collection. Timestamped by commit.*

## Study Information

**Title:** Do Composable Contracts Improve LLM-Based Software Decomposition?

**Authors:** June Kim

**Date:** 2026-03-17

**Framework reference:** [The Natural Framework](https://june.kim/the-natural-framework)

**Formal specification:** [Lean 4 proof](https://github.com/kimjune01/natural-framework) (zero sorry)

---

## Background

The Natural Framework (NF) decomposes information-processing systems into six roles with formal pre/postconditions proven in Lean 4. Each postcondition guarantees the next precondition — the contracts *compose*.

This study asks whether composable contract text carries information when used as prompt context for LLM-based decomposition. NF's six roles can be stated three ways: with composable contracts, with shuffled contracts (same text, wrong assignment), or with no contracts (labels only). If composable contracts outperform shuffled ones at the same token count, the specific arrangement matters. If shuffled contracts outperform bare labels, even misassigned contract text helps. If all three perform equally, the role names and definitions carry all the recoverable information, with contracts adding no detectable value.

This is an LLM-evaluation study. It tests whether composable contract text improves LLM-mediated decomposition of software artifacts. It does not test whether Lean proofs are necessary for software engineering, whether these contracts are correct descriptions of real systems, or whether the framework generalizes beyond this task.

---

## The Three Conditions

Full specifications in [`rubric.md`](rubric.md).

### NF (correct contracts)

| Role | Definition | Precondition | Postcondition |
|------|-----------|-------------|--------------|
| **Perceive** | Ingest external input, convert to internal format | External data exists | Data is in internal format |
| **Cache** | Temporarily hold data between steps | Data is in internal format | Data is buffered, retrievable |
| **Filter** | Gate, threshold, or reject by criterion | Buffered data available | Failing items rejected; passing items remain |
| **Attend** | Rank, score, or select among items | Items have been filtered | Items ranked by priority |
| **Remember** | Write to persistent storage | Items ranked/selected | Data persisted, retrievable across cycles |
| **Consolidate** | Read past outcomes, update parameters | Store contains past outcomes | Parameters updated; next cycle reflects past |

Contracts are **behavioral pre/postconditions** proven in Lean 4. Each postcondition guarantees the next precondition.

### NF-scrambled (wrong contracts)

| Role | Definition | Precondition | Postcondition |
|------|-----------|-------------|--------------|
| **Perceive** | Ingest external input, convert to internal format | Items ranked by priority | Data is buffered, retrievable |
| **Cache** | Temporarily hold data between steps | Store contains past outcomes | Failing items rejected; passing items remain |
| **Filter** | Gate, threshold, or reject by criterion | Data is in internal format | Parameters updated; next cycle reflects past |
| **Attend** | Rank, score, or select among items | Data persisted, retrievable across cycles | External data exists |
| **Remember** | Write to persistent storage | Failing items rejected; passing items remain | Data is in internal format |
| **Consolidate** | Read past outcomes, update parameters | Data is buffered, retrievable | Items ranked by priority |

Same roles, same definitions, same token count. **The contracts are shuffled** — each role gets another role's conditions. They don't compose. This is NF without the Lean proof's guarantee.

### NF-bare (labels only)

| Role | Definition |
|------|-----------|
| **Perceive** | Ingest external input, convert to internal format |
| **Cache** | Temporarily hold data between steps |
| **Filter** | Gate, threshold, or reject by criterion |
| **Attend** | Rank, score, or select among items |
| **Remember** | Write to persistent storage |
| **Consolidate** | Read past outcomes, update parameters |

Same roles, same definitions. **No pre/postconditions.**

### Controlled Comparison

| Dimension | NF | NF-scrambled | NF-bare |
|-----------|-----|-------------|---------|
| Role count | 6 | 6 | 6 |
| Role definitions | Identical | Identical | Identical |
| Contracts | Composable (Lean-proven) | Shuffled (not composable) | None |
| Token count | Full | ~same as NF | Shorter |

**H1 comparison (NF vs NF-scrambled):** Same roles, same definitions, same token count. Only difference: composition. Length-controlled.

**Exploratory comparison (NF-scrambled vs NF-bare):** Same roles, same definitions. NF-scrambled has more text. Not length-controlled — if NF-scrambled wins, it might be more tokens.

---

## Data Sources

All pre-specified and mechanically enumerable.

### Source 1: RL Framework Bug Issues

**Repos:** Top 50 RL frameworks by GitHub stars:

```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=50
```

RL repos have explicit perceive/act/learn loops where NF roles are architecturally visible. This is NF's home turf — acknowledged, not hidden.

**Issues:** Closed, labeled "bug", >= 10 comments, created 2022-2025.

### Source 2: Public Post-Mortems

**Source:** All entries on [github.com/danluu/post-mortems](https://github.com/danluu/post-mortems) at a snapshot commit hash (recorded in `DATA_SOURCES.md`). Exclude hardware-only root cause, natural disaster, physical security breach.

### Source 3: ML Ablation Tables

**Papers:** NeurIPS 2024 oral/spotlight papers containing "ablation" in the text.

**Rows:** Every row that removes or disables a named component.

### Extraction Protocol

**Unit of analysis:** One component or failure per data point.

**Source 1 extraction:** For each qualifying issue, extract the first message's description of the bug. If the issue describes multiple independent bugs, take only the first. The data point text is the issue title + first paragraph of the issue body. Exclude issues where the bug is "feature request mislabeled as bug" or "documentation error."

**Source 2 extraction:** For each post-mortem, extract the root cause section (or equivalent). If the post-mortem describes a cascade of failures, take the initiating failure only. The data point text is the root cause description, max 200 words. Exclude post-mortems with no identifiable software component (e.g., "human operator error" with no system context).

**Source 3 extraction:** For each ablation row, extract the component name and its description from the paper's method section. The data point text is: "[component name]: [description from paper]." If the paper doesn't describe the component beyond naming it, exclude that row.

**Deduplication:** If the same component appears in multiple issues or rows, keep the first occurrence in enumeration order.

**Independence rule:** At most one data point per parent source (one per repo for Source 1, one per post-mortem for Source 2, one per paper for Source 3). If a repo has multiple qualifying issues, take the first by creation date. If a paper has multiple ablation rows, take the first row in the table. This ensures observations are independent across different systems/incidents/papers.

**Ambiguous cases:** Flag with `uncertain: true` in the data point record. Include in primary analysis. Report sensitivity analysis with uncertain cases excluded.

---

## Procedure

Single model: GPT-5.4 via codex CLI. Cross-model replication left to future work.

**Generation settings (frozen at Phase 2 start, no mid-study changes):** Record `codex --version` and the exact model identifier returned by the API in the first call. Temperature = 1.0 (codex CLI default). Seeds cannot be fixed via codex CLI — the 3 runs per data point capture stochastic variation. All raw responses are logged verbatim. No generation settings are changed after Phase 2 begins, regardless of CLI updates.

### Phase 1: Data Enumeration

Run all queries mechanically. Commit results as `data/enumeration/source1_repos.csv`, `data/enumeration/source2_postmortems.csv`, `data/enumeration/source3_papers.csv` before any screening begins. These snapshots freeze the sampling frame. Randomize qualified data point order into `data/processing_order.csv` before Phase 2 — this is the processing order for sequential analysis.

### Phase 2: Mapping

For each data point and each condition:

**Mapper prompt** (used verbatim for each run × condition × data point):

> Here is a component or failure description from a real system:
>
> [component description inserted here]
>
> Here is a decomposition framework with 6 roles:
>
> [condition rubric inserted here]
>
> 1. Which role does this component best fit? Pick exactly one, or say "unmapped" if none fit.
> 2. In one sentence, what does a component with this role label typically do?

3 runs × 1 model × 3 conditions = 9 mapping calls per data point.

**Token counts:** Record and report token counts for each condition's rubric. NF and NF-scrambled must be within 5% of each other.

**Unmapped items:** Excluded from fidelity scoring (Phases 3-4) and actionability (Phase 5). Unmapped rate is reported as a separate pre-registered outcome. If a data point is unmapped under one condition but mapped under another, the mapped conditions still receive fidelity scores — the unmapped condition simply contributes no paired observation for that data point.

**Mapping outcomes (pre-registered, reported before fidelity):**
- Unmapped rate per condition. If one condition produces significantly more "unmapped" responses, it's harder to use, independent of fidelity.
- Role assignment distribution per condition. Chi-squared test comparing NF vs NF-scrambled distributions (do composable contracts shift which roles get assigned?).
- Cross-condition agreement: for data points mapped by all three conditions, do NF and NF-scrambled assign the same role? (They share definitions, so disagreement indicates contract text influences mapping, not just reconstruction.)

### Phase 3: Reconstruction

**Reconstruction prompt** (used verbatim; for NF-bare, the contract line is omitted; for NF-scrambled, the shuffled contracts are included):

> A component in an information-processing system was labeled with the role "[ROLE_LABEL]" in a framework called "[LENS_NAME]".
>
> The framework defines this role as: "[ROLE_DEFINITION]"
>
> This role has the following contract: [CONTRACT_TEXT]
>
> Based only on this label, definition, and contract, write one sentence describing what this component most likely does. Be as specific as possible.

### Phase 4: Judging

Judge model scores reconstruction fidelity with condition labels masked ("Lens A"/"Lens B"/"Lens C", randomized per data point). Note: outputs may carry stylistic traces of their condition, so this is label masking, not full blinding.

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

3 judge runs per reconstruction. Majority vote.

**Human validation subset:** 20 data points, stratified random sample (balanced across sources). Two human raters score the same reconstructions on the same 1-3 rubric, blind to condition. Report human-human Cohen's κ and human-LLM agreement. If human-LLM κ < 0.4, results are suspect.

### Phase 5: Actionability (Sources 1 and 2 only, with resolution)

**Eligibility:** Only failures with an explicitly described resolution/remediation in the source text. If the resolution has multiple parts, use the first stated remediation. Exclude failures where the resolution is "unclear," "ongoing," or "not yet resolved."

**Repair prompt** (used verbatim; for NF-bare, contract lines are omitted):

> A system failure was categorized under the role "[ROLE_LABEL]" in a framework called "[LENS_NAME]".
>
> The framework defines this role as: "[ROLE_DEFINITION]"
>
> This role's contract is: [CONTRACT_TEXT]. The failure means this contract was violated.
>
> Based only on this label, definition, and contract violation, write one sentence suggesting how to fix the system.

Judge scores the repair on a 1-3 scale:

> Score this repair suggestion:
> - 3: Addresses the root cause of the failure
> - 2: Plausible mitigation but doesn't address root cause
> - 1: Irrelevant or would not help
>
> The actual resolution was: [actual resolution]
> Does the suggestion match? {"score": N, "matches_actual": true/false, "rationale": "one sentence"}

This separates plausibility (the 1-3 score) from historical match (the boolean). A good suggestion that differs from the actual fix still scores 3.

---

## Hypotheses

Two hypotheses tested sequentially with Bayesian updating. One exploratory comparison.

### H1: Does Composable Contract Text Improve Fidelity? (primary)

NF produces higher reconstruction scores than NF-scrambled.

**Statistical model:** For each data point, compute d = score(NF) - score(NF_scrambled). Scores are ordinal {1, 2, 3}; differences are in {-2, -1, 0, 1, 2}. Classify each d as positive (d > 0), negative (d < 0), or tied (d = 0). Ties are excluded from the sign test (reported separately as tie rate).

Let k = number of positive signs out of n_nontied observations. Under H₀, k ~ Binomial(n_nontied, 0.5). Under H₁, k ~ Binomial(n_nontied, θ) with θ ~ Beta(1, 1) prior (uniform on [0, 1]).

BF₁₀ = P(data | H₁) / P(data | H₀) = [B(k+1, n_nontied-k+1) × 2^n_nontied] / 1, where B is the beta function.

**Decision rule:**
- BF₁₀ > 10: **Stop. Strong evidence composable contracts improve fidelity** for this model on these data.
- BF₁₀ < 0.1: **Stop. Strong evidence composability doesn't matter** for this task.
- 0.1 ≤ BF₁₀ ≤ 10: **Continue.** Inconclusive.

**Minimum sample:** 10 data points before first check.

**Maximum sample:** All qualifying data points. If still inconclusive after exhausting the dataset, report the final BF, posterior on θ, and tie rate.

### H2: Does Composable Contract Text Improve Diagnosis? (secondary)

NF produces repair suggestions closer to actual resolutions than NF-scrambled.

**Same statistical model** applied to actionability scores. Only runs on Sources 1 and 2 data points that describe failures. **Runs independently of H1** — H2 continues regardless of H1's outcome, on its own data point stream (failures only, in the same pre-randomized order).

### Exploratory: Does Contract Text Help At All?

NF-scrambled vs NF-bare. Same sequential procedure but **no stopping rule** — runs on all data points processed for H1. Reported as exploratory because the comparison is confounded by prompt length. A positive result means "adding this contract text changes performance," not "contracts help."

---

## Analysis Plan

```
For each data point d (in pre-randomized order):
  score(d, C) = median fidelity across 3 runs for condition C
  diff_H1(d) = score(d, NF) - score(d, NF_scrambled)
  diff_expl(d) = score(d, NF_scrambled) - score(d, NF_bare)

After each data point (n >= 10):
  Exclude ties (diff = 0) from sign count.
  k = count(diff > 0), m = count(diff != 0)
  BF₁₀ = Beta(k+1, m-k+1) * 2^m  (Bayesian sign test)

  Log: n, n_nontied, k, BF₁₀, posterior_mean(θ), tie_rate
  to sequential_log.csv

  If BF₁₀ > 10 or BF₁₀ < 0.1 for H1: stop fidelity pipeline.
  H2 continues independently on failure data points.

Report per hypothesis:
  - Final BF₁₀, posterior on θ, tie rate
  - Sequential plot: BF₁₀ vs n
  - Effect size: median paired difference with 95% HDI
  - Source-specific BF (per-source breakdown, see below)
```

### Pre-registered analyses (not hypotheses)

- **Mapping outcomes:** Unmapped rate, role distribution, cross-condition agreement (reported before fidelity).
- **Per-source BF:** Compute H1's BF separately for each source (minimum 10 data points per source before reporting). If the effect is driven by one source, the pooled result is misleading.
- **Reconstruction-given-matched-role:** For data points where NF and NF-scrambled assign the same role, recompute H1's BF on this subset only. If the effect disappears when mapping is held constant, composition helps mapping, not reconstruction. If the effect persists, composition helps reconstruction even when the role is identical.

### Exploratory (labeled as such)

- NF-scrambled vs NF-bare (confounded by prompt length)
- Per-condition Fleiss' kappa
- NF vs NF-bare (combines composition + text effects)
- Per-role fidelity scores

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (fidelity) | BF₁₀ > 10 by n ≈ 20-30 | Composable contracts narrow reconstruction. "Filter's postcondition enables Attend's precondition" carries structural information that shuffled contracts don't. |
| H2 (diagnosis) | BF₁₀ > 10 | Composable contract violations point to specific fixes. Shuffled violations point to wrong fixes. |
| Exploratory (text) | BF₁₀ near 1 | Wrong contracts might help slightly (more text) or hurt (misleading). Unclear direction, confounded by length. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| H1: BF > 10 | **Composable contract text improves LLM fidelity** on this task. Consistent with the Lean proof capturing real structure, but does not prove it — the proof may have helped write better text, or the composition pattern may be independently useful as prompt context. |
| H1: BF < 0.1 | **Composable contract text does not improve LLM fidelity** on this task. The role definitions already carry the information. Adding pre/postconditions — even correctly composed ones — doesn't help the LLM reconstruct component descriptions. |
| H1: BF > 10, H2: BF < 0.1 | **Composition helps fidelity but not diagnosis.** The contracts carry information about what things are, not about how to fix them. |
| H1: inconclusive | **Effect is small or absent.** Report posterior and move on. The role definitions may be so semantically rich that contracts add only marginal information — too small to detect at this sample size. |

---

## Budget

Single model, sequential stopping. Worst case (inconclusive, run everything):

| Phase | Per data point | Max data points | Max calls |
|-------|---------------|----------------|-----------|
| Phases 2-4 (fidelity) | 27 calls | ~100 | ~2,700 |
| Phase 5 (actionability) | 9 calls | ~100 | ~900 |
| **Total** | | | **~3,600** |

**Expected case:** If composition effect is large, H1 stops at n ≈ 20-30. That's ~540-810 fidelity calls. H2 (diagnosis) continues independently on failure data points.

At ~15s per codex CLI call: worst case ~15 hours, expected case ~3-4 hours.

---

## Stopping Rules

**Sequential Bayesian stopping.** Process data points in pre-randomized order. Check BF after each point (n >= 10). Stop when BF₁₀ > 10 or BF₁₀ < 0.1, or when data is exhausted.

**Minimum viable dataset** (for enumeration, before sequential processing begins):
- Source 1 (bugs): >= 20 qualifying bug issues
- Source 2: >= 30 qualifying post-mortems
- Source 3: >= 50 ablation rows

If total qualifying data points < 30, the study is underpowered. Report descriptive statistics only.

---

## Logging

```
data/
├── enumeration/          # Raw query results, committed before mapping
├── processing_order.csv  # Pre-randomized order, committed before Phase 2
├── mappings/             # {datapoint}_{condition}_{run}.json
├── reconstructions/      # {datapoint}_{condition}_{run}_recon.json
├── judgments/            # {datapoint}_{condition}_{run}_judge{1,2,3}.json
├── repairs/              # {datapoint}_{condition}_{run}_repair.json
├── sequential_log.csv    # BF, n, posterior after each data point
├── scores/               # Aggregated scores per data point per condition
└── analysis/             # Final results, plots
```

### Commit policy

- Enumeration + processing order: committed before any mapping begins.
- Mappings: committed after each data point (append-only).
- Sequential log: committed after each BF update.
- No data is overwritten.

---

## Limitations

**This is an LLM-evaluation study.** It tests whether contract text improves LLM-mediated decomposition of software artifacts. It does not test whether Lean proofs are necessary for software engineering in general.

**Single model.** GPT-5.4 only. If the result is model-specific, we won't know. Cross-model replication is future work.

**Token-count confound (exploratory comparison only).** H1 (NF vs NF-scrambled) is length-controlled. The exploratory NF-scrambled vs NF-bare comparison is not. If NF-scrambled > NF-bare, the win might be more tokens.

**Tech-domain bias.** All sources are software/ML. The framework claims broader generality.

**LLM bias.** All three conditions share the same role names, reducing one obvious familiarity confound. Actual training data exposure is unknown.

**Home turf.** RL repos have perceive/act/learn loops that map naturally to NF vocabulary. All three conditions share the same vocabulary, so this doesn't bias H1.

**Researcher designed the rubrics.** The treatment is literally text shown to LLMs. Wording choices could favor NF. Mitigation: all rubrics committed verbatim; NF-scrambled uses the exact same text, just reassigned.

**Definitions may carry most of the signal.** The role names and definitions are semantically rich ("Remember: write to persistent storage" already implies persistence without any contract). If definitions dominate, the test is underpowered — contracts add marginal information on top of a strong baseline. A null result on H1 would be ambiguous: composition might matter but be undetectable over the definitions' signal.

**Sequential stopping.** Optional stopping with Bayesian analysis is principled (the likelihood principle guarantees valid inference regardless of stopping rule), but early stopping on small samples can give imprecise effect size estimates. We report the full posterior, not just the decision.

---

## Transparency

- All prompts committed verbatim before Phase 2.
- Processing order randomized and committed before Phase 2.
- Condition labels anonymized during judging ("Lens A"/"Lens B"/"Lens C").
- Sequential log published after each data point.
- All raw data published as JSON/CSV.
- Timestamped by commit. No amendments after Phase 2 without a dated commit.

---

*Three conditions. One question: does composable contract text improve LLM-based decomposition? NF vs NF-scrambled at the same token count. Bayesian sequential testing — stop when the evidence is clear.*
