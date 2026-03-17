# Preregistration: Physics and Chemistry of Information Processing (SUPERSEDED)

*This document has been split into two focused studies:*
- *[Study 1: The Lean Test](STUDY1-LEAN-TEST.md) — does contract composition carry information?*
- *[Study 2: Physics vs Chemistry](STUDY2-PHYSICS-CHEMISTRY.md) — which level of description fits the instruments we have?*

*Kept as history. The split was driven by codex review: the combined design had too many conditions, too many hypotheses, and two distinct questions fighting for the same document.*

---

*Original document below (pre-registered before any data collection, timestamped by commit).*

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

**Central question:** Do correct behavioral contracts help? NF's six roles can be stated with correct pre/postconditions, wrong pre/postconditions, or none at all. This study compares four conditions — Shannon (physics-level contracts), NF with correct contracts (Lean-proven), NF with scrambled contracts (same text, wrong assignment), and NF with no contracts (labels only) — to isolate whether contracts help, and if so, whether correctness matters.

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

### NF-scrambled (wrong contracts)

| Role | Definition | Precondition | Postcondition |
|------|-----------|-------------|--------------|
| **Perceive** | Ingest external input, convert to internal format | Items ranked by priority | Data is buffered, retrievable |
| **Cache** | Temporarily hold data between steps | Store contains past outcomes | Failing items rejected; passing items remain |
| **Filter** | Gate, threshold, or reject by criterion | Data is in internal format | Parameters updated; next cycle reflects past |
| **Attend** | Rank, score, or select among items | Data persisted, retrievable across cycles | External data exists |
| **Remember** | Write to persistent storage | Failing items rejected; passing items remain | Data is in internal format |
| **Consolidate** | Read past outcomes, update parameters | Data is buffered, retrievable | Items ranked by priority |

