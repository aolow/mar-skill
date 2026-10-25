# MAR checklist output template

Save a fresh `MAR_REVIEW_CHECKLIST_<RUN_TAG>.md` next to the draft. Replace the
placeholders with observed facts; omit empty themes. Do not assume full completion.
Follow [writing.md](writing.md): state results, interpretations and limits directly,
with adjacent references and enough study context to assess each conclusion.

Aim for 120–180 words in **Review at a glance**, using the completed triage as its
source. State a bounded scientific assessment, one supported contribution, up to
three priorities and a useful first step. Link to the detailed themes/strengths;
keep verification questions conditional and say when a strength is not established.
Retain a short mode/version/coverage line. Do not introduce new findings, numerical
quality scores or a submission-readiness verdict through the summary. Keep the
source records and full evidence challenge below it.

```markdown
# MAR review triage — <draft> (<run tag>)

## Review at a glance

**Assessment:** <one or two sentences about what the evidence supports and the
main revision need; link to relevant themes>.

**Main contribution:** <bounded, supported strength, linked to S1; or not established>.

**Review priorities:**

1. <highest-value revision or explicitly labeled verification task> — [T1](#t1).
<Up to two more priorities if justified; preserve their status.>

**Next step:** <one practical action and why it matters; link to the theme>.

*Scope: <actual mode, draft/version and material exclusions or incomplete coverage>.*

## Review record

Source: <report path and run_id, engine version, manuscript hashes; or model-native>.
Mode: <offline / provider panel / paired comparison / preview / model-native>.
Coverage: <roles requested, returned and failed; rejected items; quarantined concerns>.
Counts: <active concerns by severity; pending candidates separately>.

Source matching establishes the quoted text, not scientific correctness. Repeated
role opinions are not independent evidence. Recommendations below await author
adjudication; the manuscript and engine records have not been changed.

## Priority themes

<a id="t1"></a>

### T1 — <claim-level concern>. Recommendation: <accept/revise / rebut / partial / verify first>

- Claim at stake: <actual claim and consequence>.
- Source records: <canonical finding ID(s), role(s), severity, confidence, disposition>.
- Reported result: <study context, relevant result and short exact passage with source location>.
- Counterevidence / strongest rebuttal: <source and interpretation, or not located in supplied material>.
- Assessment: <what the result supports, its limits, references and any disagreement>.
- Decisive next check: <data required, comparison, unit, metric, and what would change the judgment>.

<Repeat only for justified themes, with stable anchors t2, t3, etc.; order by impact
and evidence, not role count.>

## Strengths to preserve

<a id="s1"></a>

- S1: <Reported evidence and the bounded contribution it supports; source ID/location>.

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
