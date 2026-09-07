# Protocol: Attribution

**Version:** 1.0.0

This protocol defines how a contribution is attributed to a contributor, at what confidence, and on what evidence. Related schema: [`schemas/attribution-record.md`](../schemas/attribution-record.md).

## Purpose

Produce defensible statements about who contributed what, kept separate from the contribution itself so multiple attributions can coexist and be revised as evidence changes.

## Procedure

1. **Select the contribution** to attribute (`contribution_id`).
2. **Name the contributor.** Assign `contributor_type` and, if known, `contributor_id` / `agent_ref`.
3. **Name the dimension.** Set `contribution_kind` (CODE_GENERATION, CODE_REVIEW, PROMPT_DIRECTION, APPROVAL, ...). Contribution is not reducible to lines of code.
4. **Establish the basis.** Set `basis` to the strongest supporting evidence class: `OBSERVED`, `REPORTED`, `INFERRED`, or `UNKNOWN`.
5. **Assign confidence from evidence:**
   - `HIGH` — multiple corroborating OBSERVED items.
   - `MEDIUM` — one OBSERVED item, or several agreeing REPORTED items.
   - `LOW` — a single REPORTED item, or weak/indirect signals.
   - `UNKNOWN` — no evidence, or unresolved contradiction.
6. **Link evidence.** Populate `evidence`; it may be empty only when `confidence = UNKNOWN`.
7. **Record verification status** per the verification protocol.

## The Cardinal Rules

- Do not infer an AI agent from code style.
- Do not infer a human merely because no AI record exists.
- An attribution based solely on `INFERRED` signals cannot exceed `LOW` and must never be `VERIFIED`.
- `contributor_id = UNKNOWN` is a valid, correct answer.

## Multiple Attributions

A single contribution commonly carries several attributions — for example AI CODE_GENERATION, human CODE_MODIFICATION, and human APPROVAL. Record each separately; never merge them into one blended claim.

## Rules

1. `confidence` must be justified by the listed `evidence`.
2. Never fabricate contributor identity.
3. Preserve the honest evidence basis; do not upgrade REPORTED to OBSERVED.
