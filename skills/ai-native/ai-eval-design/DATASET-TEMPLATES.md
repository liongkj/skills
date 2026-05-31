# Dataset Templates

## Golden Dataset Record

```yaml
input: ""
expected_output: ""
rationale: ""
source: ""
```

## Coverage Categories

| Category | Description | Minimum Count |
|---|---|---|
| Happy path | Typical inputs with expected outputs | 5 |
| Edge cases | Min, max, empty, very long, mixed language | 5 |
| Ambiguous cases | Inputs where the right answer is arguable | 4 |
| Adversarial cases | Injection attempts, contradictory instructions, out-of-scope requests | 3 |
| Regression cases | Inputs that previously produced wrong outputs | 3 |

Target: at least 20 cases per core workflow. 50+ for high-risk workflows.

## Per Output Type Guidance

| Output Type | Key Boundary Conditions |
|---|---|
| Classification | Near-decision-boundary inputs, unseen labels, null input |
| Extraction | Empty text, text with no entities, text with all entities, duplicate entities |
| Generation | Empty prompt, single-word prompt, contradictory instructions, very long context |
| Scoring | Out-of-range values, tied scores, extreme inputs |
| Decision | Missing data, conflicting signals, ambiguous edge cases |
| Structured data | Missing optional fields, extra fields, type mismatches, null values |

See the full workflow in [SKILL.md](./SKILL.md#2-design-the-golden-dataset).
