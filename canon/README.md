# canon

A reusable agent skill that bootstraps and operates a **vision-driven, phase-gated
implementation system** in any software project — where the spec docs are written as
**canon**, the binding law every implementation and later change is compliance-checked against.

Extracted from a real project where it prevented the classic AI-coding failure mode: building
everything at once, then discovering that no part works.

## What it sets up

| Piece | Role |
|---|---|
| `Project Vision/` | Canonical numbered spec docs — **the constitution and law** of the project. Code is compliance-checked against them; contradictions halt work until the user decides. |
| `Implementation Plan/` | The project as phases 0..N. Each phase: objective, exact files, automated gates, manual UAT, hard approval checklist, integration notes. |
| The phase loop | Read → compliance pre-check → Implementer builds → Verifier cold-checks → Reporter presents UAT → user accepts → commit. Maker ≠ checker. |
| Compliance checking | Every change compared against the vision, pre- and post-implementation, logged append-only. Contradiction Protocol: halt → document → user decides. |
| `.state/` | Durable memory: per-phase status JSON, independent verification reports, compliance log. Committed to git. "The agent forgets; the repo remembers." |

## Install

Already installed if this folder is at `~/.agents/skills/canon/` (personal, all projects) or
`<project>/.agents/skills/canon/` (one project). The folder name and the `name:` in
`SKILL.md` must both be `canon`.

## Use

* **New / empty project:** say *"set up canon"* — the agent scaffolds the three folders,
  then **guides you through writing the vision** (the constitution — it takes its time here on
  purpose), then decomposes the project into phases.
* **During implementation:** say *"start phase N"* — the fixed loop runs: compliance pre-check,
  Implementer/Verifier/Reporter sub-agents, UAT, commit. No phase N+1 until phase N passes.

## Contents

```
canon/
├── SKILL.md                      — bootstrap (4 stages) + operate modes
├── references/
│   └── vision-interview.md       — Stage B guide: interview rounds, checkability rule, ratification
└── assets/templates/             — the copy-ready kit
    ├── Project Vision/           — canonical-docs contract + vision starter skeleton
    ├── Implementation Plan/      — master index, master prompt, start-phase prompt, phase template
    └── .state/                   — status JSON, compliance log, verification report
```

## Design principles

* **Vision is law.** Vague vision makes every compliance check theater — so bootstrap spends
  real time sharpening it, and the user ratifies it before any phase is written.
* **Phases are contracts.** No phase N+1 until every gate in phase N passes.
* **Maker ≠ checker.** The Verifier checks cold, with no knowledge of the Implementer's rationale.
* **Golden fixtures are sacred.** If a test fails, the code is wrong — never the fixture.
* **No retry loops.** Fix the prompt, not the code; token budgets are hard caps.
* **The user is the final arbiter.** Agents present findings; only the user ratifies, accepts,
  and resolves contradictions.
