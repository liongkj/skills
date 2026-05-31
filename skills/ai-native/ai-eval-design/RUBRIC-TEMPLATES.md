# Rubric Templates

## Generation Tasks (summary, draft, reply)

| Criteria | Weight | 1 (Poor) | 2 (Acceptable) | 3 (Good) |
|---|---|---|---|---|
| Accuracy | 40% | Key facts wrong | Minor errors | All facts correct |
| Completeness | 25% | Missing critical info | Covers core, misses nuance | Comprehensive |
| Conciseness | 15% | Verbose/wasteful | Slightly long | Appropriate length |
| Tone | 10% | Wrong register | Mostly appropriate | Perfect tone |
| Structure | 10% | Hard to follow | Reasonable flow | Well-organized |

## Classification Tasks

| Criteria | Weight | 1 (Poor) | 2 (Acceptable) | 3 (Good) |
|---|---|---|---|---|
| Precision | 40% | Frequent false positives | Occasional FP | Correct when it labels |
| Recall | 40% | Misses obvious cases | Catches most | Catches all expected |
| Confidence calibration | 20% | Confident when wrong | Reasonable | Accurate confidence |

## Extraction Tasks

- **Precision** — extracted entities are correct
- **Recall** — all expected entities are extracted
- **Boundary accuracy** — spans match exactly (no extra/missing characters)

## Custom Rubrics

When building a custom rubric, always define:

1. **Criteria** — what you are measuring
2. **Weight** — relative importance (percentages summing to 100)
3. **Scale** — 3 or 5 levels with concrete descriptors per level
4. **Auto-fail conditions** — any single criterion that invalidates the whole output

See the full workflow in [SKILL.md](./SKILL.md#3-build-the-rubric).
