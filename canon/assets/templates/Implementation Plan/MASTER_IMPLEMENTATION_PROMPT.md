# {{PROJECT_NAME}} — Master Implementation Prompt

**Document ID:** `{{PROJECT_SLUG}}-MASTER-PROMPT`
**Version:** 1.0.0
**Status:** Canonical
**Date:** {{DATE}}

---

## Overview

This document is the standard prompt given to the AI coding agent at the beginning of each
implementation phase. It establishes the loop framework, the sub-agent architecture, and the
compliance requirements.

**The agent must read this prompt in full before starting any phase.**

---

## 1. The Loop Framework

You are not writing code in a single pass. You are operating within a **goal-driven loop**
that continues until the phase is complete, tested, and validated.

### 1.1 The Five Loop Primitives

1. **Verifiable goal** — Define a verifiable stopping condition and work until it is met.
   After every turn, check whether the goal is achieved. (If your agent harness has a goal/loop
   skill, use it; otherwise the status file in §1.4 carries the goal.)
2. **Isolation** — Each sub-agent operates in its own isolated context. The main agent
   coordinates; sub-agents execute.
3. **Knowledge before action** — Project knowledge lives in the vision documents. You read them
   before acting. You do not guess.
4. **Sub-agents** — The maker and the checker are separate. One sub-agent implements; another
   verifies. They do not grade their own homework.
5. **State / Memory** — Progress is tracked on disk, not in context. The agent forgets; the
   repo remembers.

### 1.2 The Goal

The goal for a phase is a verifiable stopping condition, not a vague instruction.

**Good goal:**
```
All {X} technical gates in PHASE_{N}_*.md pass ({TEST_COMMAND} is green),
all {Y} UAT steps are documented with expected output,
and the implementation matches the specification in {VISION_DOC}.
Total LLM cost is under ${BUDGET}.
```

**Bad goal:**
```
Implement the component.
```

### 1.3 Sub-Agent Architecture

You must use three sub-agents, each with a distinct role:

| Sub-agent | Role | When Invoked |
|---|---|---|
| **Implementer** | Writes code, runs tests, fixes bugs | For every implementation task |
| **Verifier** | Checks compliance against vision docs, runs tests independently | After Implementer claims a gate is passed |
| **Reporter** | Summarizes results, presents UAT steps to user, logs compliance | After Verifier confirms phase is complete |

**Rules for sub-agents:**
- The Implementer does not know if the Verifier will approve. It writes the best code it can.
- The Verifier does not know the Implementer's rationale. It checks against the spec cold.
- The Reporter does not modify code. It presents findings to the user.
- Sub-agents run in isolated contexts. They do not share reasoning. Only final results pass
  between them.

### 1.4 Dynamic Prompting Between Sub-agents

Sub-agents communicate via structured status files, not conversation.

**Status file format:** `.state/phase-{N}-status.json`
```json
{
  "phase": "{N}",
  "status": "in-progress",
  "goal": "{verifiable stopping condition}",
  "lastAction": "{what just happened}",
  "gatesPassed": ["{N}.1", "{N}.2"],
  "gatesFailed": [],
  "gatesPending": ["{N}.3"],
  "llmCost": "$0.00",
  "tokenBudgetRemaining": "${BUDGET}",
  "blocker": null,
  "deviations": [],
  "nextAction": "{what the next agent should do}"
}
```

When a sub-agent completes, it writes the status file. The next sub-agent reads it and decides
what to do. This is dynamic prompting: the prompt for the next agent is generated based on the
current state.

---

## 2. The Compliance Rule (MANDATORY)

Before any code is written, before any test is run, you must check the implementation against
the Project Vision documents.

### 2.1 The Compliance Checklist

1. **Identify the relevant vision document(s).** Every change touches at least one vision
   document. Find it.
2. **Read the relevant section(s).** Do not skim. Read the full section.
3. **Compare implementation against vision.** Does it match? Does it contradict? Does it extend?
4. **Document the comparison.** Write a compliance note.
5. **If contradiction found, STOP.** Trigger the Contradiction Protocol.

### 2.2 The Contradiction Protocol

When implementation contradicts vision:
1. **Halt.** Stop all work.
2. **Document.** Create a contradiction report.
3. **Present to user.** Two options: Accept (update vision) or Reject (roll back code).
4. **Record decision.** Log in the compliance log and commit message.

**You do not proceed without user decision.**

---

## 3. Standard Phase Prompt Template

At the beginning of each phase, present this prompt to the agent:

