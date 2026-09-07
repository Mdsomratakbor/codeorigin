# Protocol: Agent Events

**Version:** 1.0.0

This protocol defines how agent, human, and tool events are captured into an ordered stream, and how partial streams are handled. Related schema: [`schemas/agent-event.md`](../schemas/agent-event.md).

## Purpose

Capture the raw sequence of occurrences in a development session so that contributions and provenance can be derived from real events rather than assumptions.

## Procedure

1. **Capture events as they occur or as they are reported.** Each event becomes an Agent Event record.
2. **Assign `actor_type`.** `HUMAN`, `AI_AGENT`, `AUTOMATED_TOOL`, or `UNKNOWN`.
3. **Classify provenance.** `OBSERVED` (captured by a trusted system), `REPORTED` (asserted by an agent/human), or `INFERRED`.
4. **Order within a session.** Use `session_id` and `parent_event` to express sequence. If ordering is uncertain, leave it `UNKNOWN` — do not guess.
5. **Reference, do not copy.** Store a `payload_ref` pointer to raw data rather than embedding full transcripts.
6. **Promote to evidence.** Events that support a contribution become `AGENT_EVENT` or `AGENT_SESSION` evidence via the evidence protocol.

## Partial Streams

Not every agent emits every event type. Legitimate gaps include a missing `SESSION_STARTED`, an observed commit with no captured session, or a human edit visible only through Git. Record what exists; mark the rest absent. Never synthesize missing events to complete a chain.

## Multi-Agent Streams

When multiple agents participate, each event carries its own `agent_ref`. A single `session_id` may span a handoff, or each agent may have its own session. Preserve enough linkage (`parent_event`, `session_id`) to reconstruct the handoff order.

## Rules

1. Every event corresponds to a real or reported occurrence.
2. `provenance_class` is mandatory.
3. Do not fabricate `timestamp` or `parent_event`.
4. `actor_type = UNKNOWN` is preferable to a guess.
