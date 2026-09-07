# Protocol: Contribution

**Version:** 1.0.0

This protocol defines how a contribution is recorded from evidence and events. Related schema: [`schemas/contribution-record.md`](../schemas/contribution-record.md).

## Purpose

Record a single meaningful act of contribution — a generation, modification, review, approval, prompt, and so on — linking actor, action, artifact, and change, backed by evidence.

## Procedure

1. **Identify the act.** Determine the `action` (GENERATED, MODIFIED, REVIEWED, APPROVED, DIRECTED, PROMPTED, etc.) and the `artifact`.
2. **Determine the primary contributor.** Assign `contributor_type` and, if known, `contributor_id` / `agent_ref`. Use `UNKNOWN` where unknown.
3. **Attach the change.** Set `change_ref` (commit, PR, patch) when a change exists.
4. **Gather evidence.** Link Evidence Records via the evidence protocol.
5. **Add attributions.** For collaborative acts, create Attribution Records (attribution protocol) and reference them in `attributions`.
6. **Link lineage.** Set `parent_contribution` when this act derives from an earlier one; set `related_contributions` for siblings in the same change.
7. **Set confidence and verification.** Derive both from the evidence, not from assertion.

## Collaboration

Do not collapse a collaborative change into a single actor. Represent each act as its own contribution and connect them:

```text
Human PROMPTED -> AI_AGENT GENERATED -> Human MODIFIED -> Human REVIEWED -> Human APPROVED -> MERGED
```

Each arrow is a `parent_contribution` link.

## Rules

1. `contribution_id` is stable and unique; the scheme is configurable.
2. `confidence` and `verification_status` are mandatory and evidence-justified.
3. Never fabricate `contributor_id`, `timestamp`, or `change_ref`.
4. A human contribution need not involve writing code (DIRECTED, PROMPTED, REVIEWED, APPROVED, VALIDATED are first-class).
