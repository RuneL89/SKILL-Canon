# Vision Interview Guide — Writing the Constitution

The `Project Vision/` documents are the constitution and law of the project. Every phase, every
implementation, and every later change is compliance-checked against them. This guide is how
you (the agent) lead the user through writing them during Stage B of bootstrap.

**Spend the time.** A vision doc written in five minutes produces compliance checks that are
theater. A vision doc written over a careful hour produces a project where an AI agent can
implement phase after phase without drifting. The user has already decided the time is worth
it — that is why this stage exists.

## Posture

* **The user is the lawmaker; you are the drafter.** You ask, probe, sharpen, and write. The
  user decides. Never invent law and present it as the user's intent — propose, then confirm.
* **One round at a time.** Do not dump all questions at once. Finish a round, reflect the
  answers back in your own words, confirm, then move on.
* **Push back.** If an answer is vague, contradictory, or uncheckable, say so and dig. Being
  agreeable here is how bad law gets written.
* **Write for the stranger.** Every doc must make sense to a reader (human or agent) with zero
  prior context. If a sentence only means something to people in this conversation, rewrite it.

## The Checkability Rule (apply constantly)

Every statement that goes into a vision doc must be something a future compliance check can
judge as COMPLIANT or CONTRADICTION. Translate fluff into law:

| Fluff (reject) | Law (accept) |
|---|---|
| "The app should be fast" | "A search returns in under 2s on a 10k-document corpus" |
| "Clean architecture" | "No module under `src/ui/` imports from `src/db/`" |
| "Use AI where sensible" | "The LLM writes prose only; folder structure and counts are always produced by deterministic code" |
| "Secure" | "All secrets come from env vars; no secret is ever written to disk or logs" |
| "User-friendly" | "Every core workflow is reachable from the TUI main menu in ≤3 keystrokes" |

If a statement cannot be sharpened into something checkable, either it is a value statement
(move it to a short "Principles" preamble, clearly marked as guidance, not law) or it should
be cut.

## The Rounds

Adapt depth to the project; skip nothing without a reason. For each round: ask → probe the
answers → reflect back a summary → get confirmation → note what will go into which doc.

### Round 1 — Purpose and users

* What does this project do? What problem does it solve?
* Who uses it? What can they do when it is finished that they cannot do today?
* What does "done" look like for v1 — the smallest complete thing?

### Round 2 — Non-goals

* What does this project explicitly NOT do? (Adjacent features people will assume are in scope.)
* What will you deliberately never add? (Scope-creep tripwires — future phases will be tempted.)
* Non-goals are law too: a phase doc that sneaks one in fails its compliance check.

### Round 3 — Core principles (the inviolable articles)

* What must ALWAYS be true of the system? (e.g. "every claim carries a citation",
  "the CLI works offline", "deterministic code owns the file layout")
* What must NEVER happen? (e.g. "the LLM never writes file paths", "no retry loops that burn tokens")
* Which trade-offs are decided in advance? (e.g. "quality over cost for X", "correctness over speed for Y")
* Target: a short numbered list of articles, each checkable. These get cited in compliance logs.

### Round 4 — Architecture and stack

* Major components/layers and the data flow between them (a diagram in ASCII is fine).
* For each component: deterministic code, LLM-driven, or user-driven — and why. This split is
  the single most valuable architectural decision for AI-built systems; get it explicit.
* Stack: language, runtime, test framework, build/test commands. (This settles the
  `{{TEST_COMMAND}}` / `{{BUILD_COMMAND}}` placeholders — do not leave Stage B without them.)
* Repository layout: the durable folder structure and what lives where.

### Round 5 — Domain concepts

* List the durable domain concepts (usually 3–7). Each gets its own numbered vision doc
  (`02_<concept>.md`, `03_<concept>.md`, …) written in Stage B's drafting step.
* For each concept: what it is, its rules and invariants, its data shapes, and how other
  concepts may/may not interact with it.

### Round 6 — Quality bar

* How is correctness judged? What are the golden fixtures — the immutable test data that
  makes failures unambiguous ("if a test fails, the code is wrong, never the fixture")?
* What must always be tested automatically vs. what is acceptable as human-verifiable UAT?
  (Everything machine-checkable becomes a Verifier pre-UAT check; only perceptual judgments
  and acceptance decisions reach the user.)
* Performance/cost limits that count as law (these become token budgets and gate criteria).

## Drafting

After the rounds, write the docs:

1. `01_PRODUCT_VISION_AND_ARCHITECTURE.md` from the starter template — purpose, principles
   (Round 3 articles verbatim), architecture, layout, non-goals, glossary.
2. One `NN_<concept>.md` per Round 5 concept. Self-contained; a stranger could implement from it.
3. Fill `Project Vision/AGENTS.md` ownership list with the actual doc names.
4. Fill the Document ID headers (`{{PROJECT_SLUG}}-VISION-NNN`, version `1.0.0`, date).

Quality bar for the drafts:

* Every Round 3 article appears verbatim in `01` and is numbered/citable.
* Every rule is checkable (apply the Checkability Rule table above to your own draft).
* No forward references to conversations — only to other vision docs by name and section.
* No contradictions between docs. Cross-check twice; contradictions in the constitution make
  the Contradiction Protocol fire constantly later.

## Ratification

Present the complete set to the user:

* A summary of the articles (Round 3) and the doc list.
* The full drafts for review.
* Explicit ask: "Do you ratify this as the law of the project? Anything to strike or amend?"

Do not proceed to Stage C (implementation plan) until the user explicitly ratifies. If they
amend, update the docs and re-present — amendments are normal and cheap now, expensive later.

## Amendments after ratification

The constitution can change, but deliberately:

* Implementation contradicts vision → Contradiction Protocol (halt; user decides: accept =
  amend the vision doc in the same pass, or reject = roll back the code).
* User requests a change → update the vision doc directly, note it in the compliance log.
* Never edit vision docs to match code silently. The day the docs start following the code,
  they stop being law.
