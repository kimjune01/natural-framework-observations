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

This study asks whether that composition carries information. NF's six roles can be stated three ways: with correct contracts (Lean-proven), with wrong contracts (same text, shuffled), or with no contracts (labels only). If correct contracts outperform scrambled ones at the same token count, the Lean proof is load-bearing. If scrambled contracts outperform bare labels, even wrong contract text helps. If all three perform equally, the role names carry all the information and the proof is ceremony.

This is an LLM-evaluation study. It tests whether contract text improves LLM-mediated decomposition of software artifacts, not whether formal proofs are necessary in general.

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

**H2 comparison (NF-scrambled vs NF-bare):** Same roles, same definitions. NF-scrambled has more text. Not length-controlled — if NF-scrambled wins, it might be more tokens.

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
> [condition rubric inserted here]
>
> 1. Which role does this component best fit? Pick exactly one, or say "unmapped" if none fit.
> 2. In one sentence, what does a component with this role label typically do?

3 runs × 2 models × 3 conditions = 18 mapping calls per data point.

**Token counts:** Record and report token counts for each condition's rubric. NF and NF-scrambled must be within 5% of each other.

**Unmapped items:** Fidelity score = 0. Excluded from actionability. Included in fidelity analysis.

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

3 runs per judge model, majority vote. GPT-5.4 and Sonnet 4.5 judge independently.

**Human validation subset:** 20 data points, stratified random sample (balanced across sources). Two human raters score the same reconstructions on the same 1-3 rubric, blind to condition. Report human-human Cohen's κ and human-LLM agreement. If human-LLM κ < 0.4, results are suspect.

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

---

## Hypotheses

One primary hypothesis, two secondary.

### H1: Does Composition Matter? (primary)

NF produces higher reconstruction scores than NF-scrambled.

**Test:** Wilcoxon signed-rank, paired by data point. α = 0.05.

**Success:** NF > NF-scrambled, p < 0.05. Composable contracts carry more information than shuffled ones. The Lean proof guarantees something real.

**Failure:** NF-scrambled >= NF. Composition doesn't matter. Any plausible-sounding contracts work equally well.

### H2: Does Contract Text Help? (secondary)

NF-scrambled produces higher reconstruction scores than NF-bare.

**Test:** Wilcoxon signed-rank, paired by data point. Holm-Bonferroni corrected (2 secondary tests, α_adj = 0.025).

**Success:** NF-scrambled > NF-bare, adjusted p < 0.025. Even wrong contracts help — extra descriptive text carries information.

**Failure:** NF-bare >= NF-scrambled. Contract text without correctness is noise.

### H3: Does Composition Help Diagnosis? (secondary)

NF produces repair suggestions that match actual resolutions better than NF-scrambled.

**Test:** Wilcoxon signed-rank, paired by failure. Holm-Bonferroni corrected (α_adj = 0.025).

**Success:** NF > NF-scrambled, adjusted p < 0.025. Correct contract violations point to fixes.

**Failure:** NF-scrambled >= NF. Any contract violation framing works.

---

## Analysis Plan

```
Unit of analysis: the data point. Each data point gets one score per
condition = median across 3 runs × 2 models. N = unique data points.

Primary (α = 0.05):
  H1: W, p = wilcoxon_signed_rank(fidelity_NF, fidelity_NF_scrambled)

Secondary (Holm-Bonferroni, 2 tests):
  H2: W, p = wilcoxon_signed_rank(fidelity_NF_scrambled, fidelity_NF_bare)
  H3: W, p = wilcoxon_signed_rank(action_NF, action_NF_scrambled)

Report effect sizes (rank-biserial r) and 95% CIs for all.
```

### Cross-Model Agreement

Compare GPT-5.4 and Sonnet 4.5 results per condition. If the models disagree on H1, the result is model-dependent.

### Exploratory (labeled as such)

