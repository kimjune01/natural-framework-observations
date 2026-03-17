# Natural Framework: Comparative Fidelity Study

Does the [Natural Framework](https://june.kim/the-natural-framework) decompose information-processing systems with higher fidelity than existing lenses?

## The question

Multiple frameworks decompose systems into named roles. The question is not whether things fit into bins — any taxonomy achieves that. The question is which lens preserves the most information through the compression. That is fidelity.

**Fidelity metric:** Map a component to a role label. From just the label, try to reconstruct what the component does. The lens that enables the best reconstruction wins.

## Lenses compared

Eight lenses, selected from a [catalog of 80+](https://github.com/kimjune01/natural-framework-fidelity/blob/master/lens_catalog.md), matched at 5-6 bins:

| Lens | Bins | Origin |
|------|------|--------|
| Viable System Model | 5 | Cybernetics |
| MAPE-K | 5 | Autonomic computing |
| Intelligence Cycle | 6 | Military/intelligence |
| Shannon | 6 | Information theory |
| Immune Response | 6 | Immunology |
| F3EAD | 6 | Military operations |
| CRISP-DM | 6 | Data science |
| **Natural Framework** | 6 | [Formal specification](https://github.com/kimjune01/natural-framework) |

## Method

LLM-automated. GPT-5.4 and Sonnet 4.5 independently map data through all eight lenses. A blind judge scores reconstruction fidelity. No human coders.

## Hypotheses

1. **H1 (fidelity):** NF labels produce higher reconstruction accuracy than all other lenses at matched bin count.
2. **H2 (consistency):** NF produces higher self-consistency across repeated mappings.
3. **H3 (actionability):** NF labels produce more actionable repair suggestions for system failures.

## Data sources

1. Bug issues from the top 5 RL frameworks (GitHub stars)
2. Software post-mortems from [danluu/post-mortems](https://github.com/danluu/post-mortems)
3. Ablation tables from NeurIPS 2024 oral/spotlight papers

## Related work

- [The Natural Framework](https://june.kim/the-natural-framework) — the specification under test
- [Lean 4 proof](https://github.com/kimjune01/natural-framework) — formal verification
- [Metacognition experiment](https://github.com/kimjune01/metacognition) — prior rounds testing framework utility for LLMs
