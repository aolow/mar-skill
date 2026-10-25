---
name: mar
description: Use the manuscript-review CLI to stress-test a scientific manuscript before submission or revision, then triage its evidence-linked findings. Use for requests to MAR a draft, run an adversarial review, or anticipate reviewer attacks. Supports offline review, explicitly selected provider review, and a labeled model-native fallback.
---

# MAR — Manuscript Adversarial Reviewer

Wrap the `manuscript-review` CLI from
[`aolow/manuscript-adversarial-reviewer`](https://github.com/aolow/manuscript-adversarial-reviewer).
The engine owns extraction, deterministic checks, optional provider calls, grounding,
and version comparisons. This skill produces the final author-facing triage.
Inspect `manuscript-review --version` and `manuscript-review review --help` for the
installed contract; do not infer available flags from this document.

## 1. Establish scope

- Locate the CLI with `command -v manuscript-review`. If unavailable, explain the
  missing dependency and offer installation or the labeled fallback below. Do not
  install or clone an execution copy into a temporary folder.
- Use the draft, supplements, journal and old-version inputs supplied by the user.
  Ask for the draft path only when it cannot be identified from context.
- Honor the selected review mode/provider. Offline review needs no credentials.
  A request to run a Bedrock/OpenAI review authorizes that selected run when the
  manuscript-sharing scope is clear; existing authorization need not be repeated.
  A general request to review a local file alone does not authorize uploading it.
- For Bedrock, resolve the model and region from explicit settings or documented
  environment variables. Do not guess a model, switch accounts/providers, display
  credentials, or launch a paid call to test credentials.
- Keep outputs outside source control, in a durable directory next to the draft:
  `<draft_dir>/mar_reviews/<YYYY-MM-DD_HHMMSS>[_tag]/`. Check that it is unused;
  choose a fresh suffix on collision. Never add `--force` automatically.

## 2. Run and inspect completion

Offline example:

```sh
manuscript-review review "<DRAFT>" --out "<NEW_OUTPUT_DIR>"
```

For an authorized Bedrock run (add `--journal` only if supplied):

```sh
manuscript-review review "<DRAFT>" \
  --llm --provider bedrock --model "<MODEL_OR_PROFILE>" --region "<REGION>" \
  --out "<NEW_OUTPUT_DIR>"
```

- Keep the six default roles unless the user requests a different set. Role
  charges for interpreting output are in [references/roles.md](references/roles.md).
- A separate `--dry-run` output lets the user inspect extraction and exact requests
  without provider calls. It is a preview, not a completed model review.
- In revision mode, add `--prior <OLD_DRAFT>` and, when available,
  `--prior-review <OLD_REPORT_JSON>`, plus version-specific supplements. This uses
  **one comparison request**, not a fresh six-role panel over the revision. Keep
  scope withdrawal, weaker wording, and methodological repair distinct.
- Exit 2 means input/configuration/output failure. Exit 3 means incomplete provider
  review: inspect surviving artifacts and failed roles; do not silently rerun.
  Exit 0 means processing succeeded, not scientific validation or complete coverage.
- Read `report.json` and `report.md`; for provider runs also inspect `diagnostics.json`
  and `llm-run.json`. Record actual mode, engine version, source hashes, requested
  roles, returned/failed roles, item exclusions, and quarantined findings. Never
  hard-code “six-role panel complete.”
- `requests/*.json` is exported for provider runs including dry runs and contains
  manuscript text even without `--export-prompts`. Reports and checklists are also
  sensitive. Keep all artifacts within the authorized local workflow.

## 3. Challenge the review, then synthesize

Before choosing headline themes, use the bounded evidence check in
[references/synthesis.md](references/synthesis.md). This is a triage pass over
existing outputs and supplied source passages; it does not start another panel,
execute analyses, retrieve literature, or change the engine's findings.

Write a fresh `MAR_REVIEW_CHECKLIST_<RUN_TAG>.md` next to the draft, using
[references/checklist-template.md](references/checklist-template.md).

- Open with the template's short **Review at a glance**, written after the evidence
  challenge. Link its assessment, contribution, priorities and next step to the
  detailed review; preserve verification status and material coverage limits.
- Rank at most 3–7 justified themes by impact on the central claim, source support,
  and what can be decided next. Do not pad the list. A strong single-role concern
  can outrank a repeated weak opinion.
- Preserve engine finding IDs, source locations, confidence, disposition, and
  provenance. Group duplicate findings under their canonical ID; retain disagreement.
  Role counts show recurrence, not independent corroboration or higher confidence.
- Keep `needs_review`, external/unverified suggestions, dismissed items, and duplicate
  records distinct from active concerns. Do not promote them by summarizing them.
  New synthesis suggestions must be labeled separately; never edit `report.json`
  to make them look engine-validated.
- Recommend accept/revise, rebut, partial, or verify first, with the decisive check.
  The author's actual adjudication remains open. Do not edit the manuscript.
- State journal requirements as verified only with a supplied authoritative policy
  or a current official source obtained within authorized search scope. Record the
  URL/date. Otherwise say “journal requirement unverified”; model memory is not a gate.
- For revisions, report statuses from `comparison.json` and their source evidence.
  Disappearance alone does not establish resolution or completed analysis.

## 4. Report back

Give actual coverage and active concern counts by severity, the top themes in
revision order, the most useful next check, and links to the report and checklist.
Mention material exclusions or failed roles. Do not equate a clean report or
agreement among roles with readiness for submission.

## 5. Model-native fallback

When the CLI/provider is unavailable and the user wants to proceed in the current
conversation, apply the role lenses in [references/roles.md](references/roles.md)
to the supplied draft and use the same evidence check/checklist. Honor the current
sharing scope. Do not switch providers or upload files to obtain the fallback.

Label it **model-native review; CLI validation not performed**. One model applying
six lenses is not six independent reviewers. Record which sources were actually
read, distinguish observations from hypotheses, and retain exact quotes/locations
where possible. Do not claim engine finding IDs, schema validation, executable
analysis, or verified external literature. Preserve any partial CLI run separately.
