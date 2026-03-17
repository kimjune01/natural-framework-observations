# Natural Framework Fidelity Study

LLM-automated comparative fidelity study. Eight lenses (VSM, MAPE-K, Intelligence Cycle, Shannon, Immune Response, F3EAD, CRISP-DM, Natural Framework) applied to the same data by GPT-5.4 and Sonnet 4.5. Blind judge scores reconstruction accuracy.

## Key files

- `PREREGISTRATION.md` — hypotheses, lenses, procedure, analysis plan. **Do not modify** unless appending a dated amendment.
- `rubric.md` — eight rubrics (one per lens), inserted verbatim into LLM prompts.
- `data/` — all mappings, reconstructions, judgments as JSON. Append-only.

## Data collection

### Source 1: RL bug issues
```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=5
```
Per repo: closed issues, labeled "bug", >= 10 comments, created 2022-2025.

### Source 2: Post-mortems
Snapshot danluu/post-mortems at a specific commit. Record hash in `DATA_SOURCES.md`.

### Source 3: Ablation tables
NeurIPS 2024 oral/spotlight papers with "ablation" in the text. All removal rows.

## Execution

Per data point: 3 runs × 2 models × 8 lenses = 48 mapping calls, plus reconstruction, judging, and repair calls. ~160 calls per data point. ~16,000 total.

## Analysis

- H1 (fidelity): Wilcoxon signed-rank, paired by data point, Holm-Bonferroni corrected
- H2 (consistency): Fleiss' kappa across 3 runs per model per lens
- H3 (actionability): Wilcoxon signed-rank on repair scores
- Cross-model: compare GPT-5.4 vs Sonnet mappings per lens

## Rules

- No exploratory analyses without labeling them as such
- All prompts committed verbatim before Phase 2
- All raw data committed before analysis
- Lens identity anonymized during judging
