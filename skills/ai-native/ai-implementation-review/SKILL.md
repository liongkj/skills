---
name: ai-implementation-review
description: Audit AI implementation quality in an existing codebase. Use when reviewing AI features, debugging AI behavior, checking prompt quality, or evaluating AI architecture against the domain model.
argument-hint: "Which mode? static-audit, runtime-diagnosis, or architecture-evaluation"
---

# AI Implementation Review

## Purpose

Evaluate how well AI features are implemented in a codebase. Catches common failure modes: missing evals, weak human-in-the-loop, prompt spaghetti, no context management, poor error handling, and architecture drift from the domain model.

## Use When

The user asks to:
- review AI features in an existing codebase
- debug why an AI feature is behaving badly
- evaluate whether the AI architecture matches the product design
- check prompt quality and organization
- assess eval coverage and test quality
- find security or safety issues in AI code

## Workflow

Start by asking the user which mode they need:

> Which kind of review?
> 1. **Static audit** — scan the codebase for AI antipatterns
> 2. **Runtime diagnosis** — diagnose a specific failure (hallucination, latency, cost, brittle output, etc.)
> 3. **Architecture evaluation** — evaluate AI architecture against the domain model

Then follow the corresponding workflow below.

---

### Mode 1: Static Audit

1. Read the project's `CONTEXT.md` and relevant ADRs to understand the domain model.
2. Scan the codebase for antipatterns listed in [REVIEW-CRITERIA.md](./REVIEW-CRITERIA.md#ai-antipatterns).
3. For each finding, document:
   - **Location** — file, line number
   - **Antipattern** — which pattern is violated
   - **Impact** — what could go wrong (hallucination, cost, brittleness, safety)
   - **Fix** — concrete recommendation
4. Present findings grouped by severity: `Critical`, `Significant`, `Informational`.
5. If the audit reveals missing evals, poor context architecture, or missing human-in-the-loop, recommend the relevant skill:
   - Missing evals → `/ai-eval-design`
   - Poor context architecture → deeper architecture review

---

### Mode 2: Runtime Diagnosis

1. Ask the user for:
   - **Symptom** — what is happening that should not be happening
   - **Workflow** — what the user did to trigger it
   - **Frequency** — always, intermittent, edge-case-only
   - **Environment** — production, staging, local
2. Identify the failure category using the symptom:
   - Hallucination / incorrect output → check context quality, prompt specificity, temperature, RAG grounding, source attribution
   - Latency → check model size, prompt length, tool call depth, retry storms, streaming
   - Cost blowup → check prompt size per call, redundant retries, model too large for task, no caching
   - Brittle output → check output format mismatch, schema parsing, tool call arg errors
   - Context overflow → check token counts, truncation strategy, window management
   - Quality regression → check eval scores, rubric drift, user feedback signals
3. Follow the protocol for that category in [DIAGNOSIS.md](./DIAGNOSIS.md).
4. Document root cause and recommended fix.

---

### Mode 3: Architecture Evaluation

1. Read the project's `CONTEXT.md` and ADRs to reconstruct the intended AI architecture.
2. Evaluate the codebase against the dimensions in [REVIEW-CRITERIA.md](./REVIEW-CRITERIA.md#architecture-dimensions).
3. For each dimension, document:
   - **Intended design** — what the domain model says
   - **Actual implementation** — what the code does
   - **Gap** — where they diverge
   - **Impact** — what problems the gap causes
   - **Fix** — how to close the gap
4. Surface contradictions between the domain model and implementation. If a contradiction reveals a flaw in the domain model (not the code), flag it for a `/grill-with-docs` session rather than proposing a code fix.

---

## Cross-References

- [REVIEW-CRITERIA.md](./REVIEW-CRITERIA.md) — antipattern checklist and architecture dimensions
- [DIAGNOSIS.md](./DIAGNOSIS.md) — runtime failure diagnosis protocols
- [CONTEXT.md](../../../CONTEXT.md) — shared vocabulary
- `improve-codebase-architecture` — for code-structure concerns (module depth, seam quality, testability) outside AI-specific quality
