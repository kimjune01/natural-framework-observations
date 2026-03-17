# Natural Framework: Comparative Fidelity Study

Does the [Natural Framework](https://june.kim/the-natural-framework) decompose information-processing systems with higher fidelity than existing lenses?

## What this repo contains

- **`PREREGISTRATION.md`** — Hypotheses, data sources, analysis plan, and stopping rules.
- **`rubric.md`** — Coding rubrics for all four lenses (IPO, OODA, Intelligence Cycle, Natural Framework).
- **`data/`** — Raw coded data (CSV), published after coding.

## The question

Multiple frameworks decompose systems into named roles. The question is not whether failures "fit" into bins — any taxonomy with enough bins achieves that. The question is which lens preserves the most information through the compression.

**Fidelity:** Given only a role label, can you reconstruct what the component does? "Process" (IPO) tells you nothing. "Filter" (Natural Framework) tells you it gates. That difference is measurable.

## Lenses compared

| Lens | Bins | Origin |
|------|------|--------|
| **IPO** | 3 (Input, Process, Output) | Systems theory — baseline |
| **OODA** | 4 (Observe, Orient, Decide, Act) | Military strategy |
| **Intelligence Cycle** | 6 (Direction, Collection, Processing, Analysis, Dissemination, Feedback) | Intelligence community |
| **Natural Framework** | 6 (Perceive, Cache, Filter, Attend, Remember, Consolidate) | [Formal specification](https://github.com/kimjune01/natural-framework) |

## Hypotheses

1. **H1 (fidelity):** Natural Framework labels produce higher reconstruction accuracy than all other lenses.
2. **H2 (agreement):** Natural Framework produces higher inter-rater agreement than all other lenses.
3. **H3 (actionability):** Natural Framework labels produce more actionable repair suggestions than all other lenses.

## Data sources

1. Bug issues from the top 5 RL frameworks (GitHub stars)
2. Software post-mortems from [danluu/post-mortems](https://github.com/danluu/post-mortems)
3. Ablation tables from NeurIPS 2024 oral/spotlight papers

## Contributing

Coders needed. Requirements:
- Unfamiliar with the Natural Framework
- Willing to apply all four rubrics to the same data
- Available for ~8 hours of coding over 2 weeks

Open an issue or email to volunteer.

## Related work

- [The Natural Framework](https://june.kim/the-natural-framework) — the specification under test
- [Lean 4 proof](https://github.com/kimjune01/natural-framework) — formal verification
- [Metacognition experiment](https://github.com/kimjune01/metacognition) — prior rounds testing framework utility for LLMs
