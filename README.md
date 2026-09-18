# Canon

Harness engineering and loop engineering for AI coding agents, packaged as a single skill.
You write the spec once, as binding law, and the agent has to answer to it on every change.

Canon is built for ZCode. The three roles in its loop are real ZCode subagents, and the
model each one runs on is pinned in the subagent definition, so the harness enforces it —
no prompting required.

## The problem

Give an AI agent a big project and it does what agents do. It builds everything at once,
drifts off the spec, and hands you a system where no single part actually works. Context
windows reset, the reasoning behind decisions evaporates, and the agent ends up grading its
own homework.

Prompting harder doesn't fix this. The environment the agent works in is the problem, so
that's what Canon changes.

## What it does

Two ideas, combined.

Harness engineering: instead of hoping the agent remembers your intent, you install a fixed
structure around it. Vision docs that act as the law of the project. A plan that splits the
work into gated phases. A `.state/` folder that persists across sessions and context resets.
The agent forgets; the repo remembers.

Loop engineering: every phase runs the same loop. Read the spec, run a compliance pre-check,
implement, get cold-checked by a separate verifier that knows nothing about the
implementation, present UAT, commit. Phase N+1 doesn't start until every gate in phase N
passes and you've accepted the result. The agent can't freelance because the loop doesn't
leave room for it.

And when the code contradicts the vision, work stops. The agent documents the contradiction
and waits. Only you get to resolve it.

## The DOX framework

The kit also installs DOX, a small framework for keeping project documentation alive. The
root `AGENTS.md` is the project-wide contract, and any folder that becomes a durable
boundary (`src/`, `tests/`, and so on) gets its own child `AGENTS.md` with local rules.
Before editing, the agent reads the chain of docs from the root down to whatever it's
touching. After editing, it must update them. A child index in each doc records what every
folder is for.

This fills a gap the other pieces don't cover. The vision docs say what the project must
be, and `.state/` remembers where the work stands, but neither tells the agent how to
behave inside each part of the tree. DOX puts the operating rules next to the code they
govern, and the update-after-editing rule means the docs can't quietly rot between phases.
When a fresh agent, or a fresh context window, picks the project up in month three, it can
reconstruct how everything works from the docs alone.

## Built for ZCode

The loop spawns three named subagents: `canon-implementer`, `canon-verifier`, and
`canon-reporter`. Each is a ZCode subagent definition — a markdown file with a `model:` line
in its frontmatter. The implementer and reporter run on glm-5.3-flash, the verifier on
deepseek-v4-pro. ZCode reads the pin and launches the agent on that model every time, so
"always verify with a different model" is a property of the setup, not an instruction the
agent might forget.

Two more rules ride on the same mechanism. Vision docs are only written in a GLM-5.3
session — if you start bootstrap on another model, canon halts and asks you to switch
before it drafts any law. And if a named subagent is missing or its pinned model can't
run, canon stops and asks instead of quietly substituting whatever is available.

## Install

Copy the `canon/` folder into your agent's skills directory:

```bash
git clone https://github.com/RuneL89/SKILL-Canon.git
cp -r SKILL-Canon/canon ~/.agents/skills/      # personal: available in all projects
# or into <project>/.agents/skills/            # project-local: one project only
cp SKILL-Canon/canon/agents/*.md ~/.zcode/cli/agents/   # the pinned subagents
```

The last line installs the three subagent definitions. They show up under Settings →
Subagents, and canon won't spawn unpinned roles without them.

You can also download the repo as a zip and drop `canon/` into one of those locations
manually. The folder name and the `name:` field in `SKILL.md` must both be `canon` (they
are, so a plain copy works).

## Use

New or empty project: say "set up canon". The agent scaffolds `Project Vision/`,
`Implementation Plan/`, and `.state/`, then interviews you to write the vision. It takes its
time here on purpose. An hour sharpening the vision saves days of building the wrong thing.
Once you ratify the vision, it decomposes the project into phases.

During implementation: say "start phase N". The loop runs: compliance pre-check,
implementer/verifier/reporter sub-agents communicating through on-disk state, UAT, commit.
Nothing moves forward until you accept the current phase.

## What's inside

| Piece | Role |
|---|---|
| `Project Vision/` | The numbered spec docs, treated as law. Code is compliance-checked against them; contradictions halt work until you decide. |
| `Implementation Plan/` | The project as phases 0..N. Each phase has an objective, exact files, automated gates, UAT (split: verifier-run mechanical checks + human-verifiable steps), and a hard approval checklist. |
| The phase loop | Read, pre-check, implement, cold-verify, UAT, commit. The builder and the checker are never the same agent. |
| Compliance checking | Every change compared against the vision, before and after implementation, logged append-only. |
| `.state/` | Per-phase status JSON, independent verification reports, compliance log. Committed to git. |
| DOX (`AGENTS.md`) | A root contract plus per-folder child docs that the agent must read before editing and update after. Keeps the project's rules alive between sessions. |

## Design principles

* Vision is law. Vague vision makes every compliance check theater, so bootstrap spends real
  time sharpening it, and you ratify it before any phase is written.
* Phases are contracts. No phase N+1 until every gate in phase N passes.
* Maker is not checker. The verifier checks cold, with no knowledge of the implementer's
  rationale.
* Models are pinned. The implementer and reporter run glm-5.3-flash, the verifier runs
  deepseek-v4-pro, and ZCode enforces both — a missing pin stops the loop rather than
  degrading it.
* Golden fixtures are sacred. If a test fails, the code is wrong, never the fixture.
* No retry loops. Fix the prompt, not the code. Token budgets are hard caps.
* The user is the final arbiter. Agents present findings; only you ratify, accept, and
  resolve contradictions.

## Repo layout

```
canon/                          <- the skill (this folder is what you install)
├── SKILL.md                    — bootstrap (4 stages) + operate modes
├── agents/                     — the three model-pinned subagent definitions
├── references/
│   └── vision-interview.md     — the vision interview: rounds, checkability rule, ratification
└── assets/templates/           — the copy-ready project kit
    ├── AGENTS.md               — the DOX framework rail (root contract + child-doc rules)
    ├── Project Vision/         — canonical-docs contract + vision starter skeleton
    ├── Implementation Plan/    — master index, master prompt, start-phase prompt, phase template
    └── .state/                 — status JSON, compliance log, verification report
```

## License

MIT. See [LICENSE](LICENSE).
