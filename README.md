# mar-skill

A globally invokable Claude Code skill (`/mar`) that **wraps** the manuscript adversarial reviewer
CLI — package `manuscript-adversarial-review`, source
[`aolow/manuscript-adversarial-reviewer`](https://github.com/aolow/manuscript-adversarial-reviewer).

The CLI does the review (extraction, deterministic checks, six-role Bedrock panel, grounded
findings, Markdown + JSON report). This skill invokes it correctly, keeps output durable, and
triages the result into the dated `MAR_REVIEW_CHECKLIST_*.md` format. It is **not** a reimplementation
— the CLI's `--help` and its own docs are the source of truth.

## Layout

```
SKILL.md                          the skill (name: mar) — wrapper + fallback
references/roles.md               the six role charges (for reading/grouping output)
references/checklist-template.md  the triage checklist format
```

## Install (global, all projects)

```sh
# the review engine (once):
pip install "git+ssh://git@github.com/aolow/manuscript-adversarial-reviewer.git#egg=manuscript-adversarial-review[bedrock]"

# the skill (symlink into the user-level skills dir so /mar works everywhere):
ln -s ~/Stash/mar-skill ~/.claude/skills/mar
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
  --out DRAFT_DIR/mar_reviews/$(date +%F) --force
```

Output: `report.md` + `report.json` under the `--out` dir, plus a triaged
`MAR_REVIEW_CHECKLIST_<date>.md` next to the draft. MAR reviews only — it never edits the
manuscript. If the CLI/Bedrock is unavailable, `/mar` falls back to a Claude-native panel over the
same six roles (SKILL.md §5), labeled as such.
