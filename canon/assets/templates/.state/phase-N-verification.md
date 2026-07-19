# Phase {{N}} ({{PHASE_NAME}}) — Independent Verification Report

**Verifier:** Verifier sub-agent (cold check; no knowledge of Implementer rationale)
**Date:** {{DATE}}
**Spec:** `Implementation Plan/PHASE_{{N}}_*.md` (gates §3, formats §2, integration §6)
**Pre-check:** `.state/compliance-log.md` [{{TIMESTAMP}}] ({{RESULT}})

## Verdict: {{APPROVED | REJECTED}}

{{One paragraph: overall result.}}

## Commands run (independent)

| Command | Result |
|---|---|
| `{{BUILD_COMMAND}}` | {{result}} |
| `{{TEST_COMMAND}}` | {{result}} |

## Per-gate results (cold)

| Gate | Test | Result | Evidence |
|---|---|---|---|
| {{N}}.1 {{name}} | `{{test name}}` | {{PASS/FAIL}} | {{what was observed — output, files, behavior; not the Implementer's claim}} |

## Design-level requirements

{{Numbered list verifying the phase's cross-cutting requirements (the things the phase doc
demands beyond individual gates), each VERIFIED/FAILED with evidence.}}

## Compliance check ({{vision docs + sections}})

{{Comparison against the mapped vision documents; COMPLIANT / EXTENSION / CONTRADICTION per
requirement.}}

## Findings

* **Blocking:** {{must fix before phase acceptance, or "none"}}
* **Non-blocking:** {{observations for later phases, or "none"}}

## LLM cost

{{Total spend attributable to this phase's implementation and verification.}}
