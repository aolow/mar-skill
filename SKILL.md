---
name: mar
description: Manuscript Adversarial Reviewer. Run a six-role adversarial peer-review panel against an unsubmitted manuscript draft to anticipate the harshest reviewer objections before submission, then triage the findings into priority themes with recommended dispositions. Use when the user asks to MAR a draft, run an adversarial/pre-submission review, stress-test a paper's claims, or anticipate reviewer attacks. Invoked as /mar [path-to-draft].
---

# mar — Manuscript Adversarial Reviewer

Run a six-role adversarial peer-review panel against a manuscript draft, grounded in verbatim
quotes from the draft, then triage the findings. The purpose is **reviewer anticipation on an
unsubmitted draft** — surface the objections a hostile, competent reviewer would raise, so the
authors can accept/revise or build a rebuttal *before* submission. These are not detected
errors; they are claims to adjudicate.

The model does the whole review itself. There is no external runner — you read the draft, play
each role in turn, and write the findings and triage to a dated checklist file.

## 1. Inputs

- **Draft path** (required): a manuscript file (markdown preferred; also `.tex`, `.docx`→read as
  text, or a directory of section files). If not given, ask for it. Read the whole thing.
- **Target journal** (optional): changes which bars are *hard gates* (e.g. Genome Biology /
  Nature-family require a data-and-code-availability gate; clinical journals require
  reporting-standard compliance). If unknown, ask or state the gate as journal-agnostic.
- **Scope** (optional): which roles to run (default: all six) and whether to limit to specific
  sections.
- **Repo conventions**: if the draft sits in a repo with a `CONVENTIONS.md`, read it — honor its
  terminology and sign conventions in how you phrase findings, but do not let it soften the
  adversarial read.

## 2. The six roles

Run each as an independent reviewer. Do not let one role's conclusions leak into another's — the
value is **cross-role convergence**: when independent roles flag the same issue, that is the real
signal, far more than any single low-confidence finding. Full charges in
[references/roles.md](references/roles.md); in brief:

1. **methods** — study design, statistics, controls, thresholds, power. Is each test the right
   test, is each threshold justified, is each control present?
2. **reviewer2** — the skeptical generalist who distrusts the headline. Attacks the strongest
   claim and the most load-bearing number.
3. **scientific** — biological/scientific plausibility and whether the narrative is forced onto
   the data. Would the result survive an alternative explanation?
4. **novelty** — what is actually new vs restated; whether comparisons to prior work /
   benchmarks are missing or cherry-picked.
5. **reproducibility** — can an outsider rerun this? Seeds, versions, data/code availability,
   deposited accessions, env pinning.
6. **computational** — the circularity / leakage / confounding / pseudoreplication / validation
   specialist. Hunts for label-in-feature contamination, train-in-test leakage, non-independent
   replicates, and circular readouts.

## 3. Grounding (non-negotiable)

Every finding must cite a **verbatim excerpt** from the draft (quote + section/line) as its
anchor. The quote is what you guarantee; the scientific judgment attached to it is *unverified by
design* and is the point the authors adjudicate. If you cannot ground a finding in a real quote,
drop it — do not invent text. Findings skew low-confidence intentionally; breadth plus
cross-role convergence is the design, not per-finding certainty.

## 4. Per-finding schema

Each finding is one line:

```
[severity | role(s)] <section> — <1-line issue grounded in a quote> → <cheap/decisive fix>
```

- **severity**: `major` / `moderate` / `minor`.
- **role(s)**: which role(s) raised it; list all if convergent.
- **fix**: prefer a *cheap decisive check* the authors can run (e.g. "re-run the classifier with
  the N label genes excluded; report the AUROC delta") over vague advice.

Keep the finding falsifiable and specific. "Clarify the methods" is not a finding; "the δ≥0.15
bar sits above the max observed 0.135 with no stated justification → report the δ distribution
and justify the cut" is.

## 5. Procedure

**Pass 1 — per-role findings.** For each role, walk the draft section by section and emit every
grounded finding at that role's altitude. Tag severity. Expect the Results and Methods sections
to be densest. Do not deduplicate across roles yet — convergence is signal.

**Pass 2 — triage.** Collapse the per-role findings into **priority themes** `T1, T2, …`, one per
recurring issue (an issue flagged by ≥2 independent roles is a theme). For each theme give:
- the roles that converged on it and the sections it touches,
- a **recommended disposition**: `[A]` accept/revise, `[R]` rebut (and exactly how), `[P]`
  partial, and
- the single cheapest check that would settle it.
Rank themes by (convergence × severity). Call out any finding that touches a specific headline
number separately — those are the ones a reviewer can weaponize.
Flag the hard gates for the target journal explicitly (§1).

## 6. Output

Write a dated checklist next to the draft: `MAR_REVIEW_CHECKLIST_<YYYY-MM-DD>.md`, following
[references/checklist-template.md](references/checklist-template.md). Structure:

1. **Header** — draft path/version, roles run, finding count, the "reviewer-anticipation, not
   detected errors; grounding guarantees the quote not the judgment" disclaimer.
2. **Priority themes** — T1…Tn with convergence, recommended disposition, decisive check.
3. **By section** — all findings, `[severity | roles]` + issue → fix, mapped to their theme.
4. **Hard gates** — the target-journal gates and their status.
5. **Status** — initially "unadjudicated; nothing in the draft changed off this review."

Then give the user a short plain-text summary: finding count, the top 3 themes by convergence,
and the suggested adjudication order (settle the highest-convergence hinge theme first). Do **not**
edit the manuscript — MAR only reviews. Leave adjudication (`[A]/[R]/[P]`) to the user.

## 7. Limits

- You are anticipating reviewers, not fact-checking the science end-to-end. State clearly that
  the judgments are unverified and that convergence, not any single finding, is the signal.
- Never fabricate a quote, a number, or a citation. If a claim's supporting figure/table is
  absent from the draft, that absence is itself a (reproducibility) finding.
- Do not soften findings to be polite, and do not inflate a minor into a major to pad the list.
- One clean re-run against a revised draft is the natural follow-up; keep each run's checklist
  dated so the authors can diff.
