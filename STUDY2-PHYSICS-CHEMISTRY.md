# Study 2: Physics vs Chemistry

*Which level of description fits the instruments we have?*

*Pre-registered before any data collection. Timestamped by commit. Depends on [Study 1](STUDY1-LEAN-TEST.md) data.*

## Study Information

**Title:** Behavioral vs Information-Theoretic Contracts for Software System Decomposition

**Authors:** June Kim

**Date:** 2026-03-17

**Prerequisite:** Study 1 must complete first. This study reuses Study 1's NF mappings and adds Shannon as a comparator, plus two downstream tasks (prediction, maturity correlation).

---

## Background

Study 1 tests whether composable contracts carry information (NF vs NF-scrambled vs NF-bare). This study asks a different question: how does NF compare to Shannon?

Shannon is physics: mathematical theorems about rate, capacity, and distortion. NF is chemistry: behavioral pre/postconditions about data content at handoffs. Shannon describes speeding. NF describes the speeding ticket. We're reading the filing cabinet.

This comparison is heavily confounded — different roles, different contract types, different vocabulary, observability skew. It's context, not a clean test. We run it because the answer is interesting even if it's not isolable.

---

## Conditions

### Shannon Communication Model (1948)

| Role | Definition | Contract |
|------|-----------|---------|
| **Source** | Produces the message | Output has entropy H(X) |
| **Encoder** | Converts message into transmittable signal | Rate R ≥ H(X) for lossless; R < C for reliable transmission |
| **Channel** | Medium the signal travels through | Capacity C = max I(X;Y); defines the bottleneck |
| **Noise** | Interference corrupting the signal | Modeled by p(y\|x); decoder must know this |
| **Decoder** | Reconstructs message from received signal | Must share codebook with encoder; error → 0 as block length → ∞ |
| **Destination** | Intended recipient | Receives reconstructed message |

Contracts are **mathematical theorems**. [Full details →](lenses/shannon.md)

### NF (correct contracts)

Reused from Study 1. See [Study 1](STUDY1-LEAN-TEST.md) for the full table.

### Known Confounds

| Confound | Direction | Severity |
|----------|-----------|----------|
| **Observability skew** | Favors NF. We have code (procedures), not data (streams). NF points at procedures; Shannon points at data properties. | High |
| **Vocabulary** | Favors NF. "Perceive, filter, attend" are software-adjacent. "Source, encoder, channel" are not. | Medium |
| **Discrete vs continuous** | Favors NF on binary outcomes (bugs). May favor Shannon on continuous outcomes (ablation deltas). | Medium |
| **Training data** | Favors Shannon. Far more Shannon content in LLM training data. | Low-medium |
| **Researcher bias** | Favors NF. The researcher designed NF's contracts. | Medium |

These confounds make it impossible to isolate *why* one condition outperforms the other. This study tells us *which* framework is more useful for this specific task with these specific instruments. It doesn't tell us which is "better."

---

## Data Sources

Same as Study 1, plus:

### Predictive Validity (Phase 6)

