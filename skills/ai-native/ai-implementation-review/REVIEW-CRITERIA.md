# Review Criteria

Two sections. Section 1 is used by the **Static Audit** mode. Section 2 is used by the **Architecture Evaluation** mode.

---

## 1. AI Antipatterns

Check each. Flag file + line number + severity.

### Prompt Management

| # | Antipattern | Why It Matters |
|---|---|---|
| 1.1 | Prompts hardcoded in UI components | Cannot version, review, or test prompts independently |
| 1.2 | Same prompt duplicated across files | Drift guaranteed; fixing one miss leaves others broken |
| 1.3 | No prompt versioning or diff tracking | Regression impossible to diagnose |
| 1.4 | System prompt mixed with user message inline | No separation of concerns; context window wasted |
| 1.5 | Prompt templates without parameterization | Every variation requires a copy-paste |

### Context Management

| # | Antipattern | Why It Matters |
|---|---|---|
| 2.1 | No context window budgeting | Token overflow silently truncates critical context |
| 2.2 | Entire conversation dumped into every call | Cost blowup + irrelevant context degrades quality |
| 2.3 | No separation of short-term vs long-term memory | Model confuses stale facts with current state |
| 2.4 | Raw model input/output not logged | Impossible to debug what the model actually saw |

### Error Handling & Reliability

| # | Antipattern | Why It Matters |
|---|---|---|
| 3.1 | Empty catch blocks around LLM calls | Silent failures leave users with broken state |
| 3.2 | No retry with backoff for transient failures | Network blips become user-facing errors |
| 3.3 | Hardcoded model name in application code | Cannot swap models without code change; vendor lock-in |
| 3.4 | No fallback model on API failure | Single point of failure |
| 3.5 | Streaming without error recovery | Partial output leaves UI in inconsistent state |

### Human-in-the-Loop

| # | Antipattern | Why It Matters |
|---|---|---|
| 4.1 | AI action executed without user confirmation for irreversible operations | Trust erosion; user feels out of control |
| 4.2 | No undo or revision path for AI-generated content | User stuck with wrong output |
| 4.3 | AI output displayed without source attribution | Hallucinations indistinguishable from facts |
| 4.4 | No confidence indicator on AI suggestions | User cannot calibrate trust per response |

### Observability & Safety

| # | Antipattern | Why It Matters |
|---|---|---|
| 5.1 | No latency or cost tracking per call | Cost surprises in production |
| 5.2 | No eval harness or regression tests | Quality regression invisible until users complain |
| 5.3 | Raw model output rendered directly (no sanitization) | XSS / injection risk |
| 5.4 | No input/output validation for tool call arguments | Unexpected JSON crashes downstream |
| 5.5 | User data sent to model without privacy review | Compliance risk |
| 5.6 | No hallucination detection or guardrails | Confidently wrong output shipped to users |

---

## 2. Architecture Dimensions

Evaluate each dimension against the domain model.

### Capture / Derivation Separation

- Is raw source data stored separately from generated/derived data?
- Can capture run without any LLM involvement?
- Is derivation gated by explicit user action or bounded automatic triggers?
- Can the generation be re-run without losing raw source?

### Context Architecture

- What is the context budget per call?
- How is short-term context (conversation) separated from long-term memory (user preferences, past facts)?
- What external knowledge sources are available?
- How are system instructions versioned and updated?
- Is there a cursor or watermark concept separating committed vs volatile state?

### Entity Identity Model

- How are entities identified and deduplicated across sessions?
- Is identity stable across context windows?
- How does the system handle entity renames or merges?

### Human Control Points

- What does the AI decide alone?
- What requires user approval?
- What can the user edit after AI output?
- What is reversible?
- What is logged for audit?

### Eval Coverage

- Are there golden test cases for core workflows?
- Are there adversarial cases (edge inputs, injection attempts)?
- Is there a rubric defining what "good" means per output type?
- Are evals run automatically on changes?
- Are there cost and latency thresholds?

### Privacy and Data Flow

- Where does user data cross process boundaries (content script ↔ service worker ↔ server)?
- What data is sent to the LLM?
- What data is persisted server-side?
- Is there a consent model for data capture?
- Can users delete their data?

### Tool / Agent Architecture

- What tools does the agent have access to?
- Are tool permissions scoped to minimum necessary?
- Are tool call arguments validated before execution?
- Is tool use logged?
- Are there stop conditions for agent loops?
