# mar-skill

A globally invokable Claude Code skill (`/mar`) that **wraps** the manuscript adversarial reviewer
CLI — package `manuscript-adversarial-review`, source
[`aolow/manuscript-adversarial-reviewer`](https://github.com/aolow/manuscript-adversarial-reviewer).

The CLI does the review (extraction, deterministic checks, six-role Bedrock panel, grounded
findings, Markdown + JSON report). This skill invokes it correctly, keeps output durable, and
triages the result into the dated `MAR_REVIEW_CHECKLIST_*.md` format. It is **not** a reimplementation
— the CLI's `--help` and its own docs are the source of truth.

## Real-paper showcases

Two worked examples in the engine repository illustrate the evidence challenge
and final triage, including counterevidence and concerns withdrawn after checking:

- **[Single-cell foundation models](https://github.com/aolow/manuscript-adversarial-reviewer/tree/184abd292f500ac96b86d34d70323d19716186d5/showcases/single-cell-foundation-models):**
  respect a benchmark's scope and recognize objections the authors already address.
- **[Virtual Biotech](https://github.com/aolow/manuscript-adversarial-reviewer/tree/184abd292f500ac96b86d34d70323d19716186d5/showcases/virtual-biotech):**
  distinguish observational evidence from mechanism and validated treatment selection.

Each combines an actual offline CLI run with a separately authored model-native
review. These are not six-role Bedrock runs or independently human-validated
reviews. Every engine lead is preserved and triaged; source versions, exact
evidence anchors and reproduction instructions are included. The Virtual Biotech
case reviews its February 2026 preprint, not the later journal article.

[Reproduce both examples](https://github.com/aolow/manuscript-adversarial-reviewer/tree/184abd292f500ac96b86d34d70323d19716186d5/showcases).

## Layout

```
SKILL.md                          the skill (name: mar) — wrapper + fallback
references/roles.md               the six role charges (for reading/grouping output)
references/checklist-template.md  the triage checklist format
references/synthesis.md           evidence challenge before final triage
```

## Install (global, all projects)

Clone both into `~/Stash_gh/` (the personal aolow-profile tree) via the `github-aolow` SSH alias
(`~/.ssh/config` Host → github.com with the aolow key):

```sh
# the review engine — editable install so the CLI stays current on `git pull`:
git clone git@github-aolow:aolow/manuscript-adversarial-reviewer.git ~/Stash_gh/manuscript-adversarial-reviewer
cd ~/Stash_gh/manuscript-adversarial-reviewer && pip install -e ".[bedrock]"

# the skill — symlink into the user-level skills dir so /mar works everywhere:
git clone git@github-aolow:aolow/mar-skill.git ~/Stash_gh/mar-skill
ln -s ~/Stash_gh/mar-skill ~/.claude/skills/mar
```

Bedrock needs `MANUSCRIPT_REVIEW_MODEL` (or `--model`) and AWS creds (`AWS_PROFILE` / `AWS_REGION`).

## Use

```
/mar path/to/DRAFT.md                 # six-role Bedrock review + triage
```

Direct CLI equivalent:

```sh
manuscript-review review DRAFT.md --llm --provider bedrock \
  --model "$MANUSCRIPT_REVIEW_MODEL" --journal "Genome Biology" \
  --out "DRAFT_DIR/mar_reviews/$(date +%Y-%m-%d_%H%M%S)"
```

Output: `report.md` + `report.json` under the `--out` dir, plus a triaged
`MAR_REVIEW_CHECKLIST_<date>.md` next to the draft. MAR reviews only — it never edits the
manuscript. If the CLI/Bedrock is unavailable, `/mar` falls back to a Claude-native panel over the
same six role lenses (SKILL.md §5), labeled as such, when the user chooses to proceed.

Choose an unused output directory for every run; never overwrite earlier reviews by
default. Provider calls require authorization to share the manuscript with that
provider. Offline review remains available without a cloud account.

Final triage now checks each major concern against source evidence, counterevidence,
and a feasible discriminating next step. Role agreement does not increase confidence.
Coverage, validation exclusions, and unresolved external claims remain visible.
This workflow is informed by Virtual Biotech and Virtual Lab, but its effect on
scientific review quality still needs blinded human evaluation; see
[the synthesis reference](references/synthesis.md).
