# Natural Framework Validation Study

Preregistered retrospective case-study validation. Three data sources, three hypotheses, two independent coders.

## Key files

- `PREREGISTRATION.md` — hypotheses, data sources, analysis plan. **Do not modify after initial commit** unless appending a dated amendment with rationale.
- `rubric.md` — coding rubric. Immutable after coder training, except clarifications appended below the marker line.
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

- H1: binomial hit rate with Wilson CI
- H2: Cohen's kappa (6-category)
- H3: Spearman's rho (role position vs normalized performance drop)

## Rules

- No exploratory analyses without labeling them as such
- No rubric changes after coder training (clarifications only, appended, committed)
- No amendments to preregistration after data collection starts without a new dated commit
- All raw data committed before analysis
