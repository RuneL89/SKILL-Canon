---
name: canon
description: Bootstrap and operate a vision-driven, phase-gated implementation system (Project Vision + Implementation Plan + mandatory compliance loop + .state memory) where the vision docs are written as canon — the binding law that every implementation and later change must comply with. Use whenever the user starts a new or empty project and wants guided help writing the project vision/spec before implementation, says "set up canon", "bootstrap canon", "initiate phases", "start phase N", "run the phase loop", asks for gated/milestone-driven AI implementation with spec compliance checks, or wants a Project Vision / Implementation Plan structure — even if they never say the word "canon".
---

# Canon

Set up and run a **vision-driven, phase-gated implementation system** in a project, where the
spec docs are **canon** — the law every implementation and later change is judged against.

Canon has five parts:

1. `Project Vision/` — canonical, numbered spec docs. **The constitution and law of the project**: every implementation and every later change is compliance-checked against them.
2. `Implementation Plan/` — the project decomposed into phases 0..N. Each phase is a standalone, independently valuable deliverable with automated gates, manual UAT steps, a hard approval checklist, and integration notes.
3. A fixed per-phase loop — read → compliance pre-check → Implementer builds → Verifier cold-checks → Reporter presents UAT → user accepts → commit.
4. Mandatory compliance checking against the vision docs, with a Contradiction Protocol (halt on contradiction; only the user resolves it).
5. `.state/` — durable on-disk memory: per-phase status JSON, independent verification reports, append-only compliance log. "The agent forgets; the repo remembers."

Bundled templates live in `assets/templates/` (paths below are relative to this skill's directory).

## Detect the mode

* **Bootstrap** — the project has no `Implementation Plan/MASTER_IMPLEMENTATION_PROMPT.md` yet, or the user asks to set up / start a new project / write the vision.
* **Operate** — canon is set up in the project and the user asks to start/continue a phase, run gates, do a compliance check, or present UAT.

## Mode 1: Bootstrap

Four stages, in order. **Do not rush Stage B.** The vision docs are the constitution and law
of the project — every implementation, every phase, and every later change is judged against
them. An extra hour sharpening the vision saves days of implementing the wrong thing; vague
vision makes every future compliance check meaningless. Rushing the vision is the most
expensive shortcut in this methodology.

### Stage A — Scaffold

1. **Check before writing.** Look for existing `AGENTS.md`, `Project Vision/`, `Implementation Plan/`, `.state/` in the target project root. If any exist, do not overwrite — ask the user whether to merge or skip.
2. **Copy the kit.** Copy the contents of `assets/templates/` into the project root, preserving folder names (including the dot-folder `.state/`). The kit includes the root `AGENTS.md` — the DOX framework rail.
3. Leave `{{DOUBLE_BRACE}}` placeholders that depend on undecided choices (stack, test command) unfilled for now — Stage B settles them.

### Stage B — Vision (the constitution)

Read `references/vision-interview.md` and follow it. Summary:

1. **Interview the user in rounds** — purpose & users → non-goals → core principles → architecture → domain concepts → quality bar. One round at a time; reflect understanding back after each round.
2. **Every rule must be checkable.** If you cannot write a compliance check for a statement, it is fluff — sharpen it or cut it. "Fast" is fluff; "the CLI answers a query in under 2s on a 10k-page corpus" is law.
3. **Draft the numbered vision docs** (`01_PRODUCT_VISION_AND_ARCHITECTURE.md` + one per durable domain concept). Written for a reader with zero prior context.
4. **Ratification.** Present the drafts and get the user's explicit approval before writing any phase doc. The user is the lawmaker; the agent only drafts. Record later amendments the same way — vision changes are deliberate, never drive-by.

### Stage C — Implementation plan

1. **Decompose into phases.** Write one `Implementation Plan/PHASE_XX_<slug>.md` per phase from `PHASE_TEMPLATE.md`. Rules of thumb:
   * Phase 0 is always infrastructure: repo, build, test framework, and **golden fixtures** (immutable test data — if a test fails, the code is wrong, never the fixture).
   * Each phase must be testable in isolation and valuable even if later phases never ship.
   * Order strictly by dependency; keep phases small (a few hours each).
   * Phases that call an LLM get a token budget hard cap; deterministic phases get `$0`.
2. **Fill the plan tables.** Phase directory, token budgets, fixture strategy, and the phase↔vision mapping in `IMPLEMENTATION_PLAN_MASTER_INDEX.md` and `MASTER_IMPLEMENTATION_PROMPT.md` §4.
3. **Fill remaining placeholders** (`{{PROJECT_NAME}}`, `{{PROJECT_SLUG}}`, `{{TEST_COMMAND}}`, `{{BUILD_COMMAND}}`, budgets) — all known once Stage B has settled the stack.

### Stage D — Memory + commit

1. Seed `.state/phase-0-status.json` from the template and make an initial commit so the memory
layer starts clean.
2. **Root `AGENTS.md` must always exist.** A bootstrapped project is not complete until the project
root contains an `AGENTS.md` whose first heading is `# DOX framework`, from
`assets/templates/AGENTS.md`. Fill its `{{PROJECT_NAME}}` and `{{PROJECT_ONE_LINER}}`
placeholders (known once Stage B settles the vision). If the project already had a root
`AGENTS.md`, merge the DOX framework sections in rather than overwriting existing content.
As phases add durable folders (`src/`, `tests/`, etc.), create child AGENTS.md files per the
framework and keep the root Child DOX Index current.
3. Then tell the user how to start: *"Start Phase 0"*.

## Mode 2: Operate ("start phase N")

Read the project's `Implementation Plan/MASTER_IMPLEMENTATION_PROMPT.md` and
`Implementation Plan/START_PHASE_PROMPT.md` in full and follow them exactly — they are the
canonical loop. The short version:

1. Read the phase doc, the mapped vision docs (mapping: master prompt §4), and the root `AGENTS.md`.
2. Run the compliance pre-check and log it to `.state/compliance-log.md`. Contradiction → **stop**, Contradiction Protocol.
3. Set a verifiable stopping condition from the phase's approval checklist — never a vague goal.
4. Keep the maker and checker separate: Implementer builds and runs tests; Verifier re-checks cold (no knowledge of implementation rationale) and re-runs tests independently; Reporter only presents UAT and results. Sub-agents communicate via `.state/phase-N-status.json`, not conversation.
5. Record every deviation and all LLM spend in the status file. Pause at 80% of the token budget; never retry-loop a failing LLM call — fix the prompt instead.
6. Apply the Golden Rule: do not start phase N+1 until every gate in phase N passes and the user has accepted UAT.

## Why the rules exist (don't skip these)

* **Vision is law** — compliance checks compare code against the vision docs; if the vision is vague, contradictory, or stale, every downstream check is theater.
* **Phases are contracts** — building everything at once is how you get a system where no part works and bugs compound across layers.
* **Maker ≠ checker** — an agent that verifies its own work grades its own homework; the Verifier must check against the spec cold.
* **Fixtures are sacred** — mutable test data makes it impossible to tell whether a failure is in the code or the data.
* **State on disk** — sub-agents are stateless; if progress lives only in context, it is lost on every handoff.
* **The user is the final arbiter** — agents present findings; only the user ratifies the vision, accepts phases, and resolves contradictions between code and vision.
