# Adapter: GitHub Copilot

**Status:** Optional. Does not modify the canonical model.

Maps signals from GitHub Copilot (completions and chat) onto CodeOrigin schemas.

## Agent Identity

```text
agent_id:     agent/copilot
agent_name:   GitHub Copilot
provider:     GitHub/OpenAI (if known)
model:        UNKNOWN unless surfaced
session:      chat session id if available
environment:  IDE
```

## Signal Mapping

| Copilot signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| Chat session | Agent Event | `SESSION_STARTED` | REPORTED |
| Human prompt (chat) | Agent Event | `PROMPT_SUBMITTED` | REPORTED |
| Chat-generated code applied | Agent Event / Evidence | `PATCH_GENERATED` | REPORTED |
| Inline completion accepted | Agent Event | `HUMAN_EDIT` (AI-assisted) | INFERRED / REPORTED |
| Commit created | Evidence | `GIT_COMMIT` | OBSERVED |

## Gaps

- Inline completions rarely carry a durable record of what was AI-suggested vs human-typed. Treat as **AI-assisted** collaboration; do not claim precise AI line percentages without evidence.
- Model is typically `UNKNOWN`.

## Corroboration

- Corroborate with `GIT_COMMIT` / `GIT_DIFF`.

## Notes

Copilot completions are the clearest case where line-based AI attribution is unreliable. Record AI assistance honestly at LOW/MEDIUM confidence and rely on the AI/Human Accounting skill's method/limitations documentation.
