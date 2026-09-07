# Adapters

Adapters are **optional**. Each adapter explains how a specific ecosystem (a coding agent or an SDD framework) can supply information to CodeOrigin. Adapters map that ecosystem's signals onto the canonical schemas and event model.

## Hard Rule

> Adapters **must never modify** the canonical provenance model. They only describe how external signals map onto the existing [`schemas/`](../schemas/) and [`protocols/`](../protocols/).

If an ecosystem provides no signal for a field, that field is `UNKNOWN`. Adapters never invent evidence, agent identity, or events to fill gaps.

## Provenance of Adapter-Sourced Signals

Most signals a coding agent emits about its own behavior are **REPORTED** (the agent asserting what it did), not **OBSERVED**. Signals captured by an independent, trusted system (Git history, a CI provider, a PR platform) are **OBSERVED**. Adapters state the honest class for each mapped signal so downstream confidence and verification stay correct.

## Available Adapters

| Adapter | Ecosystem | Typical role |
|---|---|---|
| [`kiro.md`](kiro.md) | Kiro IDE | Coding agent, session + hook events |
| [`codex.md`](codex.md) | Codex | Coding agent |
| [`claude-code.md`](claude-code.md) | Claude Code | Coding agent (CLI) |
| [`opencode.md`](opencode.md) | OpenCode | Coding agent |
| [`cursor.md`](cursor.md) | Cursor | AI-assisted IDE |
| [`copilot.md`](copilot.md) | GitHub Copilot | AI code assistant |
| [`spec-kit.md`](spec-kit.md) | Spec Kit | SDD framework (metadata as evidence) |
| [`openspec.md`](openspec.md) | OpenSpec | SDD framework (metadata as evidence) |

## Adding an Adapter

An adapter should document:

1. What signals the ecosystem exposes.
2. How each signal maps to an Evidence Type / Event Type / schema field.
3. The honest provenance class (OBSERVED / REPORTED / INFERRED) of each signal.
4. Known gaps (fields that will be `UNKNOWN`).
5. Corroboration hints (which independent signal, e.g. Git, can confirm a REPORTED claim).
