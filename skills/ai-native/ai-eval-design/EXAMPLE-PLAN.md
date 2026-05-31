# Example Eval Plan

```markdown
## Eval Plan: [Feature Name]

### Output Type
[classification | generation | extraction | ...]

### Golden Dataset
- [N] total cases
- [N] happy path, [N] edge, [N] adversarial, [N] regression
- Location: [path to dataset]

### Rubric
[Link or embedded rubric — see RUBRIC-TEMPLATES.md]

### Pass/Fail
- Minimum score: [N]/10
- Critical failures: [list]
- Error budget: [N]%

### Human Review
- Sampling rate: [N]%
- Reviewers: [who]
- Escalation: [path]

### Regression Suite
- Pre-commit: [yes/no]
- Pre-release: [yes/no]
- Monitor: [yes/no]

### Budgets
- Latency target: [value]
- Cost target: [value]
- Measurement: [method]
```
