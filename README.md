# manuscript_adversarial_reviewer (`mar`)

A standalone, globally invokable Claude Code skill that runs a **six-role adversarial peer-review
panel** against an unsubmitted manuscript draft, to anticipate the harshest reviewer objections
before submission.

The model performs the review itself — there is no external runner. It reads the draft, plays six
independent reviewer roles (methods, reviewer2, scientific, novelty, reproducibility,
computational), emits grounded severity-tagged findings, and triages them into priority themes
with recommended dispositions.

## Layout

```
SKILL.md                          the skill (frontmatter name: mar)
references/roles.md               the six role charges
references/checklist-template.md  the output checklist format
```

## Install (global, all projects)

Symlinked into the user-level skills directory so it is invokable as `/mar` from any project:

```sh
mkdir -p ~/.claude/skills
ln -s ~/Stash/manuscript_adversarial_reviewer ~/.claude/skills/mar
```

## Use

```
/mar path/to/DRAFT.md            # optionally name the target journal for hard-gate selection
```

Output: a dated `MAR_REVIEW_CHECKLIST_<YYYY-MM-DD>.md` written next to the draft, plus a short
summary. MAR reviews only — it never edits the manuscript; adjudication (`[A]/[R]/[P]`) is left to
the author.

## Origin

Encodes the methodology first used on the CAF-state manuscript
(`~/Stash/caf_state_manuscript/paper/MAR_REVIEW_CHECKLIST_2026-10-07.md`): grounded findings,
severity tiers, and cross-role convergence as the primary signal.