Same six roles, same definitions, same number of pre/postconditions as NF. **The contracts are shuffled** — each role gets another role's conditions. They don't compose (Perceive's postcondition doesn't enable Cache's precondition). Same token count as NF. This is NF without the Lean proof's guarantee.

### NF-bare (labels only)

| Role | Definition |
|------|-----------|
| **Perceive** | Ingest external input, convert to internal format |
| **Cache** | Temporarily hold data between steps |
| **Filter** | Gate, threshold, or reject by criterion |
| **Attend** | Rank, score, or select among items |
| **Remember** | Write to persistent storage |
| **Consolidate** | Read past outcomes, update parameters |

Same six roles and definitions as NF. **No pre/postconditions.** This is NF as TypeScript interfaces — the role vocabulary without any contracts.

### Controlled Comparison

| Dimension | Shannon | NF | NF-scrambled | NF-bare |
|-----------|---------|-----|-------------|---------|
| Role count | 6 | 6 | 6 | 6 |
| Role definitions | Communication-theoretic | Behavioral | Behavioral (same) | Behavioral (same) |
| Formal contracts | Mathematical theorems | Lean 4 pre/postconditions | Shuffled pre/postconditions | **None** |
| Contract correctness | N/A (theorems) | Proven composable | **Not composable** | — |
| Token count | ~NF | Full | ~same as NF | Shorter |
| Stage ordering | No inherent order | Pipeline (handshake) | Pipeline (broken handshake) | Pipeline (implied) |
| Origin domain | Communication engineering | Cognition / software | Cognition / software | Cognition / software |

**Delta 1 (NF vs NF-scrambled):** Does correctness matter? Same roles, same definitions, same token count. The only difference: NF's contracts compose (each postcondition enables the next precondition); NF-scrambled's don't. This is the Lean test. If NF > NF-scrambled, the proof is load-bearing.

**Delta 2 (NF vs NF-bare):** Does contract text help at all? If NF-scrambled ≈ NF-bare, the contract text is noise — only correct contracts help. If NF-scrambled > NF-bare, even wrong contracts help (more tokens = more information, regardless of correctness).

**Delta 3 (NF vs Shannon):** Which level of description? Different roles, different contract type, different origin. Context only — all confounds intact.

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

RL repos have explicit perceive/act/learn loops where NF roles are architecturally visible. This is closer to NF's home turf than Shannon's — NF's vocabulary (perceive, filter, remember) maps more naturally to RL components than Shannon's (source, encoder, channel). This biases H2 but not H1, since NF and NF-bare share the same vocabulary.

**Bug issues (Phases 2-5):** Closed, labeled "bug", >= 10 comments, created 2022-2025.

**Predictive validity (Phase 6):** Top 5 repos only (richest commit history).

**Health correlation (Phase 7):** All 50 repos. Ecosystem maturity measured by [Libraries.io SourceRank](https://docs.libraries.io/overview.html#sourcerank) (a composite of activity, dependencies, contributors — a proxy for project maturity, not a direct health measure).

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
> [condition rubric inserted here — full specification for Shannon, NF, and NF-scrambled; definitions only for NF-bare]
>
> 1. Which role does this component best fit? Pick exactly one, or say "unmapped" if none fit.
> 2. In one sentence, what does a component with this role label typically do?

3 runs × 2 models × 4 conditions = 24 mapping calls per data point.

**Token counts:** Record the token count of each condition's rubric insert. NF and NF-scrambled have ~equal token counts (same number of pre/postconditions). NF-bare is shorter. Report all differences. The NF vs NF-scrambled comparison controls for prompt length; the NF vs NF-bare comparison does not.

**Unmapped items:** If the mapper returns "unmapped," the item receives a fidelity score of 0 (no information preserved). Unmapped items are excluded from actionability (Phase 5) and predictive (Phase 6) analyses but included in fidelity (Phases 2-4) — a lens that can't map a component preserves zero information about it.

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

3 runs per judge model, majority vote. GPT-5.4 and Sonnet 4.5 judge every reconstruction independently.

**Human validation subset:** 20 data points selected by stratified random sample (balanced across sources). Two human raters score the same reconstructions on the same 1-3 rubric, blind to condition. Report human-human Cohen's κ and human-LLM agreement. If human-LLM κ < 0.4, the LLM judge is unreliable and results are suspect.

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

3 runs × 2 models × 4 conditions = 24 diagnosis calls per repo.

**Step 3: Check against history.**

**History judge prompt** (used verbatim):

> Here is a prediction made about a software project based on its early source code:
> [prediction]
>
> Here is what actually happened in the project's subsequent development:
> [mechanically extracted: git log --oneline from snapshot to HEAD, plus issue titles closed in that range — no editorial summary, no curated narrative]
>
> Score the prediction:
> - 3: The predicted change or bug actually occurred
> - 2: Something related occurred but not exactly as predicted
> - 1: The prediction did not materialize
>
> Return: {"score": N, "rationale": "one sentence"}

### Phase 7: Health Correlation (all 50 Source 1 repos)

Diagnose current codebase (HEAD) using all three conditions. Count roles rated present, partial, or missing. Correlate role completeness with SourceRank.

3 runs × 2 models × 4 conditions = 24 calls per repo. 50 repos = 1,200 calls.

---

## Hypotheses

One primary hypothesis, five secondary. H1 is the sole primary test at α = 0.05. H2–H6 are secondary, Holm-Bonferroni corrected (five tests).

### Why Not Mapping Consistency?

Any well-defined 6-bin taxonomy produces consistent LLM mappings. Consistency measures how crisp the bins are, not how useful. We report per-condition Fleiss' kappa as an exploratory check, not as a hypothesis.

### H1: Does Correctness Matter? (primary)

NF-with-correct-contracts produces higher reconstruction scores than NF-scrambled.

**Test:** Wilcoxon signed-rank, paired by data point. Same roles, same definitions, same token count. The only difference: NF's contracts compose (each postcondition enables the next precondition); NF-scrambled's don't.

**Success:** NF > NF-scrambled, p < 0.05. Correct contracts carry more information than wrong ones. The Lean proof — which guarantees composition — is load-bearing.

**Failure:** NF-scrambled >= NF. Composition doesn't matter. Any plausible-sounding pre/postconditions work equally well. The proof is ceremony.

### H2: Does Contract Text Help? (secondary)

NF-scrambled produces higher reconstruction scores than NF-bare.

**Test:** Wilcoxon signed-rank, paired by data point. Same roles, same definitions. NF-scrambled has wrong contracts; NF-bare has none.

**Success:** NF-scrambled > NF-bare, adjusted p < 0.01. Even wrong contracts help — the extra descriptive text carries information regardless of correctness.

**Failure:** NF-bare >= NF-scrambled. Contract text without correctness is noise. Only labels matter.

### H3: Which Level of Description? (secondary)

NF-with-contracts produces higher reconstruction scores than Shannon.

**Test:** Wilcoxon signed-rank, paired by data point.

**Success:** NF > Shannon, adjusted p < 0.01. Chemistry beats physics at the lab bench.

**Failure:** Shannon >= NF. Physics-level descriptions are useful even without physics-level instruments.

### H4: Does Correctness Help Diagnosis? (secondary)

NF-with-correct-contracts produces repair suggestions that match actual resolutions better than NF-scrambled.

**Test:** Wilcoxon signed-rank, paired by failure.

**Success:** NF > NF-scrambled, adjusted p < 0.01. "Filter's postcondition violated" points to fixes better than a wrong postcondition.

**Failure:** NF-scrambled >= NF. Any contract violation framing works; correctness doesn't help diagnosis.

### H5: Does Correctness Help Prediction? (secondary)

NF-with-correct-contracts predicts subsequent development better than NF-scrambled.

**Test:** Wilcoxon signed-rank, paired by repo (one aggregated score per repo per condition). Only predictions that name a specific file, function, or behavior count — vague predictions ("the system will improve") score 0 regardless of outcome.

**Success:** NF > NF-scrambled, adjusted p < 0.01. The composable handshake predicts what gets built next.

**Failure:** NF-scrambled >= NF. Any pipeline-shaped contract predicts equally well.

### H6: Maturity Correlation (secondary)

NF role completeness correlates more strongly with project maturity (SourceRank) than other conditions.

**Test:** Steiger's test comparing four Spearman's rho values pairwise, n=50.

**Success:** NF rho > NF-scrambled rho, adjusted p < 0.01.

**Failure:** All four rho values are similar. Role completeness tracks maturity regardless of contract correctness.

**Power note:** n=50 detects rho difference >= 0.28 at p < 0.05. This is a weak test — SourceRank measures ecosystem activity (contributors, releases, dependents), not architectural health. If H6 fails, it tells us nothing about the framework; if it succeeds, the signal is suggestive but indirect.

---

## Analysis Plan

```
Unit of analysis: the data point. Each data point gets one score per
condition = median across 3 runs × 2 models. The runs and models are
for robustness, not for inflating N. N = number of unique data points.

Primary (α = 0.05):
  H1: W, p = wilcoxon_signed_rank(fidelity_NF, fidelity_NF_scrambled)

Secondary (Holm-Bonferroni, 5 tests):
  H2: W, p = wilcoxon_signed_rank(fidelity_NF_scrambled, fidelity_NF_bare)
  H3: W, p = wilcoxon_signed_rank(fidelity_NF, fidelity_Shannon)
  H4: W, p = wilcoxon_signed_rank(action_NF, action_NF_scrambled)
  H5: W, p = wilcoxon_signed_rank(predict_NF, predict_NF_scrambled)
  H6: z, p = steiger_test(rho_NF, rho_scrambled, rho_bare, rho_Shannon, n=50)

Report effect sizes (rank-biserial r) and 95% CIs for all.
Exploratory: per-condition Fleiss' kappa, all pairwise comparisons,
per-source breakdown, NF-bare vs Shannon.
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
| H1 (correctness: fidelity) | NF > NF-scrambled | Composable contracts narrow the reconstruction. "Filter's postcondition enables Attend's precondition" carries structural information that a shuffled contract doesn't. |
| H2 (text: fidelity) | NF-scrambled > NF-bare | Even wrong contracts add descriptive text that helps the LLM. But if NF-scrambled ≈ NF-bare, contract text is noise without correctness. |
| H3 (level: fidelity) | NF > Shannon | NF distinguishes Cache/Remember and Filter/Attend behaviorally. Shannon collapses these. But this comparison has the observability skew. |
| H4 (correctness: actionability) | NF > NF-scrambled | "Correct postcondition violated" points to the actual fix. Wrong postcondition points to a wrong fix. |
| H5 (correctness: prediction) | NF > NF-scrambled | The composable handshake predicts ordering. A broken handshake predicts nothing beyond what the definitions imply. |
| H6 (maturity) | NF > NF-scrambled ≈ NF-bare | Correctness helps; wrong contract text doesn't help more than no contracts. |

### What H1 Actually Tests

H1 is the clean test. NF and NF-scrambled have identical roles, identical definitions, identical token count. The only difference: NF's contracts compose; NF-scrambled's don't. If NF > NF-scrambled, the specific arrangement of pre/postconditions — the thing the Lean proof guarantees — carries information. If NF-scrambled >= NF, composition doesn't matter.

H2 tells us whether contract text helps at all, regardless of correctness. H3 (NF vs Shannon) has every confound: different roles, different contract types, observability skew. H1 has none. H1 is the experiment; H3 is context.

### What Would Change Our Minds

| Outcome | Implication |
|---------|------------|
| NF > NF-scrambled > NF-bare | **Correctness matters, and text helps.** The Lean proof guarantees something real (composition). Even wrong contracts add some value over bare labels. Strongest result for the framework. |
| NF > NF-scrambled ≈ NF-bare | **Correctness matters, but text alone doesn't.** Only composable contracts carry information. Wrong contracts are noise — no better than no contracts. The proof is the whole story. |
| NF ≈ NF-scrambled > NF-bare | **Text helps, correctness doesn't.** Any plausible-sounding pre/postconditions work. The Lean proof is ceremony — you need more prompt text, not correct contracts. |
| NF ≈ NF-scrambled ≈ NF-bare | **Neither text nor correctness helps.** The six role labels carry all the information. Contracts are irrelevant. |
| NF ≈ NF-scrambled ≈ NF-bare ≈ Shannon | **Any 6-role taxonomy works equally well** — for this task. The act of decomposing matters; the specific framework doesn't. |
| Shannon > NF > NF-scrambled | **Shannon outperforms despite observability skew, but correctness still matters within NF.** Strong result for Shannon; partial result for the proof. |

---

## Budget

No human coders (except validation subset). Four conditions, ~9,500 CLI calls.

| Phase | Per unit | Units | Calls |
|-------|----------|-------|-------|
| Phases 2-5 | ~80 calls/data point | ~100 data points | ~8,000 |
| Phase 6 | ~48 calls/repo | 5 repos | ~240 |
| Phase 7 | ~24 calls/repo | 50 repos | ~1,200 |
| **Total** | | | **~9,400** |

At ~10s per call: ~26 hours wall clock. Parallelizable across models and conditions.

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

**Token-count confound (H2 only).** NF-scrambled and NF have ~equal token counts, so H1 (NF vs NF-scrambled) is length-controlled. H2 (NF-scrambled vs NF-bare) is not — NF-scrambled has more text. If NF-scrambled > NF-bare, the win might be more tokens, not contract structure. Token counts for each condition are measured and reported.

**Prompt language confound (H2).** NF's role names (perceive, filter, attend) are more software-adjacent than Shannon's (source, encoder, channel). An LLM mapping software components will find NF's vocabulary more natural regardless of contract quality. This biases H2 but not H1 — NF and NF-bare share the same vocabulary.

**Contract research asymmetry (H2 only).** The researcher designed NF's contracts and researched Shannon's from textbooks. Does not affect H1. Mitigation: all rubrics committed verbatim. [Shannon contract research →](lenses/shannon.md)

---

## Transparency

- All prompts committed verbatim before Phase 2.
- Lens anonymization key sealed until analysis.
- All raw data published as JSON/CSV.
- Timestamped by commit. No amendments after Phase 2 without a dated commit explaining the change.

---

*Four conditions: Shannon (physics), NF with correct contracts (Lean-proven), NF with scrambled contracts (same text, wrong assignment), NF-bare (labels only). The sharp test is NF vs NF-scrambled — does composition matter, or do any plausible-sounding contracts work equally well? That's the Lean test.*
