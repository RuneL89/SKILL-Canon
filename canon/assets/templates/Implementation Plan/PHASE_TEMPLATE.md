# Phase {{N}}: {{PHASE_NAME}}

**Document ID:** `{{PROJECT_SLUG}}-IMPL-PHASE-{{N}}`
**Version:** 1.0.0
**Status:** Draft
**Date:** {{DATE}}
**Dependencies:** Phase {{N-1}} ({{PREV_PHASE_NAME}})
**Estimated Time:** {{ESTIMATE}}
**LLM Token Budget:** {{BUDGET}} (hard cap) — or `$0` for deterministic phases

---

<!-- PHASE DOC ANATOMY — read this before writing a phase:
     1. Objective          — one component, clearly bounded
     2. What to Build      — exact files, interfaces, prompts; concrete enough that the
                             Implementer never has to invent structure
     3. Technical Gates    — automated tests with explicit pass criteria; EVERY gate has an
                             isolation test (component alone, no pipeline)
     4. UAT                — manual steps with exact commands + expected output
     5. Approval Checklist — hard sign-off list
     6. Integration Notes  — contract with the previous and next phase
     Delete these HTML comments from the generated phase docs. -->

## 1. Objective

{{One paragraph: what this phase builds and why it is a single deliverable. State what is
explicitly NOT in scope (features of later phases).}}

---

## 2. What to Build

### 2.1 {{COMPONENT}}

**File:** `{{path/to/file}}`

{{Exact interface, prompt text, schema, or behavior. Include code blocks for interfaces and
data shapes. Specify error handling.}}

---

## 3. Technical Approval Gates

<!-- Gate format: numbered Gate {N}.{k}, a concrete test (code or precise procedure),
     and an explicit Pass Criteria line. LLM-dependent gates should be verifiable with
     injected stubs so the suite runs without API keys by default. -->

### Gate {{N}}.1: {{GATE_NAME}}

```
{{test code or precise test procedure}}
```

**Pass Criteria:** {{observable, binary condition}}

---

## 4. User Acceptance Tests (UAT)

<!-- UAT format: something the USER does — a command to run or a screen to open —
     plus exactly what they should see and how to confirm it worked. -->

### UAT {{N}}.1: {{USER_FACING_CAPABILITY}}

```bash
{{exact command}}
```

**Expected:** {{what the user should see, precisely enough to judge pass/fail}}

---

## 5. Approval Checklist

Before moving to Phase {{N+1}}, verify:

- [ ] All {{X}} technical gates pass (`{{TEST_COMMAND}}` is green).
- [ ] All {{Y}} UAT steps pass (manual verification).
- [ ] {{phase-specific invariant}}
- [ ] Total LLM cost for this phase is under {{BUDGET}}.
- [ ] No code exists for {{NEXT_PHASE_COMPONENT}} (next phase's scope).
- [ ] Compliance pre-check and post-check logged; no unresolved contradictions.
- [ ] `.state/phase-{{N}}-status.json` shows all gates passed, deviations recorded, no blockers.

---

## 6. Integration Notes

### What Phase {{N}} Depends On (from Phase {{N-1}})
- {{artifacts, contracts, files the previous phase must have produced}}

### What Phase {{N}} Produces (for Phase {{N+1}})
- {{artifacts this phase must leave behind}}

### Contract with Phase {{N+1}}
- {{exact shapes/paths/semantics the next phase will rely on — if this changes, both phase
   docs change}}
