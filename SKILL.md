---
name: mar
description: Manuscript Adversarial Reviewer. Drive the `manuscript-review` CLI (package manuscript-adversarial-review, repo aolow/manuscript-adversarial-reviewer) to run a six-role adversarial peer-review panel over Bedrock against a manuscript draft, then surface and triage the findings before submission. Use when the user asks to MAR a draft, run an adversarial/pre-submission review, stress-test a paper's claims, or anticipate reviewer attacks. Invoked as /mar [path-to-draft].
---

# mar — Manuscript Adversarial Reviewer (wrapper)

This skill is a **wrapper** around the real tool: the `manuscript-review` CLI (package
`manuscript-adversarial-review`, source `github.com/aolow/manuscript-adversarial-reviewer`). The
CLI does the work — it extracts the manuscript, runs deterministic reporting/design checks, calls
the six-role LLM panel over Amazon Bedrock, grounds every concern to a verbatim excerpt, and writes
a Markdown + JSON report. This skill's job is to invoke it correctly, keep the output durable, and
triage the result into the repo's checklist format.

The CLI is the source of truth for flags and behavior — run `manuscript-review review --help`
rather than restating its rules here. If the CLI is unavailable, fall back to the Claude-native
panel (§5).

## 1. Preflight

1. **Find the CLI.** `command -v manuscript-review`. If missing, tell the user to install it (do
   **not** clone into `/tmp`):
   `pip install "git+ssh://git@github.com/aolow/manuscript-adversarial-reviewer.git#egg=manuscript-adversarial-review[bedrock]"`
   then stop for them to install, or use the §5 fallback.
2. **Bedrock creds.** The panel needs AWS credentials and a Bedrock model id. Check env:
   `MANUSCRIPT_REVIEW_MODEL` (required unless passed as `--model`), `AWS_PROFILE` / `AWS_REGION`
   (or `MANUSCRIPT_REVIEW_AWS_REGION`). If no model is resolvable, ask the user for the Bedrock
   model id / inference-profile ARN (and confirm the AWS profile) before running — do not guess one.
3. **Draft path** (required). If not given, ask. Note the draft's directory — output goes there.

## 2. Run

Always pass an explicit, durable `--out` **next to the manuscript** — the CLI's default output dir
is relative to the current working directory, which is how a prior run landed in `/tmp` and nearly
got purged. Write to `<draft_dir>/mar_reviews/<YYYY-MM-DD>[_tag]/` instead.

```sh
manuscript-review review <DRAFT> \
  --llm --provider bedrock \
  --model "${MANUSCRIPT_REVIEW_MODEL:?set or pass --model}" \
  --journal "<target journal, if named>" \
  --out "<DRAFT_DIR>/mar_reviews/<YYYY-MM-DD>" \
  --force
```

- **Default roles are the six core adversarial roles** (scientific, methods, computational,
  novelty, reviewer2, reproducibility) — do not pass `--roles` unless the user wants a different
  set (e.g. add `statistics,clinical`).
- **Validate cheaply first when unsure:** `--dry-run` with `--llm` exports the exact per-role
  requests and runs the deterministic checks **without** calling Bedrock (no cost, no creds). Use
  it to confirm extraction and role set before the real run.
- **Version tracking:** when reviewing a revision, pass `--prior <OLD_DRAFT>` and, if available,
  `--prior-review <OLD_report.json>` so the CLI marks each prior concern resolved / persistent /
  worsened / out-of-scope. This is the right way to show a re-run supersedes an earlier one.
- **Data safety:** do **not** pass `--export-prompts` for sensitive/unpublished manuscripts — it
  writes the manuscript text into local reviewer packets. See the tool's `DATA_SAFETY.md`.

Outputs land in `<out>/`: `report.md` (read this), `report.json` (structured findings),
`diagnostics.json`, `llm-run.json`, and `requests/<role>.json`.

## 3. Triage

The CLI emits grounded provider findings; your job is interpretation, not re-derivation. Read
`report.md` and collapse it into the repo's checklist, `MAR_REVIEW_CHECKLIST_<YYYY-MM-DD>.md` next
to the draft, following [references/checklist-template.md](references/checklist-template.md):

- Group recurring findings into **priority themes** `T1…Tn`; an issue raised by ≥2 independent
  roles is the real signal (cross-role convergence), not any single low-confidence finding.
- Give each theme a recommended disposition — `[A]` accept/revise, `[R]` rebut (how), `[P]`
  partial — and the single cheapest check that settles it.
- Call out any finding touching a specific headline number separately; those are weaponizable.
- Flag the target journal's hard gates explicitly (e.g. Genome Biology: data + code availability,
  deposited accessions, complete references).
- When run with `--prior`, list what the revision **closed** so a re-run does not re-flag it.

The six role charges are documented in [references/roles.md](references/roles.md) — use them to read
and group the CLI's output.

## 4. Report back

Give the user a short plain-text summary: finding count by severity, the top themes by
convergence, the suggested adjudication order (settle the highest-convergence hinge first), and the
paths to `report.md` and the checklist. Do **not** edit the manuscript — MAR only reviews;
adjudication (`[A]/[R]/[P]`) is the author's.

## 5. Fallback — Claude-native panel

If the CLI is not installed, or Bedrock is unreachable, and the user wants the review now, perform
the six-role panel yourself: read the whole draft, play each role in
[references/roles.md](references/roles.md) independently, emit grounded findings
(`[severity | role(s)] <section> — <issue + verbatim quote> → <cheap fix>`), and write the same
checklist (§3). State clearly that this was the model-native fallback, not the CLI/Bedrock run, so
the user knows the provenance. Never fabricate a quote, number, or citation.

## 6. Limits

- This anticipates reviewers; it does not fact-check the science or rerun analyses. Convergence,
  not any single finding, is the signal.
- The CLI records a `--journal` string but does not fetch live journal policy — apply the hard
  gates from knowledge of the target journal.
- Keep each run's output under a dated `--out` so revisions are diffable.
