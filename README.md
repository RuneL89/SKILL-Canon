# Canon

**Harness engineering + loop engineering for AI coding agents.** Canon stops agents from
going rogue by making the spec *law* — and forcing every implementation to answer to it.

## The problem

Give an AI agent a big project and it does what AI agents do: charges ahead, builds
everything at once, drifts off-spec, and hands you a system where no part actually works.
Context windows reset, rationale evaporates, and the agent happily grades its own homework.
Prompting harder doesn't fix this — the *environment* the agent operates in is the problem.

## The fix

Canon is a reusable agent skill that restructures that environment:

* **Harness engineering** — instead of hoping the agent remembers your intent, you install a
  binding structure around it: canonical vision docs, a phase-gated implementation plan, and
  durable on-disk memory (`.state/`) that survives every context reset. *The agent forgets;
  the repo remembers.*
* **Loop engineering** — every phase runs the same fixed loop: read → compliance pre-check →
  Implementer builds → Verifier cold-checks (maker ≠ checker) → UAT → user accepts → commit.
  No phase N+1 until every gate in phase N passes. The agent can't freelance, because the
  loop doesn't let it.
* **Vision is law** — the `Project Vision/` docs are the constitution of the project. Every
  change is compliance-checked against them, pre- and post-implementation. Contradiction
  between code and canon? Work **halts** — only the human resolves it. The agent presents
  findings; it never gets the final vote.

The result: an agent that ships small, verified, spec-compliant increments instead of a
rogue monolith you have to untangle.

## Install

Copy the `canon/` folder into your agent's skills directory:

```bash
git clone https://github.com/RuneL89/SKILL-Canon.git
cp -r SKILL-Canon/canon ~/.agents/skills/      # personal: available in all projects
# or into <project>/.agents/skills/            # project-local: one project only
```

Or download the repo as a zip and drop `canon/` into one of those locations manually.
The folder name and the `name:` field in `SKILL.md` must both be `canon` (they are).

## Use

* **New / empty project:** say *"set up canon"* — the agent scaffolds `Project Vision/`,
  `Implementation Plan/`, and `.state/`, then **guides you through writing the vision**
  (it deliberately takes its time here — an hour sharpening the vision saves days of
  implementing the wrong thing), then decomposes the project into phases.
* **During implementation:** say *"start phase N"* — the fixed loop runs: compliance
  pre-check, Implementer/Verifier/Reporter sub-agents communicating through on-disk state,
  UAT, commit. Nothing merges until you accept it.

## What's inside

| Piece | Role |
|---|---|
| `Project Vision/` | Canonical numbered spec docs — **the law** of the project. Code is compliance-checked against them; contradictions halt work until you decide. |
| `Implementation Plan/` | The project as phases 0..N. Each phase: objective, exact files, automated gates, manual UAT, hard approval checklist, integration notes. |
| The phase loop | Read → compliance pre-check → Implementer builds → Verifier cold-checks → UAT → you accept → commit. Maker ≠ checker. |
| Compliance checking | Every change compared against the vision, logged append-only. Contradiction Protocol: halt → document → human decides. |
| `.state/` | Durable memory: per-phase status JSON, independent verification reports, compliance log. Committed to git. |

## Design principles

* **Vision is law.** Vague vision makes every compliance check theater — so bootstrap spends
  real time sharpening it, and you ratify it before any phase is written.
* **Phases are contracts.** No phase N+1 until every gate in phase N passes.
* **Maker ≠ checker.** The Verifier checks cold, with no knowledge of the Implementer's rationale.
* **Golden fixtures are sacred.** If a test fails, the code is wrong — never the fixture.
* **No retry loops.** Fix the prompt, not the code; token budgets are hard caps.
* **The user is the final arbiter.** Agents present findings; only you ratify, accept, and
  resolve contradictions.

## Repo layout

```
canon/                          ← the skill (this folder is what you install)
├── SKILL.md                    — bootstrap (4 stages) + operate modes
├── references/
│   └── vision-interview.md     — the vision interview: rounds, checkability rule, ratification
└── assets/templates/           — the copy-ready project kit
    ├── Project Vision/         — canonical-docs contract + vision starter skeleton
    ├── Implementation Plan/    — master index, master prompt, start-phase prompt, phase template
    └── .state/                 — status JSON, compliance log, verification report
```

## License

MIT — see [LICENSE](LICENSE).
