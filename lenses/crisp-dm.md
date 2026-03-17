# CRISP-DM — Inter-Stage Contracts

Source: CRISP-DM 1.0 Guide (1999, consortium of NCR, DaimlerChrysler, SPSS, OHRA).

## Roles

| Role | Definition |
|------|-----------|
| **Business Understanding** | Define objectives and requirements from a business perspective |
| **Data Understanding** | Collect initial data and identify quality issues |
| **Data Preparation** | Construct the final dataset from raw data |
| **Modeling** | Apply modeling techniques to prepared data |
| **Evaluation** | Assess the model against business objectives |
| **Deployment** | Put the model into production use |

## Contracts

### Per-Transition Constraints

| Transition | Contract |
|-----------|---------|
| Business Understanding → Data Understanding | Must produce business objectives, business success criteria, data mining goals, data mining success criteria, and a project plan. Data Understanding cannot identify relevant sources without knowing the problem |
| Data Understanding → Data Preparation | Must produce initial data collection report, data description (formats, counts, field identities), data exploration report (patterns, distributions), and data quality report (missing values, outliers, inconsistencies) |
| Data Preparation → Modeling | Must produce cleaned/merged/reformatted dataset with documented inclusion/exclusion rationale, derived attributes, and dataset description (schema, types, distributions). Training/test/validation splits specified |
| Modeling → Evaluation | Must produce trained model artifacts with performance metrics, documented technique selection, stated assumptions, test design, and parameter settings. Multiple competing models enable comparison |
| Evaluation → Deployment | Must produce assessment against **business success criteria** (not just technical metrics), approved models, process review, and an explicit go/no-go decision. This is the primary stage gate |

### Mandatory Iteration

CRISP-DM explicitly states: "The sequence of the phases is not rigid. Moving back and forth between different phases is always required." Iteration triggers:

- Models underperform business criteria → return to Business Understanding
- Data quality issues discovered during modeling → return to Data Preparation
- Exploration reveals ill-defined problem → return to Business Understanding
- Insufficient features → return to Data Preparation

### What Is Under-Specified

- **Dataset format requirements** (Data Preparation → Modeling): mentions "Reformatted Data" but no schema contracts
- **Go/no-go criteria** (Evaluation → Deployment): requires a "Decision" but doesn't specify approval authority or evidence thresholds
- **Iteration termination**: states iteration is "always required" but doesn't specify when to stop vs. declare infeasible
- **Model artifact format** (Modeling → Evaluation): no versioning, reproducibility, or lineage requirements

## Contract Strength

**Weak to moderate.** CRISP-DM documents deliverables at each stage, which function as informal contracts. The strongest constraint is the Evaluation gate (business criteria, not just technical metrics). But the framework lacks formal handoff specifications — each implementation invents its own validation. The mandatory iteration is a structural constraint that other lenses don't specify, but it's a process rule rather than a data contract.