**Repos:** Top 5 RL repos by stars (subset of Study 1's 50). Richest commit history.

### Maturity Correlation (Phase 7)

**Repos:** All 50 RL repos from Study 1. Ecosystem maturity measured by [Libraries.io SourceRank](https://docs.libraries.io/overview.html#sourcerank) (activity, dependencies, contributors — a proxy for maturity, not health).

---

## Procedure

### Phases 2-5: Fidelity and Actionability

Run the Shannon condition through the same pipeline as Study 1 (mapping → reconstruction → judging → actionability). Same prompts, same models, same runs.

3 runs × 2 models × 1 new condition = 6 additional calls per data point.

NF data reused from Study 1.

### Phase 6: Predictive Validity (top 5 repos)

**Step 1: Snapshot.** Check out the earliest tagged release (or 25th-percentile commit). Record the hash.

**Step 2: Diagnose.**

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

3 runs × 2 models × 2 conditions (NF, Shannon) = 12 calls per repo.

Only predictions naming a specific file, function, or behavior count. Vague predictions score 0.

**Step 3: Check against history.**

> Here is a prediction made about a software project based on its early source code:
> [prediction]
>
> Here is what actually happened:
> [mechanically extracted: git log --oneline from snapshot to HEAD, plus issue titles closed in that range]
>
> Score the prediction:
> - 3: The predicted change or bug actually occurred
> - 2: Something related occurred but not exactly as predicted
> - 1: The prediction did not materialize
>
> Return: {"score": N, "rationale": "one sentence"}

One aggregated score per repo per condition before inference.

### Phase 7: Maturity Correlation (all 50 repos)

Diagnose current codebase (HEAD) using both conditions. Count roles rated present, partial, or missing. Correlate role completeness with SourceRank.

3 runs × 2 models × 2 conditions = 12 calls per repo. 50 repos = 600 calls.

---

## Hypotheses

All secondary to Study 1. All exploratory in character — the confounds preclude causal claims.

### H1: Level of Description (fidelity)

NF produces higher reconstruction scores than Shannon.

**Test:** Wilcoxon signed-rank, paired by data point. α = 0.05.

**Interpretation:** If NF > Shannon, chemistry outperforms physics *when reading the filing cabinet*. Does not generalize to tasks where Shannon's quantities are measurable.

### H2: Level of Description (prediction)

NF predictions score higher than Shannon predictions.

**Test:** Wilcoxon signed-rank, paired by repo. α = 0.025 (Holm-Bonferroni, 2 secondary).

**Power note:** n=5 is extremely low. This is a pilot, not a powered test.

### H3: Maturity Correlation

NF role completeness correlates more strongly with SourceRank than Shannon's.

**Test:** Steiger's test, n=50. α = 0.025 (Holm-Bonferroni).

**Power note:** n=50 detects rho difference >= 0.28. SourceRank measures ecosystem activity, not architectural health. Weak signal at best.

---

## Analysis Plan

```
Unit of analysis: the data point.

H1: W, p = wilcoxon_signed_rank(fidelity_NF, fidelity_Shannon)
H2: W, p = wilcoxon_signed_rank(predict_NF, predict_Shannon)  [n=5, pilot]
H3: z, p = steiger_test(rho_NF, rho_Shannon, n=50)

Report effect sizes and 95% CIs.
```

### Exploratory

- Per-source breakdown: does Shannon spike on ablation tables (continuous outcomes)?
- Role correspondence: which NF roles map to which Shannon roles on the same components?
- NF-bare vs Shannon: are NF's labels alone better than Shannon's labels + theorems?
- Unmapped rate comparison

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (fidelity) | NF > Shannon | NF distinguishes Cache/Remember and Filter/Attend behaviorally. Shannon collapses these. Plus observability skew. |
| H2 (prediction) | NF > Shannon | NF's pipeline ordering generates more specific predictions about software development. But n=5. |
| H3 (maturity) | NF rho > Shannon rho | NF vocabulary maps more naturally to RL architecture. But SourceRank is a weak proxy. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF > Shannon on all three | Chemistry outperforms physics for software artifact analysis. Expected given observability skew. |
| Shannon > NF on fidelity | Shannon outperforms despite observability skew. Strong result — information-theoretic vocabulary useful even without instruments. |
| NF > Shannon on fidelity, Shannon > NF on prediction | Different tasks favor different levels. Fidelity rewards behavioral vocabulary; prediction rewards mathematical structure. |
| NF ≈ Shannon on everything | Level of description doesn't matter for this task. Both 6-role taxonomies work equally well. |

---

## Budget

Shannon condition + prediction + maturity.

| Phase | Per unit | Units | Calls |
|-------|----------|-------|-------|
| Phases 2-5 (Shannon only) | ~20 calls/data point | ~100 data points | ~2,000 |
| Phase 6 | ~24 calls/repo | 5 repos | ~120 |
| Phase 7 | ~12 calls/repo | 50 repos | ~600 |
| **Total** | | | **~2,700** |

At ~10s per call: ~7.5 hours wall clock. NF data reused from Study 1.

---

## Limitations

**All confounds acknowledged.** This study cannot isolate why one condition outperforms the other. It measures comparative usefulness for a specific task with specific instruments.

**Observability skew.** We're reading the filing cabinet. NF is designed for this; Shannon isn't.

**Home turf.** RL repos favor NF vocabulary.

**n=5 for prediction.** Phase 6 is a pilot, not a powered test.

**SourceRank ≠ health.** Ecosystem activity proxy. Popular projects score high regardless of architecture.

**Study 1 dependency.** If Study 1 shows NF ≈ NF-scrambled ≈ NF-bare, the NF data reused here has no special validity.

---

## Transparency

- Same commit and logging policy as Study 1.
- Shannon rubric committed verbatim. [Full details →](lenses/shannon.md)
- All raw data published.

---

*NF vs Shannon. All confounds acknowledged, none controlled. This tells us which level of description fits the instruments we have — filing cabinet data from software repos. Shannon might win on running systems with instrumentation. We don't have running systems.*
