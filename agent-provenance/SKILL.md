# Skill: Agent Provenance

**Version:** 1.0.0

## Purpose

Record which AI agent participated in a contribution, using a generic agent model that captures only known facts and supports multiple and interchangeable agents.

## Scope

In scope: identifying and recording agent identity (id, name, version, provider, model, session, environment) for a contribution.

Out of scope: attributing the contribution's dimension or verifying it (see AI Attribution, Contribution Verification).

## Inputs

- Agent session records, tool metadata, IDE integration data, explicit agent declarations.

## Preconditions

- A contribution or provenance node exists to associate the agent with.

## Workflow

1. Gather agent-identifying evidence.
2. Populate the generic agent sub-model with only known fields:

   ```text
   agent_id  agent_name  agent_version  provider  model  session  environment
   ```

3. Leave every unknown field `UNKNOWN`.
4. Attach the agent to the Provenance Record per [`schemas/provenance-record.md`](../schemas/provenance-record.md).
5. In multi-agent flows, give each node its own agent identity.

## Rules

- Do not hard-code a fixed list of agents; the model is generic.
- Do not infer a specific agent from code style.
- Unknown fields remain `UNKNOWN`.
- Replacing one agent with another must not change the model — only field values.

## Evidence Requirements

- Agent identity should be backed by session/tool evidence. Self-declared identity is `REPORTED`.

## Attribution Rules

- Agent identity feeds AI Attribution but is recorded independently of the confidence assessment.

## Outputs

- A populated agent sub-model within a Provenance Record.

## Verification

- Corroborate agent identity against session metadata or IDE integration evidence where possible.

## Failure Conditions

- Conflicting identity claims → `UNKNOWN` until resolved; record the conflict.
- No identifying evidence → `agent = UNKNOWN` (valid).

## Limitations

- A transcript may name an agent that did not actually produce the artifact; corroboration bounds trust.

## Compatibility

- Kiro, Codex, Claude Code, OpenCode, Cursor, Copilot, custom, and unknown agents. See [`adapters/`](../adapters/).

## Examples

```text
agent:
  agent_id:      agent/claude-code
  agent_name:    Claude Code
  agent_version: UNKNOWN
  provider:      Anthropic
  model:         UNKNOWN
  session:       S-2026-09-05-01
  environment:   CLI

# Unknown agent is valid:
agent:
  agent_id:      UNKNOWN
  agent_name:    UNKNOWN
  provider:      UNKNOWN
```