```
# PHASE {N}: {PHASE_NAME}

## Your Goal

Implement Phase {N} of {{PROJECT_NAME}} per the implementation plan and vision documents.

## Documents You Must Read

1. **This Phase Document:** `Implementation Plan/PHASE_{N}_{phase_slug}.md`
   - Read the full document before writing any code.
   - Understand every technical gate and UAT step.
   - Note the LLM token budget. Do not exceed it.

2. **Relevant Vision Documents:** (all in `Project Vision/`)
{vision_docs_list}

3. **Project AGENTS.md:** `AGENTS.md` (project root)
   - The compliance rule and contradiction protocol are mandatory.

## Your Task

1. Read the phase document and vision documents.
2. Run the compliance check. If any contradiction is found, STOP and report it.
3. Implement the code for this phase.
4. Run the technical gates (automated tests).
5. Document the UAT steps (manual verification steps for the user).
6. Have the Verifier sub-agent check compliance against vision docs.
7. Have the Reporter sub-agent present results to the user.

## Sub-Agent Invocation

### Implementer
Task: Implement Phase {N} per the specification. Write all code, tests, and fixtures.
Run tests until they pass. Stay within the token budget. Write the status file when done.

### Verifier
Task: Read the phase document and vision documents. Check that the implementation matches
the spec. Run the tests independently. Verify no contradictions exist. Report pass/fail
for each gate with evidence to `.state/phase-{N}-verification.md`.

### Reporter
Task: Read the status file and verifier report. Present a summary to the user: what was
implemented, which gates passed, which UAT steps to perform, and what the expected results
are. Do not modify code.

## Compliance Log

Log every compliance check to `.state/compliance-log.md`:

[YYYY-MM-DD HH:MM] Phase {N} Compliance Check
  Changed: {files changed}
  Vision Docs Checked: {list}
  Sections Checked: {list}
  Result: {COMPLIANT | CONTRADICTION | EXTENSION}
  Checked By: {agent name}

## Token Budget

This phase has a hard token budget of ${BUDGET}. If you hit 80% of the budget, pause and
report to the user. Do not exceed the budget.

## Stopping Condition

The phase is complete when:
- All technical gates pass ({TEST_COMMAND} is green).
- All UAT steps are documented with expected output.
- The compliance log shows no unresolved contradictions.
- The status file shows all gates passed and no blockers.
```

---

## 4. Phase-Specific Vision Document Mapping

Phase documents live in `Implementation Plan/`; vision documents live in `Project Vision/`.

<!-- One row per phase. The mapping is what makes compliance checks concrete —
     "check against the vision" without section pointers becomes skimming. -->

| Phase | Phase Document | Vision Documents to Check | Why |
|---|---|---|---|
| 0 | `PHASE_00_infrastructure.md` | `01_PRODUCT_VISION_AND_ARCHITECTURE.md` | Overall architecture, philosophy |
| {N} | `PHASE_{N}_{phase_slug}.md` | `{VISION_DOC}` | {WHY} |

---

## 5. The Reporter's UAT Presentation Format

When presenting UAT steps to the user, the Reporter must use this format:

```
╔══════════════════════════════════════════════════════════════╗
║  PHASE {N} UAT — {Phase Name}                                ║
╠══════════════════════════════════════════════════════════════╣
║  Status: READY FOR USER TESTING                              ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  UAT 1: {Step Name}                                          ║
║  ─────────────────                                           ║
║  Command: {exact command to run}                             ║
║  Expected: {what the user should see}                        ║
║  How to verify: {how to confirm it worked}                   ║
║                                                              ║
║  UAT 2: ...                                                  ║
║                                                              ║
╠══════════════════════════════════════════════════════════════╣
║  Technical Gates: {X}/{Y} PASSED                             ║
║  LLM Cost: ${amount} / ${budget}                             ║
║  Compliance: {COMPLIANT | CONTRADICTION}                     ║
╚══════════════════════════════════════════════════════════════╝
```

The user runs the UAT steps manually and reports back. If any UAT fails, the loop continues.

---

## 6. Critical Rules

1. **Read before writing.** Always read the phase document and vision documents before writing code.
2. **Check compliance.** Every change must be checked against the vision. No exceptions.
3. **Use sub-agents.** Implementer writes, Verifier checks, Reporter presents. Never combine roles.
4. **Stay in budget.** Hard token caps per phase. Pause at 80% and report.
5. **Log everything.** Status files, compliance logs, and test results are mandatory.
6. **Fail loud.** If a gate fails, report it clearly. Do not hide failures.
7. **No contradictions without user decision.** If code contradicts vision, stop and ask.
8. **The user is the final arbiter.** Present findings; let the user decide.

---

## 7. Starting a Phase

To start a phase, the user says:

> "Start Phase {N}"

The agent then:
1. Reads this Master Prompt.
2. Reads the Phase Document for Phase {N}.
3. Reads the mapped Vision Documents.
4. Reads the Project AGENTS.md (compliance rules).
5. Runs the compliance check.
6. If compliant, invokes the Implementer sub-agent.
7. The loop begins.
