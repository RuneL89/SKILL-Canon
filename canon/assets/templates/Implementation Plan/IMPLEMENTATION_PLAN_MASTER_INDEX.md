# {{PROJECT_NAME}} — Implementation Plan Master Index

**Document ID:** `{{PROJECT_SLUG}}-IMPL-MASTER`
**Version:** 1.0.0
**Status:** Draft
**Date:** {{DATE}}

---

## Overview

This implementation plan breaks {{PROJECT_NAME}} into {{PHASE_COUNT}} phases (0-{{LAST_PHASE}}).
Each phase is a standalone deliverable that can be tested in isolation and integrated with
previously accepted phases. **You do not move to the next phase until every gate in the
current phase passes.**

This structure prevents the compounding-bug failure mode: building everything at once, then
discovering that no part works. Each phase has:

- A clear objective
- Specific files to build
- Technical approval gates (automated tests)
- User acceptance tests (manual verification)
- A hard checklist that must be signed off before proceeding
- Integration notes explaining what this phase produces and what the next phase expects

---

## Phase Directory

<!-- Replace the example rows. Rule of thumb: Phase 0 = infrastructure + golden fixtures;
     order strictly by dependency; LLM-touching phases get a token budget. -->

| Phase | Document | Objective | LLM Cost | Est. Time |
|---|---|---|---|---|
| 0 | [PHASE_00_infrastructure.md](PHASE_00_infrastructure.md) | Repo, build system, test framework, golden fixtures | $0 | 2-4h |
| 1 | [PHASE_01_{{PHASE_SLUG}}.md](PHASE_01_{{PHASE_SLUG}}.md) | {{FIRST_DELIVERABLE}} | $0 | 3-5h |
| 2 | [PHASE_02_{{PHASE_SLUG}}.md](PHASE_02_{{PHASE_SLUG}}.md) | {{SECOND_DELIVERABLE}} | {{BUDGET}} | 4-6h |

**Total Estimated LLM Cost (all phases):** {{TOTAL_BUDGET}}
**Total Estimated Time:** {{TOTAL_TIME}}

---

## The Golden Rule

> **If a phase fails a gate, you do not move to the next phase. You fix the current phase.**

This is the rule that prevents compounding bugs. Each phase is a contract. The next phase
depends on the previous phase's output being correct. If Phase 2's component returns invalid
output, Phase 3 will fail in ways that are hard to debug. Fix Phase 2 first.

---

## Isolation vs. Integration Testing

Every phase has two kinds of tests:

**Isolation Tests:** Test the new component without the rest of the pipeline.
- Call the component's function directly with crafted input. No filesystem, no CLI, no pipeline.

**Integration Tests:** Test the component within the full pipeline.
- Run the real command end-to-end and verify the artifacts it produces.

**Rule:** Every gate must have an isolation test. Integration tests are for UAT and final
verification.

---

## Token Budgets

| Phase | Budget | What Happens If You Hit It |
|---|---|---|
| {{N}} | {{BUDGET}} | {{DIAGNOSIS — e.g. "Your prompt is wrong. Fix the prompt, not the code."}} |

**No retry loops.** If an LLM call fails, fix the prompt and run again. Do not burn tokens on
retries. Pause at 80% of budget and report to the user.

---

## The Test Fixture Strategy

Golden fixtures live in `{{FIXTURE_DIR}}` (created in Phase 0).

These fixtures are sacred. They never change. If a test fails, the code is wrong, not the
fixture.

**Why this matters:** with real-world or mutable test data you cannot tell whether a bug is in
your code or in the data. Golden fixtures remove that ambiguity.

---

## Compliance with Vision Documents

Before starting each phase, read the relevant vision document (all in `Project Vision/`):

| Phase | Vision Document | Why |
|---|---|---|
| 0 | `01_PRODUCT_VISION_AND_ARCHITECTURE.md` | Overall architecture and philosophy |
| {{N}} | `{{VISION_DOC}}` | {{WHY}} |

After completing each phase, update the relevant vision document if the implementation
diverged from the spec **with user approval**. The vision documents are the source of truth.

---

## Approval Workflow

For each phase:

1. **Read the phase document.** Understand what to build.
2. **Run the compliance pre-check.** Log it to `.state/compliance-log.md`.
3. **Build the code.** Write tests first, then implementation.
4. **Run isolation tests.** Verify the component works alone.
5. **Run integration tests.** Verify the component works in the pipeline.
6. **Independent verification.** The Verifier sub-agent cold-checks every gate with evidence.
7. **Run UAT.** The user manually verifies the output.
8. **Check the approval checklist.** Every box must be checked.
9. **Compliance post-check + docs closeout.** Log it; update affected AGENTS.md / vision docs.
10. **Commit.** `git commit -m "Phase N: Description"`
11. **Move to next phase.** Only after all gates pass.

---

## What You Have at Each Phase

<!-- One row per phase: what the user can actually DO after this phase is accepted.
     If a row would be "nothing new", the phase boundary is probably wrong. -->

| Phase | What the User Can Do |
|---|---|
| 0 | Nothing yet. Infrastructure only. |
| {{N}} | {{CAPABILITY}} |

---

## Final Note

Each phase is small, verifiable, and independent. If you get stuck on Phase 2, you still have
a working system from Phase 1. Start with Phase 0. Do not skip ahead. Do not add features from
future phases. Build one gate at a time.
