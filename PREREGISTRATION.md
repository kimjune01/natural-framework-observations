# Preregistration: Retrospective Validation of the Natural Framework

*Pre-registered before any data collection. Timestamped by the initial commit.*

## Study Information

**Title:** Retrospective Case-Study Validation of the Natural Framework's Six-Role Pipeline and Death Conditions

**Authors:** June Kim

**Date:** 2026-03-17

**Framework reference:** [The Natural Framework](https://june.kim/the-natural-framework)

**Formal specification:** [Lean 4 proof](https://github.com/kimjune01/natural-framework-lean) (zero sorry)

---

## Background

The Natural Framework claims six roles are necessary for any bounded information processor: Perceive, Cache, Filter, Attend, Remember, and Consolidate. Five run forward as stages; Consolidate reads from Remember and writes parameter changes back to the substrate.

The framework makes two kinds of testable claims:

1. **Structural:** Every working information system implements all six roles.
2. **Failure:** When a role is removed or broken, the system degrades in predictable ways (three death conditions: broken step, closed loop, decaying input).

The math community responds: it's interfaces, not math. The science community responds: where's the empirical evidence? This study addresses the latter by testing whether public failures and ablation experiments map to the framework's predictions, using pre-specified data sources and coding criteria.

---

## Design

Retrospective case studies on three pre-specified, enumerable data sources. Two independent coders map components to roles and failures to death conditions using a pre-registered rubric (`rubric.md`). All coded data published as CSV.

---

## Data Sources

All sources are pre-specified and mechanically enumerable. No researcher discretion in what enters the dataset.

### Source 1: RL Framework Bug Issues

**Repos:** Top 5 reinforcement learning frameworks by GitHub stars, determined mechanically:

```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=5
```

Run this query once. Take the top 5. No substitutions.

**Issues:** All closed issues satisfying:
- Labeled "bug" (or repo-equivalent: "defect", "bugfix")
- Created between 2022-01-01 and 2025-12-31
- At least 10 comments

**Rationale:** RL agents have explicit perceive/act/learn loops, closest to the pipeline. Bug reports with extensive discussion provide enough context to identify which component failed and how.

**Enumeration query per repo:**

```
gh issue list -R {owner}/{repo} --state=closed --label=bug --json number,title,comments,createdAt \
  | jq '[.[] | select(.comments >= 10) | select(.createdAt >= "2022-01-01" and .createdAt <= "2025-12-31")]'
```

### Source 2: Public Post-Mortems

**Source:** All entries on [github.com/danluu/post-mortems](https://github.com/danluu/post-mortems) at the commit hash recorded in `DATA_SOURCES.md`.

**Inclusion:** Software system failures only.

**Exclusion (binary, no discretion):**
- Hardware failure as root cause (e.g., disk death, power outage with no software angle)
- Natural disaster as root cause
- Physical security breach (no software component)

**Rationale:** Post-mortems document what broke and what happened, which is exactly what we need to test the death conditions. The danluu/post-mortems list is curated by a third party, not by us.

### Source 3: ML Ablation Tables

**Papers:** NeurIPS 2024 oral and spotlight papers containing the word "ablation" in the body text.

**Enumeration procedure:**
1. Obtain the official NeurIPS 2024 oral/spotlight paper list from the NeurIPS proceedings page.
2. For each paper, search the PDF for the string "ablation" (case-insensitive).
3. Include all papers where the string appears.

**Tables:** Every row in every table labeled "ablation" (or "ablation study", "ablation results") that removes or disables a named component. Each row is one data point.

**Exclusion:**
- Rows that change a hyperparameter value (not component removal)
- Rows that add a component (not removal)

**Rationale:** Ablations are controlled removal experiments. The framework predicts that removing a component mapped to an earlier pipeline stage (Perceive, Cache) produces larger performance degradation than removing a later-stage component (Attend, Remember). Ablation tables test this directly.

---

## Hypotheses

All hypotheses are specific and falsifiable. Success and failure thresholds are pre-registered.

### H1: Failure Mapping

Each documented failure (Sources 1 and 2) maps to exactly one of the three death conditions:

| Death condition | Definition |
|----------------|-----------|
| **Broken step** | A role's implementation was removed, crashed, or produced incorrect output |
| **Closed loop** | No new data entering the system; the pipeline processes stale or recycled input |
| **Decaying input** | The data source degrades over time (distribution shift, sensor drift, data rot) |

**Success criterion:** Hit rate > 70% (failures that map to exactly one death condition).

**Failure criterion:** Hit rate < 50%.

**Between 50-70%:** Inconclusive. Report the rate and the failures that didn't map.

### H2: Role Mapping Agreement

Each ablated component (Source 3) maps to exactly one of the six roles. Two independent coders apply the decision tree in `rubric.md`.

**Success criterion:** Cohen's kappa > 0.6 (substantial agreement).

**Failure criterion:** Cohen's kappa < 0.4 (fair or worse agreement).

**Between 0.4-0.6:** Moderate agreement. Report as partial support — the rubric distinguishes some roles clearly but not others. Identify which role boundaries are ambiguous.

### H3: Stage-Order Effect

Within ablation studies (Source 3), removing components mapped to earlier pipeline stages produces larger performance degradation than removing components mapped to later stages.

**Stage ordering:** Perceive (1) → Cache (2) → Filter (3) → Attend (4) → Remember (5) → Consolidate (6).

**Metric:** Spearman's rank correlation between role position (1-6) and performance drop (larger drop = lower rank).

**Success criterion:** Spearman's rho < -0.3 with p < 0.05 (negative correlation: earlier stage → larger drop).

**Failure criterion:** Spearman's rho > 0 (positive or no correlation).

**Between -0.3 and 0:** Weak negative trend. Report as suggestive but not confirmed.

---

## Coding Procedure

See `rubric.md` for the full decision tree.

### Coders

Two independent coders. Both must be:
- Unfamiliar with the Natural Framework before the study begins
- Given only `rubric.md`, not the framework post or the Lean proof
- Blinded to the hypotheses (they know they're mapping components to roles and failures to categories, but not what outcome would confirm or disconfirm)

### Training

Before coding real data, both coders practice on 5 synthetic examples (included in `rubric.md`). Disagreements on training examples are discussed and resolved. The rubric may be clarified (not changed in substance) based on training. All clarifications are committed before real coding begins.

### Coding Process

1. Each coder independently codes all data points.
2. Disagreements are flagged but not resolved by discussion — the raw disagreement rate IS the data for H2.
3. For H1 and H3, use the majority coding (if two coders agree) or flag as ambiguous (if they disagree).

---

## Analysis Plan

All analyses pre-registered. No exploratory analyses without labeling them as such.

### H1 Analysis

```
hit_rate = n_mapped / n_total
CI_95 = Wilson score interval (binomial)

├─ hit_rate > 0.70 AND lower bound of CI > 0.50?
│   └─ CONFIRMED
├─ hit_rate < 0.50 AND upper bound of CI < 0.70?
│   └─ DISCONFIRMED
└─ Otherwise?
    └─ INCONCLUSIVE: report rate and CI
```

### H2 Analysis

```
kappa = Cohen's kappa on role assignments (6 categories)

├─ kappa > 0.6?
│   └─ CONFIRMED: rubric produces reliable role mappings
├─ kappa < 0.4?
│   └─ DISCONFIRMED: role boundaries are too ambiguous to code reliably
└─ 0.4 ≤ kappa ≤ 0.6?
    └─ PARTIAL: compute per-role agreement to identify which boundaries are clear
```

### H3 Analysis

```
For each ablation row with a role mapping (majority agreement):
  x = role position (1-6)
  y = performance drop (normalized within each paper's metric)

rho, p = spearman(x, y)

├─ rho < -0.3 AND p < 0.05?
│   └─ CONFIRMED: earlier stages matter more
├─ rho > 0?
│   └─ DISCONFIRMED: no stage-order effect
└─ -0.3 ≤ rho ≤ 0?
    └─ SUGGESTIVE: trend in predicted direction, insufficient power
```

### Exploratory Analyses (labeled as such)

- Which roles are hardest to code? (lowest per-role kappa)
- Which death condition is most common? (frequency distribution)
- Do certain types of systems cluster in certain failure modes?

These do not test hypotheses. They generate questions for future work.

---

## Predictions

| Hypothesis | Prediction | Confidence |
|-----------|-----------|------------|
| H1 | Hit rate ~0.75 | Medium. Post-mortems vary wildly in detail. Some may not describe the failure mechanism clearly enough to code. |
| H2 | Kappa ~0.65 | Medium-high. Filter vs. Attend boundary will be the hardest to code — both involve selection. |
| H3 | Rho ~-0.35 | Low-medium. Ablation tables often remove components that serve multiple roles. The single-role assumption may not hold for composite components. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| H1 confirmed, H2 confirmed, H3 confirmed | Framework's predictions validated retrospectively. Proceed to prospective experiments. |
| H1 confirmed, H2 fails | The death conditions map well but the six-role decomposition is too fine-grained for human coders. Consider collapsing roles. |
| H1 fails | The three death conditions don't capture real-world failure modes. The framework's failure theory needs revision. |
| H2 confirmed, H3 fails | Components map to roles reliably, but stage ordering doesn't predict ablation impact. The pipeline is a useful vocabulary, not a causal model of importance. |
| All fail | The framework is a formal specification with no empirical purchase. It describes what systems COULD have, not what matters when they don't. |

---

## Stopping Rules

Unlike the metacognition experiment (which used Bayesian adaptive stopping on LLM trials), this study has a fixed dataset. The enumeration is complete once all qualifying issues, post-mortems, and ablation rows are identified. There is no adaptive stopping — we code everything that qualifies.

**Minimum viable dataset:**
- Source 1: At least 20 qualifying bug issues across the 5 repos
- Source 2: At least 30 qualifying post-mortems
- Source 3: At least 50 ablation rows across qualifying papers

If any source falls below its minimum, report the underpowered analysis as a limitation, not a failure.

---

## Timeline

1. **Week 1:** Enumerate data sources. Run the GitHub queries, snapshot the post-mortems list, identify qualifying NeurIPS papers.
2. **Week 2:** Recruit and train coders. Finalize rubric based on training examples.
3. **Week 3-4:** Independent coding.
4. **Week 5:** Analysis and reporting.

---

## Transparency

- All coded data published as CSV in this repo.
- All enumeration queries and their raw results committed before coding begins.
- The rubric is immutable after coder training (clarifications only, no substantive changes).
- This preregistration is timestamped by the initial commit. No amendments after data collection begins without a new commit explaining the change and its rationale.

---

*Preregistration designed collaboratively with Claude Opus 4.6 (via Claude Code). Decision criteria pre-registered before any data collection.*
