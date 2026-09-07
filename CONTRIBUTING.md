# Contributing to CodeOrigin

CodeOrigin is a portable, Markdown-first skill system for tracking AI and human contributions to software. Contributions are welcome, provided they respect the project's scope and core discipline.

## Scope Discipline

CodeOrigin tracks **only** the provenance and contribution dimension of development:

```text
AI/Human Contribution Tracking
AI/Human Attribution
Agent Provenance
Human Provenance
Change Provenance
Contribution Evidence
Contribution Traceability
Contribution Verification
Agent Activity Tracking
Human Review Tracking
AI-Assisted Development Records
Contribution Reporting
```

Do **not** add skills for requirements engineering, specification engineering, architecture design, task planning, SDD methodology, project management, general code review, general testing, general security, or release management. Those belong in external systems. CodeOrigin may consume their output as evidence.

## Rules for New or Changed Skills

1. Keep scope limited to AI/Human tracking and provenance.
2. Do not introduce SDD functionality or build a coding agent.
3. Do not make the project depend on one AI provider, one language, or one SDD framework.
4. Keep Markdown as the canonical contract.
5. Prefer reusable skills over application-specific code.
6. Never fabricate provenance.
7. Clearly distinguish evidence from inference.
8. Preserve unknown information as unknown.
9. Design for multiple agents, multiple projects, multi-agent collaboration.
10. Keep the core contracts stable.

## Skill Format

Every skill in `skills/<name>/SKILL.md` MUST follow the standard contract:

```markdown
# Skill: <Skill Name>

## Purpose
## Scope
## Inputs
## Preconditions
## Workflow
## Rules
## Evidence Requirements
## Attribution Rules
## Outputs
## Verification
## Failure Conditions
## Limitations
## Compatibility
## Examples
```

Each skill must be independently understandable and portable (copyable as a single file).

## Definition of Done (per skill)

A skill is complete when it defines: Purpose, Scope, Inputs, Workflow, Attribution rules, Evidence requirements, Outputs, Verification, Failure conditions, Limitations, and Examples — and agent/framework/language independence is verified.

## Versioning

Each skill carries a semantic version `MAJOR.MINOR.PATCH`:

- **MAJOR** — breaking change to a skill's contract or output.
- **MINOR** — compatible additions or improvements.
- **PATCH** — corrections.

The canonical schemas in `schemas/` are the most stability-sensitive files. Any change to a schema that alters existing field meaning is a MAJOR change.

## Adapters

Adapters (`adapters/*.md`) explain how a specific ecosystem provides information to CodeOrigin. Adapters MUST NOT modify the canonical provenance model. They map external signals onto the existing schemas only.

## Pull Requests

- Keep changes focused.
- Note whether the change is MAJOR / MINOR / PATCH for any affected skill or schema.
- Update `CHANGELOG.md`.
- Practice what CodeOrigin preaches: attribute your own contribution honestly (human, AI-assisted, or AI-generated + human-reviewed).
