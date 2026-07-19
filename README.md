# Canon

Harness engineering and loop engineering for AI coding agents, packaged as a single skill.
You write the spec once, as binding law, and the agent has to answer to it on every change.

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

## Install

Copy the `canon/` folder into your agent's skills directory:

```bash
git clone https://github.com/RuneL89/SKILL-Canon.git
cp -r SKILL-Canon/canon ~/.agents/skills/      # personal: available in all projects
# or into <project>/.agents/skills/            # project-local: one project only
```

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
| `Implementation Plan/` | The project as phases 0..N. Each phase has an objective, exact files, automated gates, manual UAT, and a hard approval checklist. |
| The phase loop | Read, pre-check, implement, cold-verify, UAT, commit. The builder and the checker are never the same agent. |
| Compliance checking | Every change compared against the vision, before and after implementation, logged append-only. |
| `.state/` | Per-phase status JSON, independent verification reports, compliance log. Committed to git. |

## Design principles

* Vision is law. Vague vision makes every compliance check theater, so bootstrap spends real
  time sharpening it, and you ratify it before any phase is written.
* Phases are contracts. No phase N+1 until every gate in phase N passes.
* Maker is not checker. The verifier checks cold, with no knowledge of the implementer's
  rationale.
* Golden fixtures are sacred. If a test fails, the code is wrong, never the fixture.
* No retry loops. Fix the prompt, not the code. Token budgets are hard caps.
* The user is the final arbiter. Agents present findings; only you ratify, accept, and
  resolve contradictions.

## Repo layout

```
canon/                          <- the skill (this folder is what you install)
├── SKILL.md                    — bootstrap (4 stages) + operate modes
├── references/
│   └── vision-interview.md     — the vision interview: rounds, checkability rule, ratification
└── assets/templates/           — the copy-ready project kit
    ├── Project Vision/         — canonical-docs contract + vision starter skeleton
    ├── Implementation Plan/    — master index, master prompt, start-phase prompt, phase template
    └── .state/                 — status JSON, compliance log, verification report
```

## License

MIT. See [LICENSE](LICENSE).
