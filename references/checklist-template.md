# MAR checklist output template

Save a fresh `MAR_REVIEW_CHECKLIST_<RUN_TAG>.md` next to the draft. Replace the
placeholders with observed facts; omit empty themes. Do not assume full completion.

```markdown
# MAR review triage — <draft> (<run tag>)

Source: <report path and run_id, engine version, manuscript hashes; or model-native>.
Mode: <offline / provider panel / paired comparison / preview / model-native>.
Coverage: <roles requested, returned and failed; rejected items; quarantined concerns>.
Counts: <active concerns by severity; pending candidates separately>.

Source matching establishes the quoted text, not scientific correctness. Repeated
role opinions are not independent evidence. Recommendations below await author
adjudication; the manuscript and engine records have not been changed.

## Priority themes

### T1 — <claim-level concern>. Recommendation: <accept/revise / rebut / partial / verify first>

- Claim at stake: <actual claim and consequence>.
- Source records: <canonical finding ID(s), role(s), severity, confidence, disposition>.
- Evidence: <short exact passage and source location; distinguish observation from inference>.
- Counterevidence / strongest rebuttal: <source and interpretation, or not located in supplied material>.
- Judgment: <why the concern survives, narrows, or needs verification; any disagreement>.
- Decisive next check: <data required, comparison, unit, metric, and what would change the judgment>.

<Repeat only for justified themes, ordered by impact and evidence, not role count.>

## Strengths to preserve

- <Reported evidence and the bounded contribution it supports; source ID/location>.

## Other findings and verification queue

- [ ] <finding ID; disposition; severity; role(s)> — <issue → check; theme if grouped>.
- [ ] <external or quarantined suggestion; verification needed before promotion>.

## Revision status (paired mode only)

<Resolved / partial / persistent / worsened / unclear / newly detected / scope withdrawn,
with comparison record IDs and old/new evidence. Execution remains unverified.>

## Journal requirements (if relevant)

- <Requirement; official source URL/date or supplied policy; met / partial / unverified>.

## Author decisions

- T1: [ ] Accept/revise [ ] Rebut [ ] Partial [ ] Verify first. Reason: <author fills in>.

Suggested first step: <highest-value discriminating check; prerequisites if unknown>.
```
