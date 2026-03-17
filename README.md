# Natural Framework: Retrospective Validation Study

Preregistered empirical test of the [Natural Framework](https://june.kim/the-natural-framework) — a formal specification (Lean 4, zero sorry) claiming six roles are necessary for any bounded information processor.

## What this repo contains

- **`PREREGISTRATION.md`** — Hypotheses, data sources, analysis plan, and stopping rules. Locked at initial commit.
- **`rubric.md`** — Coding rubric for mapping components to roles and failures to death conditions.
- **`data/`** — Raw coded data (CSV), published after coding.

## Hypotheses

1. **H1:** Documented system failures map to the framework's three death conditions (>70% hit rate).
2. **H2:** Independent coders agree on role mappings for ablated ML components (Cohen's kappa >0.6).
3. **H3:** Removing earlier-stage components causes larger performance drops than later-stage ones (Spearman's rho < -0.3).

## Data sources

All pre-specified and mechanically enumerable. See `PREREGISTRATION.md` for exact queries.

1. Bug issues from the top 5 RL frameworks (GitHub stars)
2. Software post-mortems from [danluu/post-mortems](https://github.com/danluu/post-mortems)
3. Ablation tables from NeurIPS 2024 oral/spotlight papers

## Contributing

Coders needed. Requirements:
- Unfamiliar with the Natural Framework
- Willing to follow the rubric strictly
- Available for ~5 hours of coding over 2 weeks

Open an issue or email to volunteer.

## Related work

- [The Natural Framework](https://june.kim/the-natural-framework) — the specification under test
- [Diagnosis LLM](https://june.kim/diagnosis-llm) — diagnostic workflow that motivated this study
- [Metacognition experiment](https://github.com/kimjune01/metacognition) — prior rounds testing framework utility for LLMs
