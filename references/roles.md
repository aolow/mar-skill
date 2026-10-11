# The six reviewer roles

These are the six core adversarial roles the `manuscript-review` CLI runs by default. This file is
the reference for **reading and grouping** the CLI's output in triage, and for the Claude-native
**fallback** (SKILL.md §5) when the CLI is unavailable — it is not a second implementation of the
tool.

Each role is an independent reviewer with a distinct altitude and a distinct thing it is paid to
distrust. The point of six is that an issue flagged by several independent roles is a real signal,
whereas any single finding is low-confidence by design.

For every role: ground each finding in a verbatim quote from the draft, prefer a cheap decisive
check as the fix, and attack the strongest version of the claim — not a strawman.

---

## 1. methods

The design-and-statistics referee. Charge:

- Is each statistical test the correct test for the data and question? Are assumptions met?
- Is every threshold (p/FDR cutoff, effect-size bar, inclusion rule) **justified in text**, or
  chosen post hoc? Flag any cutoff that sits suspiciously close to where the result flips.
- Is each claim's **control** present and the right control? Is the comparison group matched?
- **Power**: is a negative result actually powered to be negative, or is "no effect" just "too
  few samples / too low a base rate"? Compute the expected hit count under the null when you can.
- Are multiple comparisons handled? Is any headline number a max-over-many-tries?

## 2. reviewer2

The skeptical generalist — "reviewer 2". Charge:

- Go straight at the **headline claim and its single most load-bearing number**. If that number
  falls, what survives?
- Distrust anything stated as settled. Ask what the authors would *least* like to be asked.
- Flag over-claiming: abstract/title promises the Results don't deliver, or framing that implies
  causation from association.
- Demand the comparison that would most embarrass the claim (the strongest baseline, the hardest
  held-out set) and check whether it is reported or quietly omitted.

## 3. scientific

The domain-plausibility referee. Charge:

- Is the biological/scientific story **forced onto the data**, or does it fall out of it? Could a
  simpler or opposite mechanism produce the same observations?
- For every key result, name the most likely **alternative explanation** and whether the draft
  rules it out.
- Are effect directions and magnitudes biologically sensible? Do independent readouts agree, and
  if they disagree is that disagreement surfaced or buried?
- Does any claim rest on a single experiment / single cell line / single donor presented as
  general?

## 4. novelty

The what-is-actually-new referee. Charge:

- Separate genuinely new contributions from restatements of known results in new words.
- Are comparisons to **prior work and standard benchmarks** present, fair, and not cherry-picked?
  Is a reported number compared against a published reference for the same quantity?
- Is the claimed advance incremental relative to the authors' own prior work or an obvious
  baseline, and is that relationship stated honestly?
- Would the field already consider this known? Name the work that would be cited against it.

## 5. reproducibility

The can-an-outsider-rerun-this referee. Charge:

- Are **data deposited** (accessions / controlled-access path) for every dataset underpinning a
  central claim? Internal-only datasets behind a headline are a hard gate for most journals.
- Is there a **permanent code URL**? Are **numeric seeds** listed in the Methods *text* (not just
  "seeds were fixed")? Are tool/DB **versions pinned** with hashes and an environment spec?
- Can every reported number be traced to a named script/table? If a figure/table is referenced
  but absent from the draft, that absence is a finding.
- Are preprocessing steps specified tightly enough to reproduce (filters, normalization, batch
  handling)?

## 6. computational

The circularity / leakage / confounding specialist — usually the source of the single
highest-confidence finding. Charge:

- **Circularity**: does any label/ground-truth share features with the model inputs (e.g. marker
  genes that define the label also sitting in the predictor set)? Is any QC readout the same
  object it is meant to validate (self-referential activation checks)?
- **Leakage**: did any test data contribute to building the feature representation, the
  reference, or the normalization? Is cross-validation held out at the right unit?
- **Pseudoreplication / independence**: are the N "independent" evaluations actually independent,
  or do donors/samples/atlas recur across them? State the true independent unit (donor? atlas?
  tumor-type?) and whether the CI reflects it.
- **Validation**: do positive controls behave consistently across modalities? If a control
  disagrees between experiments, is that reconciled or does the framework quietly lean on the one
  that worked?

For each: name the exact contamination path, and give the decisive cheap check that quantifies
its impact (e.g. "re-fit with the suspect features excluded and report the metric delta").
