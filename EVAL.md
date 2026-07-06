# EVAL.md — Scaffold Output Rubric

This rubric grades a single run of the `scaffold-project` agent. It exists to catch **behavioral regressions** — the ways a scaffolding agent quietly degrades from "helpful partner" into "confident bulldozer." Every failure mode below was observed in real use of the source skill; the Core Principles at the top of `skill/SKILL.md` are the fixes, and this rubric is how you verify they held.

Grade each dimension **Good / Acceptable / Wrong**. A run with *any* Wrong is a failed run regardless of how polished the artifacts look — a beautiful charter built on a silently-assumed capacity number is worse than no charter, because it launders a guess into an authoritative-looking plan.

---

## How to use

Run the agent end-to-end against a test project (the fictional [`examples/sample-intake.md`](examples/sample-intake.md) is a ready-made fixture). Score each dimension. For a regression suite, replay the same intake and diff the behavior — the dimensions below are chosen because they are observable in the *transcript*, not just the final files.

---

## Dimension 1 — Iterative review, not a data dump

*Failure mode observed: the agent generated all artifacts and presented them in one enormous message, overwhelming the reviewer and burying the items that actually needed a decision.*

| Grade | Behavior |
|-------|----------|
| **Good** | Phase 7 walks through artifacts **one at a time**, each with a specific question, pausing for feedback before the next. |
| **Acceptable** | Groups 2-3 tightly-related artifacts per turn but still pauses and asks per group. |
| **Wrong** | Dumps all artifacts (or the whole plan) in a single turn with a generic "let me know what you think." |

*Rubric catches the regression by:* checking the transcript for ≥1 explicit pause-and-ask per major artifact. A single mega-message trips it immediately.

---

## Dimension 2 — Never assume a rate or a number

*Failure mode observed: the agent silently baked a capacity/velocity multiplier into the resource plan without ever confirming it, producing a timeline that looked precise but rested on an invented constant.*

| Grade | Behavior |
|-------|----------|
| **Good** | Any capacity rate, velocity, or pass threshold is **asked**, with a clearly-labeled default offered ("I'll use ~60% as a baseline unless you have a number"). The number's provenance is visible in the output. |
| **Acceptable** | Uses a default but **flags it in-line** in the artifact ("assumes 3 effective days/person-week — confirm") so the assumption is auditable. |
| **Wrong** | A rate or threshold appears in the plan with no ask and no flag — presented as fact. |

*Rubric catches the regression by:* grepping the resource plan / capacity model for any numeric constant and checking the transcript for a corresponding confirmation or an in-artifact flag. An unlabeled constant is Wrong.

---

## Dimension 3 — Time-boxed, triaged ingestion

*Failure mode observed: sprawling ingestion — the agent tried to read everything it discovered in full, blowing the time budget and the context window before synthesis even started.*

| Grade | Behavior |
|-------|----------|
| **Good** | Reads user-provided sources in full; discovered sources get a title + first-~500-char triage; a ranked list is presented and the **user chooses** what to deep-read; the rest are indexed as "discovered, not read." |
| **Acceptable** | Time-boxes correctly but under-explains the ranked list, so the user's choice is less informed. |
| **Wrong** | Reads every discovered source in full, or drops discovered sources entirely with no index. |

*Rubric catches the regression by:* checking the source index distinguishes "read" from "discovered, not read," and that Tier-2 reads were gated by a user choice.

---

## Dimension 4 — Explicit about real vs. drafted side effects

*Failure mode observed: ambiguity about whether tickets/pages were actually created or merely templated — the reviewer couldn't tell if work items existed in the tracker or only lived in a CSV.*

| Grade | Behavior |
|-------|----------|
| **Good** | Publishing the charter and creating tracker items are **separate, explicit** asks ("create these now, or leave the template?"). The final summary lists what is *real* vs. *drafted*. |
| **Acceptable** | Asks once about side effects and states the real/drafted split in the summary. |
| **Wrong** | Creates tickets/pages without asking, **or** leaves the reviewer unable to tell what was actually created. |

*Rubric catches the regression by:* checking that no side-effecting tool call happened without a preceding explicit confirmation, and that the final summary has a real-vs-pending breakdown.

---

## Dimension 5 — Type-aware templates

*Failure mode observed: every project was treated as a feature build — an assessment got feature-flag sections and ship dates that made no sense for a deliverable-document project.*

| Grade | Behavior |
|-------|----------|
| **Good** | Project type is set in Phase 1 and the generated charter/plan **omit** irrelevant sections (no feature flags or ship dates for an Assessment; inventory + sunset criteria for a Deprecation). |
| **Acceptable** | Correct type-specific sections present, but a stray feature-build heading survives with an "N/A" note. |
| **Wrong** | Feature-build scaffolding applied wholesale regardless of the stated type. |

*Rubric catches the regression by:* asserting the presence/absence of type-gated sections against the Phase 1 type.

---

## Dimension 6 — Synthesis quality (the actual output)

*Not a failure mode from the retro — this is the baseline "is the artifact any good" check.*

| Grade | Behavior |
|-------|----------|
| **Good** | `problem-space.md` cites sources with links; open questions are answerable and assigned; risks state **consequences** not labels; charter names people not roles; no blank cells (TBD is explicit). |
| **Acceptable** | Mostly cited and specific; a few risks stated as bare labels or a couple of cells left blank. |
| **Wrong** | Generic filler, uncited claims, risks with no consequence, roles instead of names throughout. |

*Rubric catches the regression by:* spot-checking risk cells for a consequence clause and source links in tech-area descriptions.

---

## Scoring

- **All Good:** ship-quality run.
- **Any Acceptable, no Wrong:** usable; note the soft spots for the next iteration.
- **Any Wrong:** failed run. Fix the prompt (the corresponding Core Principle likely needs strengthening) before trusting output.

The point of the rubric is not the final grade — it's that these six dimensions are the *early-warning signals*. A scaffolding agent fails quietly, by being confidently wrong in a well-formatted way. Grading behavior in the transcript, not just the files, is what catches that.
