# Adapter: Spec Kit

**Status:** Optional. Does not modify the canonical model.

Spec Kit is an SDD framework, not a coding agent. This adapter explains how Spec Kit **metadata** can be consumed by CodeOrigin as evidence. CodeOrigin does not implement or require Spec Kit.

## Role

```text
Spec Kit -> Specification metadata -> CodeOrigin provenance (as evidence)
```

Spec Kit artifacts describe *what should be built*. When a contribution is linked to a Spec Kit artifact, that linkage is recorded as evidence and as a `SPECIFICATION_INPUT` contribution kind — it does not, by itself, establish who wrote the code.

## Signal Mapping

| Spec Kit signal | Maps to | Event/Evidence type | Provenance class |
|---|---|---|---|
| Specification document | Evidence | `MANUAL_RECORD` / `IDE_EVENT` | OBSERVED (the doc exists) / REPORTED (its authorship) |
| Spec authored/edited by a human | Contribution | action `DIRECTED` / kind `SPECIFICATION_INPUT` | REPORTED unless VCS-observed |
| Spec-to-change linkage | Evidence | `MANUAL_RECORD` | REPORTED |

## Gaps

- A specification's existence does not prove who implemented the change or how.
- Authorship of the spec is separate from authorship of the code.

## Corroboration

- Link the spec artifact to the implementing commit(s) via evidence; keep the code attribution grounded in agent/Git signals, not the spec.

## Notes

CodeOrigin must remain usable without Spec Kit. This adapter only shows how Spec Kit output can enrich provenance as `SPECIFICATION_INPUT` evidence.
