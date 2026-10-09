# MAR checklist output template

Copy this structure into `MAR_REVIEW_CHECKLIST_<YYYY-MM-DD>.md` next to the draft. Replace the
bracketed parts. The example cells below are illustrative of the *form*, not real findings.

---

```markdown
# MAR adversarial-review triage — <DRAFT_FILE> (<YYYY-MM-DD>)

Source: manuscript-adversarial-reviewer, six-role panel (methods, reviewer2, scientific, novelty,
reproducibility, computational) on `<path/to/draft>`. <N> findings.

These are **reviewer-anticipation items on an unsubmitted draft**, not detected errors. Grounding
guarantees only that each cited quote is a verbatim excerpt — the scientific judgments are
unverified and are the point to adjudicate. Mark each: **[A]** accept/revise, **[R]** rebut (and
how), **[P]** partial. Findings skew low-confidence by design; the cross-role convergence (same
issue from independent roles) is the real signal.

---

## Priority themes (recur across roles/sections) — recommended disposition

**T1 — <one-line theme>. [recommend A|R|P]**
<which roles converged, which sections>. <the issue in 2–3 sentences, grounded>.
Decisive check: <the single cheapest thing that settles it>.

**T2 — <one-line theme>. [recommend A|R|P]**
...

(Order themes by convergence × severity. Give any theme that touches a specific headline number
its own line and flag it — those are the weaponizable ones.)

---

## By section (all findings; [sev | roles] + 1-line issue → fix)

### Results / "<subsection heading>" (<count>)
- [ ] **major** <role> — <issue, grounded in a quote> → <fix>. *(T1)*
- [ ] moderate <role> — <issue> → <fix>. *(T2)*
- [ ] minor <role> — <issue> → <fix>.

### Methods (<count>)
- [ ] ...

### Discussion (<count>)
- [ ] ...

### Availability of data and materials (<count>)
- [ ] ...

---

## Hard gates (<target journal, or "journal-agnostic">)
- [ ] <gate — e.g. deposit datasets X/Y/Z + accessions> — status: unmet / partial / met.
- [ ] <gate — permanent code URL + pinned env> — status: ...

---

## Status
- Six-role panel complete; <N> findings. Cross-role convergence: <which themes were flagged by
  the most independent roles>.
- **Nothing in the draft has been changed off this review.** All items unadjudicated.
- Suggested order: settle the highest-convergence hinge theme (<T#>) first, then run the cheap
  decisive checks for <T#, T#>.
```
