# Schema: Agent Event

**Version:** 1.0.0
**Status:** Canonical (stability-sensitive)

An **Agent Event** is a single, timestamped occurrence in a development session involving an AI agent, a human, or an automated tool. Agent events are the raw stream from which contributions and provenance are derived. A stream may be partial — not every agent can emit every event type. Missing events are represented by their absence, never by fabricated placeholders.

Agent events are typically the raw material that becomes `AGENT_EVENT` or `AGENT_SESSION` evidence records.

---

## Fields

| Field | Required | Type | Description |
|---|---|---|---|
| `event_id` | yes | string | Unique identifier within the project. Example: `EVENT-0007`. |
| `event_type` | yes | enum | See **Event Types** below. |
| `actor_type` | yes | enum | `HUMAN` \| `AI_AGENT` \| `AUTOMATED_TOOL` \| `UNKNOWN`. |
| `actor_id` | no | string | Identifier of the actor. Agent ID or repository-local human ID. `UNKNOWN` if not known. |
| `agent_ref` | no | string | If `actor_type = AI_AGENT`, reference to an agent identity (see provenance-record `agent`). |
| `timestamp` | no | ISO-8601 | When the event occurred. `UNKNOWN` if not reliably known. |
| `session_id` | no | string | Session or conversation the event belongs to. |
| `artifact` | no | string | Artifact affected, if any. Example: file path, PR, commit. |
| `payload_ref` | no | string | Pointer to the raw event data (log offset, transcript ID) — not the full content. |
| `provenance_class` | yes | enum | `OBSERVED` \| `REPORTED` \| `INFERRED` \| `UNKNOWN`. |
| `parent_event` | no | string | The event this one follows from, enabling ordering within a session. |
| `description` | no | string | Factual description of the event. |

---

## Event Types

```text
SESSION_STARTED
PROMPT_SUBMITTED
RESPONSE_GENERATED
PATCH_GENERATED
FILE_CREATED
FILE_MODIFIED
FILE_DELETED
REVIEW_STARTED
REVIEW_COMPLETED
HUMAN_EDIT
APPROVAL
REJECTION
COMMIT_CREATED
MERGE_COMPLETED
VERIFICATION_COMPLETED
```

The list is extensible. An adapter may map an ecosystem-specific signal onto one of these types, or propose a new type. Adapters must not redefine existing types.

---

## Partial Streams

Support incomplete event streams. Examples of legitimate gaps:

- An agent reports `RESPONSE_GENERATED` and `PATCH_GENERATED` but no `SESSION_STARTED`.
- A human edit is observed via Git but no `HUMAN_EDIT` IDE event exists.
- A commit exists (`COMMIT_CREATED`) but the generating session was never captured.

In every case: record what exists, mark the rest absent, and let downstream confidence reflect the gap. Never synthesize the missing events.

---

## Rules

1. Every event must correspond to something that actually occurred or was actually reported.
2. `provenance_class` is mandatory: was this event `OBSERVED` (e.g., captured by the IDE), `REPORTED` (asserted by an agent), or `INFERRED`?
3. If ordering is uncertain, do not fabricate `timestamp` or `parent_event`; leave them `UNKNOWN`.
4. `actor_type = UNKNOWN` is valid and preferable to a guess.

---

## Example

```text
EVENT-0001
  event_type:        SESSION_STARTED
  actor_type:        AI_AGENT
  agent_ref:         agent/claude-code
  session_id:        S-2026-09-05-01
  timestamp:         2026-09-05T14:00:03Z
  provenance_class:  REPORTED

EVENT-0002
  event_type:        PROMPT_SUBMITTED
  actor_type:        HUMAN
  actor_id:          contrib/a.dev
  session_id:        S-2026-09-05-01
  timestamp:         2026-09-05T14:01:40Z
  parent_event:      EVENT-0001
  provenance_class:  REPORTED
  description:       Human asked the agent to implement token refresh.

EVENT-0003
  event_type:        PATCH_GENERATED
  actor_type:        AI_AGENT
  agent_ref:         agent/claude-code
  session_id:        S-2026-09-05-01
  artifact:          src/auth/token.ts
  timestamp:         2026-09-05T14:02:11Z
  parent_event:      EVENT-0002
  provenance_class:  REPORTED

EVENT-0004
  event_type:        COMMIT_CREATED
  actor_type:        HUMAN
  actor_id:          contrib/a.dev
  artifact:          3f9a1c2
  timestamp:         2026-09-05T14:38:20Z
  provenance_class:  OBSERVED
  description:       Observed in git history; links session output to the repository.
```
