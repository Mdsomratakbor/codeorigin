# Adapter: Cursor

**Status:** Optional. Does not modify the canonical model.

Maps signals from the Cursor AI-assisted IDE onto CodeOrigin schemas.

## Agent Identity

```text
agent_id:     agent/cursor
agent_name:   Cursor
provider:     configurable/UNKNOWN
model:        model id if surfaced, else UNKNOWN
session:      chat/composer session id if available
environment:  IDE
```

## Signal Mapping

| Cursor signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| Chat/Composer session | Agent Event | `SESSION_STARTED` | REPORTED |
| Human prompt | Agent Event | `PROMPT_SUBMITTED` | REPORTED |
| AI edit / apply | Agent Event / Evidence | `PATCH_GENERATED` / `FILE_MODIFIED` | REPORTED |
| Inline completion accepted by human | Agent Event | `HUMAN_EDIT` (with AI assistance noted) | REPORTED / INFERRED |
| Commit created | Evidence | `GIT_COMMIT` | OBSERVED |

## Gaps

- Inline completions blur AI vs human authorship. Attribute such changes as **AI-assisted** collaboration (AI suggestion + human acceptance), not pure AI or pure human.
- Model/provider may be `UNKNOWN`.

## Corroboration

- Corroborate applied edits with `GIT_COMMIT` / `GIT_DIFF`.

## Notes

For accepted inline suggestions, prefer a collaborative representation: an AI `CODE_GENERATION` attribution (basis often INFERRED/REPORTED, LOW/MEDIUM confidence) plus a HUMAN `CODE_MODIFICATION`/acceptance attribution. Do not overstate AI authorship of small completions.
