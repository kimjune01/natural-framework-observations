# Natural Framework Fidelity Study

Comparative fidelity study: four lenses (IPO, OODA, Intelligence Cycle, Natural Framework) applied to the same data. Measures reconstruction accuracy, inter-rater agreement, and diagnostic actionability.

## Key files

- `PREREGISTRATION.md` — hypotheses, data sources, analysis plan. **Do not modify** unless appending a dated amendment with rationale.
- `rubric.md` — four rubrics (one per lens). Immutable after coder training, except clarifications appended below the marker line.
- `data/` — coded CSVs. Append-only after coding begins.

## Data collection

### Source 1: RL bug issues
```
gh search repos "reinforcement learning" --language=python --sort=stars --limit=5
```
Then per repo:
```
gh issue list -R {owner}/{repo} --state=closed --label=bug --json number,title,comments,createdAt
```
Filter: >= 10 comments, created 2022-2025.

### Source 2: Post-mortems
Snapshot danluu/post-mortems at a specific commit. Record hash in `DATA_SOURCES.md`.

### Source 3: Ablation tables
NeurIPS 2024 oral/spotlight papers with "ablation" in the text. Extract all removal rows from ablation tables.

## Analysis

- H1 (fidelity): Wilcoxon signed-rank, reconstruction scores, paired by data point
- H2 (agreement): Cohen's kappa per lens, compare across lenses
- H3 (actionability): Wilcoxon signed-rank, actionability scores, paired by failure
- Multiple comparisons: Holm-Bonferroni correction across 9 tests

## Rules

- No exploratory analyses without labeling them as such
- No rubric changes after coder training (clarifications only, appended, committed)
- No amendments to preregistration after data collection starts without a new dated commit
- All raw data committed before analysis
- All four rubrics must be equivalently detailed — if one gets boundary cases, all get boundary cases
