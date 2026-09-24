# Agent Instructions

These skills are designed to be small, adaptable, and composable. They work with any model.

## Before using a skill

- Read this repo's `CONTEXT.md` first — it defines the shared language used across all skills.
- Skills are loaded individually. Load only the skill you need for the current task.

## Skill invocation convention

Skills in this repo are designed to be invoked by name as slash commands:

- `/ai-implementation-review` — audit, diagnose, or evaluate AI implementation quality in a codebase
- `/ai-eval-design` — convert product requirements into an evaluation plan for AI behavior
- `/loop` — check a durable job or external state on a schedule in the current Codex task

When a user invokes a skill, load the corresponding `SKILL.md` from `skills/<category>/<skill-name>/` and follow its workflow.

## Writing skills

When creating new skills, follow the pattern established by the existing skills in this repo:

1. One skill per directory under `skills/<category>/<skill-name>/`
2. `SKILL.md` is the entry point with frontmatter (`name`, `description`) and the core workflow
3. Supporting reference files live alongside `SKILL.md` (e.g., `RUBRIC-TEMPLATES.md`, `DIAGNOSIS.md`) and are referenced via relative paths
4. Split into separate files when `SKILL.md` exceeds ~100 lines — use progressive disclosure for detailed reference content
5. Each category directory has a `README.md` listing every skill with a one-line description
6. Keep workflows procedural and specific — operating instructions for the agent, not blog posts
