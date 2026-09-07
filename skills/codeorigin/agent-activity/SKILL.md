# Skill: Agent Activity

**Version:** 1.0.0

## Purpose

Capture the stream of agent, human, and tool events during development into ordered, provenance-classified Agent Event records, supporting partial streams.

## Scope

In scope: recording events (sessions, prompts, generations, edits, commits, approvals, etc.) and their ordering.

Out of scope: interpreting events into attributions or metrics.

## Inputs

- Session logs, IDE events, tool metadata, VCS events, human records.

## Preconditions

- A source for the events (may be partial).

## Workflow

1. Record each occurrence as an Agent Event per [`schemas/agent-event.md`](../schemas/agent-event.md).
2. Assign `actor_type` and `provenance_class`.
3. Order within a session via `session_id` and `parent_event`; leave uncertain ordering `UNKNOWN`.
4. Reference raw data with `payload_ref`; do not embed full transcripts.
5. Promote supporting events to Evidence Records.

## Rules

- Support partial streams; record what exists, mark the rest absent.
- Never synthesize missing events.
- Do not fabricate timestamps or ordering.

## Evidence Requirements

- Events used to support contributions are promoted to `AGENT_EVENT` / `AGENT_SESSION` evidence with locators.

## Attribution Rules

- Events are raw material for attribution; this skill does not attribute.

## Outputs

- An ordered (where known) stream of Agent Event records.

## Verification

- Confirm event ordering links resolve and do not assert unknown order as fact.

## Failure Conditions

- Ordering unknown → `parent_event = UNKNOWN`.
- Actor unknown → `actor_type = UNKNOWN`.

## Limitations

- Not every agent emits every event type; gaps are expected and must not be filled by inference.

## Compatibility

- See [`protocols/agent-events.md`](../protocols/agent-events.md) and [`adapters/`](../adapters/) for per-ecosystem mapping.

## Examples

```text
EVENT-0001 SESSION_STARTED   AI_AGENT  agent/claude-code  S-2026-09-05-01  REPORTED
EVENT-0002 PROMPT_SUBMITTED  HUMAN     contrib/a.dev      parent=EVENT-0001 REPORTED
EVENT-0003 PATCH_GENERATED   AI_AGENT  agent/claude-code  artifact=src/auth/token.ts REPORTED
EVENT-0004 COMMIT_CREATED    HUMAN     contrib/a.dev      artifact=3f9a1c2 OBSERVED
```