- Per-condition Fleiss' kappa
- Per-source breakdown
- NF vs NF-bare (combines composition + text effects)
- Per-role fidelity scores

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (composition) | NF > NF-scrambled | Composable contracts narrow reconstruction. "Filter's postcondition enables Attend's precondition" carries structural information that shuffled contracts don't. |
| H2 (text) | NF-scrambled > NF-bare | Even wrong contracts add descriptive text. But if NF-scrambled ≈ NF-bare, contract text is noise without correctness. |
| H3 (diagnosis) | NF > NF-scrambled | Correct postcondition violations point to real fixes. Wrong ones point to wrong fixes. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF > NF-scrambled > NF-bare | **Composition matters, and text helps.** The proof guarantees something real. Even wrong contracts add value over bare labels. |
| NF > NF-scrambled ≈ NF-bare | **Composition matters, text alone doesn't.** Only correct contracts help. Wrong contracts are noise. The proof is the whole story. |
| NF ≈ NF-scrambled > NF-bare | **Text helps, composition doesn't.** Any plausible contracts work. The proof is ceremony — more prompt text is what helps. |
| NF ≈ NF-scrambled ≈ NF-bare | **Neither text nor composition helps.** The role labels carry all the information. Contracts are irrelevant. |

---

## Budget

Three conditions, ~5,400 CLI calls.

| Phase | Per unit | Units | Calls |
|-------|----------|-------|-------|
| Phases 2-4 | ~45 calls/data point | ~100 data points | ~4,500 |
| Phase 5 | ~9 calls/failure | ~100 failures | ~900 |
| **Total** | | | **~5,400** |

At ~10s per call: ~15 hours wall clock. Parallelizable across models and conditions.

---

## Stopping Rules

Fixed dataset. Map everything that qualifies. No adaptive stopping.

**Minimum viable dataset:**
- Source 1 (bugs): >= 20 qualifying bug issues
- Source 2: >= 30 qualifying post-mortems
- Source 3: >= 50 ablation rows

---

## Logging

```
data/
├── enumeration/          # Raw query results, committed before mapping
├── mappings/             # {datapoint}_{condition}_{model}_{run}.json
├── reconstructions/      # {datapoint}_{condition}_{model}_{run}_recon.json
├── judgments/            # {datapoint}_{condition}_{model}_{run}_judge{1,2,3}.json
├── repairs/              # {datapoint}_{condition}_{model}_{run}_repair.json
├── scores/               # Aggregated scores per data point per condition
└── analysis/             # Final results, plots
```

### Commit policy

- Enumeration: committed before any mapping begins.
- Mappings: committed after each condition × model batch.
- Judgments: committed after each batch.
- Append-only. No data is overwritten.

---

## Limitations

**This is an LLM-evaluation study.** It tests whether contract text improves LLM-mediated decomposition of software artifacts. It does not test whether Lean proofs are necessary for software engineering in general.

**Token-count confound (H2 only).** H1 (NF vs NF-scrambled) is length-controlled. H2 (NF-scrambled vs NF-bare) is not. If NF-scrambled > NF-bare, the win might be more tokens.

**Tech-domain bias.** All sources are software/ML. The framework claims broader generality.

**LLM bias.** NF, NF-scrambled, and NF-bare use the same role names and appear equally often in training data. No condition is favored by training data familiarity.

**Home turf.** RL repos have perceive/act/learn loops that map naturally to NF vocabulary. All three conditions share the same vocabulary, so this doesn't bias H1.

**Researcher designed the rubrics.** The treatment is literally text shown to LLMs. Wording choices could favor NF. Mitigation: all rubrics committed verbatim; NF-scrambled uses the exact same text, just reassigned.

---

## Transparency

- All prompts committed verbatim before Phase 2.
- Condition labels anonymized during judging ("Lens A"/"Lens B"/"Lens C").
- All raw data published as JSON/CSV.
- Timestamped by commit. No amendments after Phase 2 without a dated commit.

---

*Three conditions. One question: does composition matter? NF vs NF-scrambled at the same token count. That's the Lean test.*
