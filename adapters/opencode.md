# Adapter: OpenCode

**Status:** Optional. Does not modify the canonical model.

Maps signals from OpenCode onto CodeOrigin schemas.

## Agent Identity

```text
agent_id:     agent/opencode
agent_name:   OpenCode
provider:     configurable/UNKNOWN (OpenCode can front multiple providers)
model:        model id if surfaced, else UNKNOWN
session:      session id if available
environment:  CLI / IDE
```

Because OpenCode can be configured against different providers/models, `provider` and `model` are frequently variable — record the actual configured values when surfaced, otherwise `UNKNOWN`.

## Signal Mapping

| OpenCode signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| Session start | Agent Event | `SESSION_STARTED` | REPORTED |
| Human prompt | Agent Event | `PROMPT_SUBMITTED` | REPORTED |
| Generated patch | Agent Event / Evidence | `PATCH_GENERATED` / `GENERATED_PATCH` | REPORTED |
| Applied changes | Agent Event | `FILE_MODIFIED` etc. | REPORTED |
| Commit created | Evidence | `GIT_COMMIT` | OBSERVED |

## Gaps

- Underlying model/provider varies by configuration; do not assume a default.

## Corroboration

- Corroborate reported generation with `GIT_COMMIT` / `GIT_DIFF`.

## Notes

When OpenCode is configured to use a third-party model, the CodeOrigin `agent` still records `agent/opencode` as the acting agent, with `provider`/`model` reflecting the backing model when known.
