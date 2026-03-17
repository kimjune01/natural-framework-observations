# F3EAD — Inter-Stage Contracts

Source: US Special Operations doctrine; JP 3-60 (Joint Targeting); publicly available SOCOM publications.

## Roles

| Role | Definition |
|------|-----------|
| **Find** | Identify targets through intelligence and surveillance |
| **Fix** | Locate and track the target precisely enough to act |
| **Finish** | Execute the operation against the target |
| **Exploit** | Gather intelligence from the operation's results |
| **Analyze** | Process exploited material to generate new understanding |
| **Disseminate** | Share findings to feed subsequent cycles |

## Contracts

### Per-Transition Constraints

| Transition | Contract |
|-----------|---------|
| Find → Fix | Find must produce target identification, pattern-of-life intelligence, and a nomination package meeting minimum confidence threshold. Target must be within operational area |
| Fix → Finish | Fix must achieve positive identification (PID) to ROE standards (~90%+ confidence), precise geolocation, collateral damage estimate, and command authority. **This is the critical gate** — insufficient Fix → mission abort |
| Finish → Exploit | Finish must produce battle damage assessment, site control, and a time window before site compromise. Speed is critical — intelligence has a half-life |
| Exploit → Analyze | Exploit must provide physical/digital evidence with chain of custody, site intelligence, and time-sensitive information (TSI) flagged for immediate forwarding |
| Analyze → Disseminate | Analyze must produce finished intelligence (not raw data): target packages, network analysis, products addressing Priority Intelligence Requirements |
| Disseminate → Find | New target nominations, network expansion (associates enter Find), PIR updates, pattern refinements. Closes the loop |

### Escalating Confidence Threshold

Intelligence confidence must *increase* as the cycle progresses:
- Find: low threshold (target of interest)
- Fix: medium threshold (confirmed identity)
- Finish: high threshold (PID to ROE standard)

This is an ordering constraint — you can't Finish at Find-level confidence.

### Time Sensitivity

Each stage has degrading value. Intelligence ages quickly in dynamic environments. Typical cycle: 24-48 hours from Find to next Find for network targeting.

### Cross-Cutting Constraints

- ROE compliance at every stage (Law of Armed Conflict)
- Authority requirements increase through the cycle
- Multiple F3EAD cycles run concurrently, competing for ISR and strike assets

## Contract Strength

**Moderate.** F3EAD has operational contracts — confidence thresholds, chain of custody, ROE gates — but they're doctrine-based rather than formally specified. The escalating confidence requirement is an ordering constraint analogous to NF's handshake, but expressed as policy rather than as pre/postconditions. Stronger than MAPE-K and CRISP-DM; weaker than Shannon, Immune, or VSM.
