---
name: ai-eval-design
description: Convert product requirements into a concrete evaluation plan for AI behavior. Use when building evals, designing rubrics, creating golden datasets, or setting up regression tests for LLM outputs.
---

# AI Evaluation Design

## Purpose

Turn product requirements and AI behavior specs into a concrete, measurable evaluation plan. Prevents shipping AI features that "feel right" but have no quality gate.

## Principles

- Eval first, build second. Define what "good" looks like before writing prompts.
- Golden datasets are not optional. Without labelled examples, you cannot measure regression.
- Rubrics must be specific to the output type. A summary eval is not a classification eval.
- Cost and latency are part of quality. An accurate answer that takes 30s is not a good product.
- Evals are living documents. Add failure cases as you discover them.

## Workflow

### 1. Define the Output Type

Identify what the AI produces:

| Output Type | Examples |
|---|---|
| Classification | Spam/not-spam, sentiment, intent, topic |
| Extraction | Entity names, dates, facts, relationship tuples |
| Generation | Draft reply, summary, email, description |
| Scoring | Relevance score, confidence, priority |
| Decision | Approve/reject, route to human, trigger action |
| Structured data | JSON, SQL, API call arguments |

For each output type, specify:
- **Schema** — the exact shape (types, fields, constraints, enums)
- **Valid range** — what values are allowed, what are not
- **Edge cases** — empty input, ambiguous input, contradictory input

### 2. Design the Golden Dataset

Create test cases covering happy path, edge cases, ambiguous inputs, adversarial examples, and regression cases. Each case includes input, expected output, rationale, and source.

See [DATASET-TEMPLATES.md](./DATASET-TEMPLATES.md) for record formats, coverage targets, and per-output-type boundary guidance.

### 3. Build the Rubric

Define scoring criteria specific to the output type (generation, classification, extraction, etc.). Each rubric needs criteria, weights, a rating scale, and auto-fail conditions.

See [RUBRIC-TEMPLATES.md](./RUBRIC-TEMPLATES.md) for ready-to-use templates and guidance on building custom rubrics.

### 4. Set Pass/Fail Thresholds

Define:
- **Minimum score** — overall rubric score needed to pass
- **Critical failures** — any single criterion that automatically fails (e.g. hallucination = auto-fail)
- **Error budget** — acceptable failure rate in production (e.g. < 5% of outputs below threshold)

### 5. Design the Human Review Loop

For tasks where automated evals are insufficient:
- **Sampling rate** — what % of outputs get human review
- **Review criteria** — what the human checks
- **Escalation path** — what happens when reviewer disagrees with the eval
- **Feedback capture** — how human corrections feed back into the dataset

### 6. Build the Regression Suite

Automate:
- Running golden dataset on every model/prompt change
- Comparing scores against the previous version
- Flagging regressions (score drops beyond threshold)
- Keeping a history of scores across versions

Include:
- **Pre-commit gate** — runs on every prompt change
- **Pre-release gate** — runs before deploying to production
- **Monitor** — runs periodically on production traffic samples

### 7. Set Cost and Latency Budgets

| Metric | Target | Hard Limit | Measurement Method |
|---|---|---|---|
| P50 latency | < 2s | < 5s | End-to-end timer |
| P95 latency | < 4s | < 8s | End-to-end timer |
| Cost per call | < $0.01 | < $0.05 | API cost logs |
| Cost per user session | < $0.10 | < $0.50 | Aggregated session cost |

### 8. Document and Maintain

Use [EXAMPLE-PLAN.md](./EXAMPLE-PLAN.md) as the template for your eval plan. Keep the plan in your repo alongside the feature it evaluates.

## Cross-References

- [DATASET-TEMPLATES.md](./DATASET-TEMPLATES.md) — golden dataset record format and coverage guidance
- [RUBRIC-TEMPLATES.md](./RUBRIC-TEMPLATES.md) — rubric templates for generation, classification, and extraction
- [EXAMPLE-PLAN.md](./EXAMPLE-PLAN.md) — eval plan output template
- [CONTEXT.md](../../../CONTEXT.md) — shared vocabulary
- `/ai-implementation-review` — use when evaluating existing eval coverage in a codebase
