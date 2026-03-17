# Intelligence Cycle — Inter-Stage Contracts

Source: JP 2-0 (Joint Intelligence), ICD 203 (Analytic Standards), Lowenthal, *Intelligence: From Secrets to Policy*.

## Roles

| Role | Definition |
|------|-----------|
| **Direction** | Define information requirements (PIRs, EEIs) |
| **Collection** | Gather raw data from sources |
| **Processing** | Convert raw data into usable format |
| **Analysis** | Evaluate processed data, produce assessments |
| **Dissemination** | Distribute finished intelligence to consumers |
| **Feedback** | Consumer responses inform the next cycle's direction |

## Contracts

### Per-Transition Constraints

| Transition | Contract |
|-----------|---------|
| Direction → Collection | Direction must specify Priority Intelligence Requirements (PIRs), Essential Elements of Information (EEIs), geographic/temporal scope, timeliness needs. Collection cannot begin without formal tasking |
| Collection → Processing | Collection must provide raw data with metadata: source, time, location, collection method, custody/classification markings, reliability indicators. Format determines processing pipeline |
| Processing → Analysis | Processing must deliver structured/normalized data with metadata preserved, correlated across intelligence disciplines, indexed and searchable. Duplicates removed or flagged |
| Analysis → Dissemination | Analysis must produce assessed intelligence with confidence levels, attribution to evidence, alternative hypotheses considered (ICD 203). Must address original PIR |
| Dissemination → Feedback | Dissemination must ensure timely delivery to cleared recipients in consumable format. Delivery confirmation required |
| Feedback → Direction | Feedback must contain relevance assessment, timeliness evaluation, gap identification, and updated PIRs. Gaps become new Direction tasking |

### Cross-Cutting Constraints (ICD 203)

- **Traceability:** Each stage must preserve provenance back to collection source
- **Uncertainty propagation:** Confidence levels must propagate forward from Collection through Analysis
- **Assumption documentation:** Analysis must flag where assumptions fill gaps in collected data

### Resource Constraints

- Collection is finite: Direction must prioritize
- Processing is lossy: filtering decisions constrain what Analysis can see
- Analysis is time-bounded: Dissemination deadlines constrain depth

## Contract Strength

**Moderate.** The Intelligence Cycle is descriptive (what stages exist) more than prescriptive (formal contracts). The strongest constraints are principle-based: no collection without tasking, intelligence must address PIRs, traceability through the pipeline. ICD 203 adds analytic standards (confidence levels, alternative hypotheses) but doesn't specify formal handoff schemas. Stronger than MAPE-K, weaker than Shannon or VSM.
