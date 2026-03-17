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

Single model: GPT-5.4 via codex CLI. Cross-model replication left to future work.

### Phase 1: Data Enumeration

Run all queries mechanically. Commit raw results before any mapping begins. Randomize data point order before Phase 2 — this is the processing order for sequential analysis.

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

3 judge runs per reconstruction. Majority vote.

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

Three questions, tested sequentially with Bayesian updating.

### H1: Does Composition Matter?

NF produces higher reconstruction scores than NF-scrambled.

**Prior:** Uniform. No prior belief about effect direction or size.

**Evidence metric:** Bayes factor (BF₁₀) computed via paired Bayesian sign test after each data point.

**Decision rule:**
- BF₁₀ > 10: **Stop. Strong evidence composition helps.** The Lean proof guarantees something real.
- BF₁₀ < 0.1: **Stop. Strong evidence composition doesn't matter.** The proof is ceremony.
- 0.1 ≤ BF₁₀ ≤ 10: **Continue.** Inconclusive.

**Minimum sample:** 10 data points before first check (posterior needs mass to stabilize).

**Maximum sample:** All qualifying data points. If still inconclusive after exhausting the dataset, report the final BF and posterior.

### H2: Does Contract Text Help?

NF-scrambled produces higher reconstruction scores than NF-bare.

**Same sequential procedure.** Runs in parallel with H1 on the same data points.

- BF₁₀ > 10: Even wrong contracts help.
- BF₁₀ < 0.1: Contract text without correctness is noise.

### H3: Does Composition Help Diagnosis?

NF produces repair suggestions that match actual resolutions better than NF-scrambled.

**Same sequential procedure.** Only runs on Sources 1 and 2 data points that describe failures.

---

## Analysis Plan

```
For each data point d (in pre-randomized order):
  score(d, C) = median fidelity across 3 runs for condition C
  diff(d) = score(d, NF) - score(d, NF_scrambled)

After each data point (n >= 10):
  Compute BF₁₀ for H1 using Bayesian sign test on diff vector
  Compute BF₁₀ for H2 using Bayesian sign test on
    score(NF_scrambled) - score(NF_bare)
  Log BF, n, and posterior to sequential_log.csv
  If BF₁₀ > 10 or BF₁₀ < 0.1 for H1: stop fidelity pipeline
  If both H1 and H2 have stopped: stop all fidelity processing

H3 runs the same procedure on actionability scores,
independent stopping criterion.

Report:
  - Final BF₁₀ and posterior for each hypothesis
  - Sequential plot: BF₁₀ vs n for each hypothesis
  - Effect size (median paired difference) with 95% HDI
```

### Exploratory (labeled as such)

- Per-condition Fleiss' kappa
- Per-source breakdown
- NF vs NF-bare (combines composition + text effects)
- Per-role fidelity scores

---

## Predictions

| Hypothesis | Prediction | Rationale |
|-----------|-----------|-----------|
| H1 (composition) | BF₁₀ > 10 by n ≈ 20-30 | Composable contracts narrow reconstruction. "Filter's postcondition enables Attend's precondition" carries structural information that shuffled contracts don't. |
| H2 (text) | BF₁₀ near 1 | Wrong contracts might help slightly (more text) or hurt (misleading). Unclear direction. |
| H3 (diagnosis) | BF₁₀ > 10 | Correct postcondition violations point to real fixes. Wrong ones point to wrong fixes. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| H1: BF > 10, H2: BF > 10 | **Composition matters, and text helps.** The proof guarantees something real. Even wrong contracts add value over bare labels. |
| H1: BF > 10, H2: BF < 0.1 | **Composition matters, text alone doesn't.** Only correct contracts help. Wrong contracts are noise. The proof is the whole story. |
| H1: BF < 0.1, H2: BF > 10 | **Text helps, composition doesn't.** Any plausible contracts work. The proof is ceremony — more prompt text is what helps. |
| H1: BF < 0.1, H2: BF < 0.1 | **Neither text nor composition helps.** The role labels carry all the information. Contracts are irrelevant. |
| H1: inconclusive after all data | **Effect is small or absent.** Report posterior and move on. |

---

## Budget

Single model, sequential stopping. Worst case (inconclusive, run everything):

| Phase | Per data point | Max data points | Max calls |
|-------|---------------|----------------|-----------|
| Phases 2-4 (fidelity) | 27 calls | ~100 | ~2,700 |
| Phase 5 (actionability) | 9 calls | ~100 | ~900 |
| **Total** | | | **~3,600** |

**Expected case:** If composition effect is large, H1 stops at n ≈ 20-30. That's ~540-810 fidelity calls. H3 (actionability) continues on failure data points only.

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

**Token-count confound (H2 only).** H1 (NF vs NF-scrambled) is length-controlled. H2 (NF-scrambled vs NF-bare) is not. If NF-scrambled > NF-bare, the win might be more tokens.

**Tech-domain bias.** All sources are software/ML. The framework claims broader generality.

**LLM bias.** NF, NF-scrambled, and NF-bare use the same role names and appear equally often in training data. No condition is favored by training data familiarity.

**Home turf.** RL repos have perceive/act/learn loops that map naturally to NF vocabulary. All three conditions share the same vocabulary, so this doesn't bias H1.

**Researcher designed the rubrics.** The treatment is literally text shown to LLMs. Wording choices could favor NF. Mitigation: all rubrics committed verbatim; NF-scrambled uses the exact same text, just reassigned.

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

*Three conditions. One question: does composition matter? NF vs NF-scrambled at the same token count. Bayesian sequential testing — stop when the evidence is clear. That's the Lean test.*
