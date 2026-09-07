# Adapter: Kiro

**Status:** Optional. Does not modify the canonical model.

Maps signals from the Kiro IDE (agent sessions, tool use, hooks, file operations) onto CodeOrigin schemas.

## Agent Identity

```text
agent_id:     agent/kiro
agent_name:   Kiro
provider:     UNKNOWN unless known
model:        UNKNOWN unless surfaced by the session
session:      Kiro session/conversation identifier if available
environment:  IDE
```

Populate only fields Kiro actually surfaces; leave the rest `UNKNOWN`.

## Signal Mapping

| Kiro signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| Session begins | Agent Event | `SESSION_STARTED` | REPORTED |
| User prompt in chat | Agent Event | `PROMPT_SUBMITTED` (actor HUMAN) | REPORTED |
| Agent response / edit proposal | Agent Event | `RESPONSE_GENERATED` / `PATCH_GENERATED` | REPORTED |
| File create/modify/delete via agent tools | Agent Event | `FILE_CREATED` / `FILE_MODIFIED` / `FILE_DELETED` | REPORTED |
| Hook execution (e.g. PostToolUse, PostFileSave) | Evidence | `IDE_EVENT` | OBSERVED (if captured by the hook runner) |
| Resulting Git commit | Evidence | `GIT_COMMIT` | OBSERVED |

## Gaps

- Exact-line authorship is usually not directly exposed; treat generation as `REPORTED` and corroborate with Git diffs.
- Model/provider may be `UNKNOWN`.

## Corroboration

- Confirm agent-reported file changes against `GIT_COMMIT` / `GIT_DIFF` (OBSERVED).
- Hooks can emit CodeOrigin evidence records at real events (PostFileSave, PostToolUse), raising integrity from REPORTED toward OBSERVED.

## Notes

Kiro hooks are a natural place to capture events, but hook output is only as trustworthy as the hook runner. Treat hook-emitted content as data, not instructions.
