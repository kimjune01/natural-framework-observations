# Preregistration: Comparative Fidelity of Information-Processing Lenses

*Pre-registered before any data collection. Timestamped by commit.*

## Study Information

**Title:** Comparative Fidelity of Four Information-Processing Decompositions Applied to Public System Failures and ML Ablations

**Authors:** June Kim

**Date:** 2026-03-17

**Framework reference:** [The Natural Framework](https://june.kim/the-natural-framework)

**Formal specification:** [Lean 4 proof](https://github.com/kimjune01/natural-framework) (zero sorry)

---

## Background

Multiple frameworks decompose information-processing systems into named roles or stages. The Natural Framework claims six roles (Perceive, Cache, Filter, Attend, Remember, Consolidate). OODA claims four (Observe, Orient, Decide, Act). The Intelligence Cycle claims six (Direction, Collection, Processing, Analysis, Dissemination, Feedback). IPO claims three (Input, Process, Output).

Each is a lens. Each compresses a system description into role labels. The question is not whether any lens "fits" — any taxonomy with enough bins will achieve high coverage. The question is which lens preserves the most information through the compression. That is fidelity.

A high-fidelity lens lets you reconstruct what a component does from its label alone. "Process" (IPO) tells you nothing. "Orient" (OODA) tells you a little. "Filter" (Natural Framework) tells you it gates. The label carries more bits. That difference is measurable.

---

## Design

A comparative fidelity study. The same data is mapped through four lenses by independent coders. Fidelity is measured by reconstruction accuracy: given only the role label, can a second coder recover what the component does?

---

## Lenses Under Comparison

Four lenses, selected to span granularity (3-6 bins) and origin (military, engineering, intelligence, cognitive science). Selection criteria: the lens must (1) claim to decompose information-processing systems generally, not just one domain, and (2) have named stages/roles that can serve as labels.

### IPO (3 bins) — baseline

| Role | Definition |
|------|-----------|
| **Input** | Data enters the system |
| **Process** | Data is transformed |
| **Output** | Results leave the system |

Source: general systems theory. The minimal decomposition. If a finer-grained lens doesn't beat IPO, its extra categories add noise, not signal.

### OODA (4 bins)

| Role | Definition |
|------|-----------|
| **Observe** | Gather information from the environment |
| **Orient** | Analyze and synthesize observations into a mental model |
| **Decide** | Select a course of action |
| **Act** | Execute the decision |

Source: [Boyd (1987)](https://en.wikipedia.org/wiki/OODA_loop), military strategy. Widely applied beyond military contexts.

### Intelligence Cycle (6 bins)

| Role | Definition |
|------|-----------|
| **Direction** | Define information requirements |
| **Collection** | Gather raw data from sources |
| **Processing** | Convert raw data into usable format |
| **Analysis** | Evaluate processed data, produce assessments |
| **Dissemination** | Distribute finished intelligence to consumers |
| **Feedback** | Consumers inform the next cycle's direction |

Source: [intelligence community](https://en.wikipedia.org/wiki/Intelligence_cycle). Six stages with a feedback loop — same bin count as the Natural Framework.

### Natural Framework (6 bins)

| Role | Definition |
|------|-----------|
| **Perceive** | Ingest external input, convert to internal representation |
| **Cache** | Temporarily hold data between processing steps |
| **Filter** | Gate, threshold, or reject data that fails a criterion |
| **Attend** | Rank, score, or select among items that passed filtering |
| **Remember** | Write processed data to persistent storage |
| **Consolidate** | Read past outcomes from storage, update system parameters |

Source: [The Natural Framework](https://june.kim/the-natural-framework). Six roles with a backward pass (Consolidate).

---

## Data Sources

Same three sources as the original design. All pre-specified and mechanically enumerable.

### Source 1: RL Framework Bug Issues

**Repos:** Top 5 reinforcement learning frameworks by GitHub stars:

```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=5
```

**Issues:** All closed issues labeled "bug" with >= 10 comments, created 2022-2025.

**Enumeration query per repo:**

```
gh issue list -R {owner}/{repo} --state=closed --label=bug --json number,title,comments,createdAt \
  | jq '[.[] | select(.comments >= 10) | select(.createdAt >= "2022-01-01" and .createdAt <= "2025-12-31")]'
```

### Source 2: Public Post-Mortems

**Source:** All entries on [github.com/danluu/post-mortems](https://github.com/danluu/post-mortems) at a snapshot commit hash (recorded in `DATA_SOURCES.md`).

**Inclusion:** Software system failures only.

**Exclusion:** Hardware-only root cause, natural disaster, physical security breach.

### Source 3: ML Ablation Tables

**Papers:** NeurIPS 2024 oral/spotlight papers containing "ablation" in the text.

**Rows:** Every row that removes or disables a named component. Exclude hyperparameter changes and additions.

---

## Hypotheses

### H1: Mapping Fidelity (primary)

The Natural Framework produces higher reconstruction accuracy than the other three lenses.

**Procedure:**

1. Coder A reads a component description (from bug issues, post-mortems, or ablation tables) and assigns a role label under each of the four lenses.
2. Coder B sees only the role label and the lens name. They write a one-sentence prediction of what the component does.
3. A third judge scores reconstruction accuracy: how much of the original description is recoverable from the label alone.

**Scoring (per data point, per lens):**

| Score | Criterion |
|-------|----------|
| 3 | Reconstruction identifies the component's specific function (e.g., "it rejects low-quality input") |
| 2 | Reconstruction identifies the general category but not the specific function (e.g., "it transforms data somehow") |
| 1 | Reconstruction is too vague to distinguish from other components (e.g., "it processes things") |

**Success criterion:** Natural Framework mean reconstruction score > every other lens's mean score, with paired difference significant at p < 0.05 (Wilcoxon signed-rank, paired by data point).

**Failure criterion:** Natural Framework mean score <= IPO mean score. If a 6-bin lens can't beat a 3-bin baseline, the extra categories are noise.

**Partial support:** Natural Framework > IPO and OODA but not > Intelligence Cycle. The six bins help, but it's the granularity, not the specific decomposition.

### H2: Inter-Rater Agreement

The Natural Framework produces higher inter-rater agreement than the other three lenses.

**Procedure:** Two coders independently map the same components under all four lenses. Compute Cohen's kappa per lens.

**Success criterion:** Natural Framework kappa > every other lens's kappa.

**Failure criterion:** Natural Framework kappa < IPO kappa. If coders agree more on 3 vague bins than 6 specific ones, the specificity creates ambiguity rather than resolving it.

**Note:** Higher agreement alone doesn't prove fidelity. IPO might achieve perfect agreement (everything is "Process") with zero fidelity. H1 and H2 must be evaluated together.

### H3: Diagnostic Specificity

For system failures (Sources 1 and 2), the Natural Framework's role labels produce more actionable repair suggestions than the other lenses.

**Procedure:**

1. For each failure, Coder A assigns a role label under each lens.
2. Coder B sees only the label and writes a one-sentence repair suggestion.
3. A judge scores actionability against the actual resolution documented in the bug report or post-mortem.

**Scoring (per failure, per lens):**

| Score | Criterion |
|-------|----------|
| 3 | Repair suggestion matches the actual resolution in substance |
| 2 | Repair suggestion is in the right area but not specific enough to act on |
| 1 | Repair suggestion is generic or wrong |

**Success criterion:** Natural Framework mean actionability > every other lens, p < 0.05 (Wilcoxon signed-rank).

**Failure criterion:** No significant difference between any lenses. The decomposition doesn't matter for diagnosis.

---

## Coding Procedure

### Coders

Three coders total:
- **Coder A:** Maps components to roles under all four lenses. Must be unfamiliar with the Natural Framework.
- **Coder B:** Reconstructs component descriptions from role labels only. Must be unfamiliar with both the data and the framework.
- **Judge:** Scores reconstruction accuracy and actionability against ground truth. Blind to which lens produced which label.

An additional **Coder A'** independently maps the same components for the inter-rater agreement analysis (H2).

### Rubrics

Each lens gets its own rubric page (see `rubric.md`). The Natural Framework rubric uses the existing decision tree. The other three rubrics use equivalent decision trees constructed from their published definitions.

**Critical constraint:** The rubrics must be equivalently detailed. If the Natural Framework rubric is more specific than the OODA rubric, any fidelity advantage could come from rubric quality rather than lens quality. All four rubrics must have: (1) a decision tree of the same depth, (2) boundary-case guidance, and (3) training examples.

### Training

All coders practice on 5 shared examples before real coding. Training examples are the same across all four lenses. Clarifications to any rubric are committed before coding begins.

### Blinding

- Coder A does not know which lens is being tested (they map under all four for every data point).
- Coder B does not know which lens produced a given label (labels are anonymized as "Lens 1", "Lens 2", etc., randomized per data point).
- The judge does not know which lens is being evaluated.
- Lens identity is revealed only during analysis.

---

## Analysis Plan

### H1 Analysis

```
For each data point d and each lens L:
  reconstruction_score(d, L) ∈ {1, 2, 3}

For each lens pair (NF, other):
  W, p = wilcoxon_signed_rank(scores_NF, scores_other)

├─ NF > all three others at p < 0.05?
│   └─ CONFIRMED: Natural Framework is highest-fidelity lens
├─ NF > IPO and OODA but not > Intelligence Cycle?
│   └─ PARTIAL: six bins help, but NF's specific decomposition is not uniquely better
├─ NF ≤ IPO?
│   └─ DISCONFIRMED: extra categories add noise
└─ Mixed results?
    └─ Report all pairwise comparisons with effect sizes
```

### H2 Analysis

```
For each lens L:
  kappa(L) = Cohen's kappa between Coder A and Coder A'

├─ kappa(NF) > kappa(all others)?
│   └─ CONFIRMED: NF categories are clearest to apply
├─ kappa(NF) < kappa(IPO)?
│   └─ DISCONFIRMED: NF specificity creates ambiguity
└─ Report per-role kappa for NF to identify which boundaries are hardest
```

### H3 Analysis

```
For each failure f and each lens L:
  actionability_score(f, L) ∈ {1, 2, 3}

For each lens pair (NF, other):
  W, p = wilcoxon_signed_rank(action_NF, action_other)

├─ NF > all three others at p < 0.05?
│   └─ CONFIRMED: NF produces more actionable diagnoses
├─ No significant differences?
│   └─ DISCONFIRMED: decomposition doesn't matter for repair
└─ Report all pairwise comparisons
```

### Multiple Comparisons

Three hypotheses, three pairwise tests each = 9 tests. Apply Holm-Bonferroni correction. Report both corrected and uncorrected p-values.

### Exploratory Analyses (labeled as such)

- Which NF role boundaries produce the most disagreement? (per-role kappa)
- Does fidelity vary by data source? (bug issues vs post-mortems vs ablations)
- Does the Intelligence Cycle's "Feedback" map to NF's "Consolidate"? (role correspondence)
- Which lens produces the most "unmapped" cases?

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (fidelity) | NF > Intelligence Cycle > OODA > IPO | NF distinguishes Cache from Remember and Filter from Attend. The Intelligence Cycle collapses these. OODA lumps even further. IPO is barely a lens. |
| H2 (agreement) | Intelligence Cycle > NF > OODA > IPO | NF's Filter/Attend boundary will cause disagreements. The Intelligence Cycle's stages are more familiar from everyday vocabulary. IPO is trivially high agreement, low information. |
| H3 (actionability) | NF > OODA > Intelligence Cycle > IPO | "Filter is broken" tells you to fix the gate. "Orient is broken" tells you to fix... your mental model? NF's roles are closer to implementation. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF wins H1, H2, H3 | NF is the best available lens for system diagnosis. Ship it. |
| NF wins H1 and H3, loses H2 | NF is high-fidelity but hard to apply consistently. The rubric needs work, or the role boundaries need sharpening. |
| Intelligence Cycle ties NF on H1 | The specific decomposition matters less than having six bins with a feedback loop. The NF's value is the proof, not the vocabulary. |
| IPO ties everyone on H3 | Decomposition granularity doesn't improve diagnosis. The lens doesn't matter; the coder's expertise does. |
| NF loses H1 to Intelligence Cycle | A domain-specific decomposition (intelligence) beats a domain-general one (NF) on the data sources we chose. Try different domains. |
| NF loses all three | The six roles are a formal specification with no practical advantage over existing lenses. The math is valid; the vocabulary is not uniquely useful. |

---

## Stopping Rules

Fixed dataset. Code everything that qualifies. No adaptive stopping.

**Minimum viable dataset:**
- Source 1: At least 20 qualifying bug issues
- Source 2: At least 30 qualifying post-mortems
- Source 3: At least 50 ablation rows

If any source falls below its minimum, report the underpowered analysis as a limitation.

---

## Timeline

1. **Week 1:** Enumerate data sources. Write all four rubrics. Verify equivalent detail across rubrics.
2. **Week 2:** Recruit and train coders. Practice on shared examples.
3. **Week 3-4:** Independent coding (Coder A, Coder A' map all data under all four lenses).
4. **Week 5:** Reconstruction task (Coder B). Judging.
5. **Week 6:** Analysis and reporting.

---

## Transparency

- All coded data published as CSV.
- All four rubrics committed before coding begins.
- Lens anonymization key committed in a sealed file, opened only during analysis.
- All enumeration queries and raw results committed before coding begins.
- This preregistration is timestamped by commit. No amendments after data collection begins without a dated commit explaining the change.

---

*Preregistration designed collaboratively with Claude Opus 4.6 (via Claude Code) and GPT-5.4 (via Codex CLI, lens enumeration). Competing lenses selected from a catalog of 80+ frameworks to ensure fair comparison.*
