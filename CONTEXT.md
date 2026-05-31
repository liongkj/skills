# Context Glossary

This glossary defines the shared language used across all skills in this repo.

## AI-Native Product

A product whose core value proposition depends on probabilistic AI (LLMs, vision, speech, etc.) — not an ordinary app with a chatbot bolted on. AI-native products reshape the workflow around uncertainty, user correction, memory, feedback loops, and continuous improvement.

## Interaction Model

The primary relationship between the user and the AI system. One of: copilot, autopilot, agent, reviewer, analyst, tutor, simulator.

## Human Control Point

A place in the workflow where the user approves, edits, rejects, or overrides an AI action. Each control point specifies what the AI decides alone and what requires confirmation.

## Context Architecture

The system design for what information the AI has access to. Separates:
- short-term context (conversation history)
- long-term memory (user preferences, past facts)
- external knowledge (retrieval sources)
- private user data
- system instructions (prompts)

## Evals

Tests and rubrics that measure AI output quality. Includes golden datasets, adversarial cases, domain-specific rubrics, and regression tests.

## AI Implementation Quality

How well an AI feature is built relative to software engineering best practices. Dimensions include: prompt architecture, eval coverage, human-in-the-loop design, context management, error handling, observability, and safety boundaries.
