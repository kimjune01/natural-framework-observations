# Preregistration: Do Behavioral Contracts Improve Decomposition Fidelity?

*Pre-registered before any data collection. Timestamped by commit.*

## Study Information

**Title:** Do Behavioral Contracts Between Pipeline Stages Improve Decomposition Fidelity? A Comparative Study of Eight Information-Processing Lenses

**Authors:** June Kim

**Date:** 2026-03-17

**Framework reference:** [The Natural Framework](https://june.kim/the-natural-framework)

**Formal specification:** [Lean 4 proof](https://github.com/kimjune01/natural-framework) (zero sorry)

---

## Background

Multiple frameworks decompose information-processing systems into named roles. Each compresses a system description into role labels. A high-fidelity lens lets you reconstruct what a component does from its label alone. A low-fidelity lens loses the signal.

Any framework can chain stages into a pipeline — type-compatible composition, expressible in TypeScript. The Natural Framework additionally specifies behavioral contracts: pre/postconditions at each role transition, where each role's postcondition guarantees what the next role's precondition requires. These contracts required a proof assistant (Lean 4) because they make claims about data content, not just data shape.

**Central question:** Do those contracts buy anything empirically? If NF with contracts beats other 6-bin lenses on fidelity, the Lean proof was necessary — the behavioral guarantees carry information that plain labels don't. If NF ties, any six roles with reasonable definitions work equally well, and the proof is valid mathematics but unnecessary engineering. That would be devastating and worth knowing.

To control for granularity (more bins = more bits by construction), we compare lenses at matched bin counts: 5-6 bins. If NF beats other 6-bin lenses, the advantage is the contracts, not the resolution.

---

## Lenses Under Comparison

Eight lenses selected from a [catalog of 80+ frameworks](https://github.com/kimjune01/natural-framework-fidelity/blob/master/lens_catalog.md) enumerated by GPT-5.4 before data collection. Selection criteria: (1) claims to decompose information-processing systems generally, (2) has 5-7 named stages/roles, (3) comes from a distinct intellectual tradition.

| Lens | Bins | Origin | Key distinction |
|------|------|--------|----------------|
| Viable System Model | 5 | Cybernetics | Recursive: each viable system contains viable subsystems |
| MAPE-K | 5 | Autonomic computing | Persistent Knowledge store shared across all stages |
| Intelligence Cycle | 6 | Military/intelligence | Six stages with explicit feedback loop |
| Shannon Communication | 6 | Information theory | Mathematically grounded; no feedback loop |
| Immune Response | 6 | Immunology | Biological pipeline with memory and contraction |
| F3EAD | 6 | Military operations | Operationally oriented; feedback via Disseminate→Find |
| CRISP-DM | 6 | Data science | Cycles back from Evaluation to Business Understanding |
| **Natural Framework** | **6** | **Formal specification** | **Pre/postcondition contracts at each role transition** |

Full role definitions and decision criteria: [`rubric.md`](rubric.md).

Each lens gets its full specification as input to the LLM. NF uniquely specifies pre/postconditions at each role transition (the handshake: each role's postcondition is the next role's precondition). Other lenses do not specify contracts between stages, so they don't get them. This is not an unfair advantage — it's the variable under test.

---

## Data Sources

All pre-specified and mechanically enumerable.

### Source 1: RL Framework Repos

**Repos:** Top 50 RL frameworks by GitHub stars:

```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=50
```

RL repos are the strongest test case: they have explicit perceive/act/learn loops where all six NF roles are architecturally visible. If a lens diagnoses complex pipelines well, simpler systems follow. The reverse doesn't hold.

**Bug issues (Phases 2-5):** Closed, labeled "bug", >= 10 comments, created 2022-2025. From all 50 repos (most qualifying issues will come from the top ~10).

**Predictive validity (Phase 6):** Top 5 repos only (richest commit history).

**Health correlation (Phase 7):** All 50 repos. Sorting by stars gives a natural range of project health — the top repos are thriving, the tail is declining or stalled. Health measured by [Libraries.io SourceRank](https://docs.libraries.io/overview.html#sourcerank), a pre-existing composite score combining stars, contributors, releases, dependents, and maintenance signals. We use SourceRank as-is; modeling project health is not our contribution.

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
> [lens rubric inserted here — full specification per rubric.md, including pre/postconditions for NF]
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
> [If NF: "This role has the following contract — Precondition: [PRE]. Postcondition: [POST]."]
>
> Based only on this label, definition, [and contract if provided,] write one sentence describing what this component most likely does. Be as specific as possible.

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
> [If NF: "This role's postcondition is: [POST]. The failure means this postcondition was violated."]
>
> Based only on this label, definition, [and contract violation if provided,] write one sentence suggesting how to fix the system.

Judge scores the repair against the actual resolution (from the bug report or post-mortem) on the same 1-3 scale.

### Phase 6: Predictive Validity (Source 1 repos only)

For each of the top 5 RL repos, test whether each lens's diagnosis of an early state predicts what actually happened later in the git history.

**Step 1: Snapshot.** Check out the earliest tagged release (or the 25th-percentile commit if no tags exist). Record the hash.

**Step 2: Diagnose.**

**Diagnosis prompt** (used verbatim):

> Here is the source code of an information-processing system at an early stage of development:
>
> [source code inserted here]
>
> Here is a decomposition framework with [N] roles:
>
> [lens rubric inserted here]
>
> 1. For each role, is it present, partially implemented, or missing in this codebase?
> 2. For each missing or partial role, predict what will happen if development continues: what will be added, what bugs will arise, and in what order?
>
> Be specific. Name files, functions, or behaviors.

3 runs × 2 models × 8 lenses = 48 diagnosis calls per repo. Top 5 repos only.

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

For each of the 50 RL repos, diagnose the current codebase (HEAD of default branch).

**Diagnosis prompt** (same as Phase 6 Step 2, applied to current code instead of early snapshot).

Output per lens per repo: count of roles rated as present, partial, or missing. This gives a "role completeness" score (0-N where N = number of bins in the lens).

Correlate role completeness with SourceRank across the 50 repos. The lens whose completeness score correlates most strongly with project health wins.

3 runs × 2 models × 8 lenses = 48 calls per repo. 50 repos = 2,400 calls.

---

## Hypotheses

### H1: Reconstruction Fidelity (primary)

NF with contracts produces higher reconstruction scores than every other lens (which have labels only) at matched bin count.

**Test:** Wilcoxon signed-rank, paired by data point, Holm-Bonferroni corrected. NF vs each 6-bin lens separately; NF vs each 5-bin lens separately.

**Success:** NF mean > every other 6-bin lens, p < 0.05. The contracts carry information that plain labels don't.

**Failure:** Any 6-bin lens >= NF. The contracts are empirically inert — the Lean proof is valid but the behavioral guarantees don't improve description. Any six roles with reasonable definitions work equally well.

### H2: Mapping Consistency

NF produces higher self-consistency across 3 runs of the same model.

**Test:** Fleiss' kappa across 3 runs per model, computed per lens.

**Success:** NF kappa > every other lens for both models. The contracts reduce ambiguity in role assignment.

**Failure:** Any 6-bin lens > NF for both models. The pre/postconditions create confusion rather than clarity.

### H3: Diagnostic Actionability

For system failures, NF's contract-aware role labels produce repair suggestions that match actual resolutions better than other lenses.

**Test:** Wilcoxon signed-rank, paired by failure, Holm-Bonferroni corrected.

**Success:** NF mean > every other lens, p < 0.05. Knowing which postcondition was violated points to the fix.

**Failure:** No significant differences. Contracts don't help repair.

### H4: Predictive Validity

NF diagnoses of early-stage codebases produce predictions that match subsequent git history better than other lenses.

**Test:** Wilcoxon signed-rank, paired by repo × prediction, Holm-Bonferroni corrected.

**Success:** NF mean prediction score > every other lens, p < 0.05. The ordering constraints (each role's postcondition enables the next) predict the sequence of development.

**Failure:** Any other lens >= NF. The contracts don't predict what gets built next.

**Note:** This is where the contracts are most directly tested. Fidelity (H1) measures whether contracts help describe what exists. Predictive validity measures whether the handshake — each postcondition enabling the next precondition — tells you what's coming. A lens without contracts can say "this role is missing." Only NF can say "this role is missing, therefore the downstream roles are starved."

### H5: Health Correlation

NF role completeness correlates more strongly with project health (SourceRank) than role completeness under any other lens.

**Test:** Spearman's rho between role completeness and SourceRank, computed per lens. Compare correlation coefficients across lenses using Steiger's test for dependent correlations.

**Success:** NF rho > every other lens's rho, p < 0.05.

**Failure:** Another lens's completeness correlates equally or more strongly.

**Power note:** n=50 repos detects Spearman's rho >= 0.28 at p < 0.05. If the true effect is smaller, this test is underpowered. Report the confidence interval regardless.

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

├─ NF > all seven others at p < 0.05 (Holm-Bonferroni)?
│   └─ CONFIRMED: contracts improve fidelity
├─ NF > all 6-bin lenses but not all 5-bin lenses?
│   └─ PARTIAL: contracts help at matched granularity
├─ Another 6-bin lens >= NF?
│   └─ DISCONFIRMED: contracts don't improve fidelity
└─ Report full pairwise matrix with effect sizes
```

### H2 Analysis

```
For each lens L and each model M:
  kappa(L, M) = Fleiss' kappa across 3 mapping runs

├─ NF kappa highest for both models?
│   └─ CONFIRMED: contracts reduce role ambiguity
├─ NF kappa highest for one model only?
│   └─ MODEL-DEPENDENT: consistency depends on the mapper
├─ Another 6-bin lens > NF for both models?
│   └─ DISCONFIRMED: contracts increase ambiguity
└─ Report per-role kappa to identify hardest NF boundaries
```

### H3 Analysis

```
For each failure f and each lens L:
  actionability(f, L) = median score across runs × judge models

For each lens pair (NF, other):
  W, p = wilcoxon_signed_rank(action_NF, action_other)

├─ NF > all others at p < 0.05?
│   └─ CONFIRMED: contract violations point to fixes
├─ No significant differences?
│   └─ DISCONFIRMED: contracts don't help repair
└─ Report all pairwise comparisons
```

### H4 Analysis

```
For each repo r and each lens L:
  predictions(r, L) = list of scored predictions from Phase 6
  mean_score(L) = mean across all repos and predictions

For each lens pair (NF, other):
  W, p = wilcoxon_signed_rank(scores_NF, scores_other)

├─ NF > all others at p < 0.05?
│   └─ CONFIRMED: handshake predicts development sequence
├─ Another lens > NF?
│   └─ DISCONFIRMED: ordering constraints don't predict
└─ Report per-repo results to check consistency
```

### H5 Analysis

```
For each lens L:
  completeness(repo, L) = mean roles rated "present" across 3 runs × 2 models
  rho(L) = spearman(completeness across 50 repos, SourceRank across 50 repos)

For each lens pair (NF, other):
  z, p = steiger_test(rho_NF, rho_other, n=50)

├─ NF rho > all others at p < 0.05?
│   └─ CONFIRMED: NF completeness tracks project health best
├─ Multiple lenses tied?
│   └─ PARTIAL: role completeness predicts health, but not uniquely via NF
├─ No lens correlates significantly?
│   └─ NULL: role completeness doesn't predict health
└─ Report all rho values with 95% CI
```

### Multiple Comparisons

Five hypotheses × seven pairwise tests = 35 primary tests (H1-H4), plus 7 Steiger tests (H5) = 42 total. Holm-Bonferroni correction. Report both corrected and uncorrected p-values.

### Cross-Model Agreement

For each lens, compare GPT-5.4 and Sonnet 4.5 mappings. If the two models disagree systematically by lens, the result is model-dependent. Report as a limitation.

### Exploratory Analyses (labeled as such)

- Which NF role boundaries cause the most inconsistency? (per-role kappa)
- Does fidelity vary by data source? (bug issues vs post-mortems vs ablations)
- Do any lenses cluster? (if two lenses always agree, they're the same lens in practice)
- Which lens produces the most "unmapped" cases?
- Role correspondence matrix: which roles across lenses map to the same components?

---

## Predictions

| Hypothesis | Predicted ranking (best → worst) | Rationale |
|-----------|----------------------------------|-----------|
| H1 (fidelity) | NF > Immune > Intel Cycle > F3EAD > Shannon > CRISP-DM > MAPE-K > VSM | NF's contracts distinguish Cache/Remember and Filter/Attend at the behavioral level. Immune has biological memory but no formal contracts. Shannon has no feedback. |
| H2 (consistency) | Shannon > CRISP-DM > NF > Intel Cycle > Immune > F3EAD > MAPE-K > VSM | Shannon's roles are mathematically crisp. NF's contracts should help, but the Filter/Attend boundary will still cause splits. VSM's System 3 vs 4 is notoriously ambiguous. |
| H3 (actionability) | NF > MAPE-K > Intel Cycle > F3EAD > Immune > CRISP-DM > Shannon > VSM | "Filter's postcondition is violated — items that should be rejected are reaching Attend" points to the fix. "Channel has noise" does not. |
| H4 (prediction) | NF > MAPE-K > Immune > Intel Cycle > CRISP-DM > F3EAD > Shannon > VSM | The handshake predicts ordering: earlier stages get built first because downstream stages can't fire without their preconditions met. No other lens makes this claim. |
| H5 (health) | NF > MAPE-K > VSM > Intel Cycle > Immune > CRISP-DM > F3EAD > Shannon | NF and MAPE-K roles map to implementation concerns that affect health. VSM was designed to diagnose organizational viability. |

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF wins H1-H4 | Contracts are load-bearing. The Lean proof was necessary. Ship the framework. |
| NF wins H4 but ties H1 | The handshake predicts, but the labels don't describe better than alternatives. The ordering theory is load-bearing; the vocabulary is not. The proof was necessary for the wrong reason. |
| NF ties all 6-bin lenses on H1 | **The devastating result.** Contracts are empirically inert. Any six roles with reasonable definitions work equally well. The Lean proof is valid mathematics and unnecessary engineering. |
| Shannon beats NF on H2 but loses H1 | Mathematical crispness aids consistency but not fidelity. Crisp categories can be consistently wrong. |
| MAPE-K beats NF on H3 | Engineering-native vocabulary is more actionable than formal contracts. Practitioners need familiar terms, not postconditions. |
| Immune ties NF on H1 | The biological decomposition is equally high-fidelity. NF's contracts may have been reverse-engineered from biology. |
| All 6-bin lenses cluster on H1, but NF separates on H4 | The best possible outcome for the theory. Six bins is the right number (any vocabulary works for description), but the contracts predict what labels can't. |
| NF loses all five | The six roles are a formal specification with no practical advantage. |

---

## Budget

No human coders. Each trial is a CLI call.

| Phase | Per unit | Units | Calls |
|-------|----------|-------|-------|
| Phases 2-5 | ~160 calls/data point | ~100 data points | ~16,000 |
| Phase 6 | ~96 calls/repo | 5 repos | ~480 |
| Phase 7 | ~48 calls/repo | 50 repos | ~2,400 |
| **Total** | | | **~18,900** |

At ~10s per call: ~53 hours wall clock. Parallelizable across models and lenses.

---

## Stopping Rules

Fixed dataset. Map everything that qualifies. No adaptive stopping.

**Minimum viable dataset:**
- Source 1 (bugs): >= 20 qualifying bug issues across all 50 repos
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
├── diagnoses/            # {repo}_{lens}_{model}_{run}_diag.json (Phases 6-7)
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

**Survivorship bias (H4, partially addressed by H5).** H4 uses the top 5 RL repos — projects that survived. H5 partially addresses this by expanding to 50 repos sorted by stars, which naturally spans a range of project health (thriving to declining). However, the sample is still biased toward repos with enough stars to appear in the top 50. Projects that died before gaining any traction are absent. The framework's strongest claim (breaking a role compounds to extinction) would require a prospective study tracking projects from inception.

**Tech-domain bias (all hypotheses).** All three sources are software/ML. The framework claims to apply to comedy, immune systems, law, and evolution. This study tests tech systems only. A positive result does not validate cross-domain generality; a negative result does not rule it out.

**LLM bias (all hypotheses).** Both mapper models were trained on text describing all eight lenses. If one lens appears more in training data, the model may apply it more fluently — reflecting training distribution, not lens quality. Cross-model comparison partially controls for this.

**Rubric asymmetry.** NF gets pre/postconditions; other lenses don't. This is intentional — each lens gets its full specification, and only NF specifies contracts. But it means NF's rubric contains more information by design. If NF wins, the question is whether the contracts *deserve* to carry that information or whether any lens would improve with invented contracts. This is addressed by the study design: we test each lens as-is, not each lens enhanced with contracts.

---

## Transparency

- All prompts committed verbatim before Phase 2 begins.
- Lens anonymization key committed in a sealed file, opened only during analysis.
- All raw data published as JSON/CSV.
- This preregistration is timestamped by commit. No amendments after Phase 2 begins without a dated commit explaining the change.

---

*Preregistration designed collaboratively with Claude Opus 4.6 (via Claude Code) and GPT-5.4 (via Codex CLI, lens enumeration). Eight competing lenses selected from a catalog of 80+ frameworks to ensure fair comparison at matched granularity.*
