# MAPE-K — Inter-Stage Contracts

Source: Kephart & Chess, "The Vision of Autonomic Computing," *Computer* 36(1), 2003. IBM Autonomic Computing Blueprint (4th ed., 2006).

## Roles

| Role | Definition |
|------|-----------|
| **Monitor** | Collect data from the managed system and environment |
| **Analyze** | Correlate and model monitored data to detect issues |
| **Plan** | Decide on actions to achieve objectives |
| **Execute** | Carry out planned changes to the managed system |
| **Knowledge** | Shared persistent store that all other stages read and write |

## Contracts

### Key Finding: Intentionally Under-Specified

IBM's original blueprint deliberately left inter-stage contracts informal. MAPE-K is an architectural pattern, not a formal specification. Practitioners discovered this gap empirically — Krupitzer et al. (2015) found 73% of MAPE-K implementations added custom validation layers between stages.

### Per-Transition Constraints (Implicit)

| Transition | Constraint |
|-----------|-----------|
| Monitor → Analyze | Monitor must produce structured observations (timestamped, associated with system components). No formal guarantees on completeness or quality |
| Analyze → Plan | Analyze must produce a diagnosis or decision trigger. No formal postcondition on diagnosis correctness or confidence |
| Plan → Execute | Plan must produce actionable adaptation strategies. No formal verification that plans are safe or feasible |
| Execute → Monitor | Execute applies changes, triggering monitoring of effects. No formal guarantee of execution success or atomicity |

### Knowledge Base Constraints

- All stages can read from Knowledge (goals, policies, past cases)
- Typically only Analyze and Plan write to Knowledge
- No formal consistency model (no ACID guarantees, no versioning requirements)
- Race conditions and stale data are possible

### What Is Missing

- No type systems for inter-stage messages
- No pre/postcondition specifications
- No data quality requirements
- No timing constraints
- No failure semantics
- No composition rules for multiple MAPE-K loops

## Contract Strength

**Weak.** MAPE-K provides architectural guidance but avoids formal contracts by design. Each implementation invents its own inter-stage validation. This makes MAPE-K a clean test case: it has the same number of bins as NF's pipeline (if you count Knowledge as a cross-cutting concern rather than a stage), but without contracts.
