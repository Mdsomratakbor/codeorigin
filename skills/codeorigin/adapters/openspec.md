# Adapter: OpenSpec

**Status:** Optional. Does not modify the canonical model.

OpenSpec is an SDD framework, not a coding agent. This adapter explains how OpenSpec **metadata** can be consumed by CodeOrigin as evidence. CodeOrigin does not implement or require OpenSpec.

## Role

```text
OpenSpec -> Change/spec metadata -> CodeOrigin provenance (as evidence)
```

An OpenSpec change proposal describes an intended change. Linking a contribution to an OpenSpec proposal is recorded as `SPECIFICATION_INPUT` evidence; it does not establish code authorship.

## Signal Mapping

| OpenSpec signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| Change proposal document | Evidence | `MANUAL_RECORD` | OBSERVED (exists) / REPORTED (authorship) |
| Proposal authored/edited by a human | Contribution | action `DIRECTED` / kind `SPECIFICATION_INPUT` | REPORTED unless VCS-observed |
| Proposal-to-implementation linkage | Evidence | `MANUAL_RECORD` | REPORTED |

## Gaps

- The proposal describes intent, not implementation authorship.

## Corroboration

- Tie the proposal to implementing commits/sessions; keep code attribution grounded in agent/Git signals.

## Notes

CodeOrigin must remain usable without OpenSpec. This adapter only shows how OpenSpec output can enrich provenance as `SPECIFICATION_INPUT` evidence.
