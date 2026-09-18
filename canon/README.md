# canon

A reusable agent skill that bootstraps and operates a **vision-driven, phase-gated
implementation system** in any software project — where the spec docs are written as
**canon**, the binding law every implementation and later change is compliance-checked against.

**Built for ZCode.** The loop's three sub-agent roles are named ZCode subagent definitions,
and each carries a model pin that ZCode itself enforces at spawn time. The agent definitions
ship in `agents/` — copy them to `~/.zcode/agents/` during install.

Extracted from a real project where it prevented the classic AI-coding failure mode: building
everything at once, then discovering that no part works.

## What it sets up

| Piece | Role |
|---|---|
| `Project Vision/` | Canonical numbered spec docs — **the constitution and law** of the project. Code is compliance-checked against them; contradictions halt work until the user decides. |
| `Implementation Plan/` | The project as phases 0..N. Each phase: objective, exact files, automated gates, UAT (split: Verifier-run mechanical checks + human-verifiable acceptance), hard approval checklist, integration notes. |
| The phase loop | Read → compliance pre-check → Implementer builds → Verifier cold-checks + runs mechanical UAT checks → Reporter presents human-verifiable UAT only → user accepts → commit. Maker ≠ checker. |
| Compliance checking | Every change compared against the vision, pre- and post-implementation, logged append-only. Contradiction Protocol: halt → document → user decides. |
| `.state/` | Durable memory: per-phase status JSON, independent verification reports, compliance log. Committed to git. "The agent forgets; the repo remembers." |

## Install

Already installed if this folder is at `~/.agents/skills/canon/` (personal, all projects) or
`<project>/.agents/skills/canon/` (one project). The folder name and the `name:` in
`SKILL.md` must both be `canon`.

The model pins need the three ZCode subagent definitions installed too:

```bash
cp agents/*.md ~/.zcode/agents/
```

They show up under Settings → Subagents. Without them, canon halts and asks rather than
spawning unpinned roles.

## Use

* **New / empty project:** say *"set up canon"* — the agent scaffolds the three folders,
  then **guides you through writing the vision** (the constitution — it takes its time here on
  purpose), then decomposes the project into phases.
* **During implementation:** say *"start phase N"* — the fixed loop runs: compliance pre-check,
  Implementer/Verifier/Reporter sub-agents (Verifier runs every mechanical UAT check first;
  the user sees only human-verifiable steps), UAT, commit. No phase N+1 until phase N passes.

## Contents

```
canon/
├── SKILL.md                      — bootstrap (4 stages) + operate modes
├── agents/                       — the three model-pinned ZCode subagent definitions
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
* **Models are pinned.** Implementer and Reporter run on glm-5.3-flash, the Verifier on
  deepseek-v4-pro — enforced by named agent definitions, not prompt text; vision law is only
  written in a GLM-5.3 session. Missing pin → halt and ask, never substitute.
* **UAT is split.** Machine-checkable steps are Verifier pre-UAT checks, evidenced before
  presentation; the user gets only perceptual judgments and decisions.
* **Golden fixtures are sacred.** If a test fails, the code is wrong — never the fixture.
* **No retry loops.** Fix the prompt, not the code; token budgets are hard caps.
* **The user is the final arbiter.** Agents present findings; only the user ratifies, accepts,
  and resolves contradictions.
