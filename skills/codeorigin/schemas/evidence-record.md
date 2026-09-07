# Schema: Evidence Record

**Version:** 1.0.0
**Status:** Canonical (stability-sensitive)

An **Evidence Record** captures a single piece of supporting information for a contribution or provenance claim. Evidence is the foundation of the entire model: attribution, provenance, and verification all rest on evidence. Evidence records are never invented — an evidence record must correspond to something that actually exists (a commit, a log line, a file diff, a human statement, etc.).

Everything else in CodeOrigin cites evidence by ID. If a claim has no evidence, its confidence and verification status must reflect that.

---

## Fields

| Field | Required | Type | Description |
|---|---|---|---|
| `evidence_id` | yes | string | Stable, unique identifier within the project. Example: `EVIDENCE-001`. |
| `evidence_type` | yes | enum | See **Evidence Types** below. |
| `provenance_class` | yes | enum | `OBSERVED` \| `REPORTED` \| `INFERRED` \| `UNKNOWN`. How this evidence came to exist. |
| `source` | yes | string | Where the evidence originates. Example: `git`, `kiro-session`, `github-pr`, `human`, `ci`. |
| `source_locator` | no | string | A pointer that lets a reviewer find the raw evidence. Example: commit SHA, PR URL, log path, session ID. |
| `timestamp` | no | ISO-8601 | When the evidenced event occurred. `UNKNOWN` if not reliably known. |
| `captured_at` | no | ISO-8601 | When the evidence was recorded into CodeOrigin. |
| `integrity` | no | enum | `ORIGINAL` \| `DERIVED` \| `REPORTED` \| `INFERRED`. Integrity boundary per SECURITY.md. |
| `related_contributions` | no | string[] | Contribution IDs this evidence supports. |
| `content_hash` | no | string | Optional hash of the raw evidence for tamper detection. |
| `description` | yes | string | Human-readable description of what the evidence shows. Factual, no interpretation beyond what is directly observable. |
| `notes` | no | string | Caveats, limitations, or reviewer notes. |

---

## Evidence Types

```text
GIT_COMMIT
GIT_DIFF
PULL_REQUEST
AGENT_SESSION
AGENT_EVENT
GENERATED_PATCH
FILE_MODIFICATION
HUMAN_REVIEW
APPROVAL
TEST_RESULT
BUILD_RESULT
CI_EVENT
IDE_EVENT
MANUAL_RECORD
```

This list is extensible. Unknown or new evidence types should be added deliberately, not invented ad hoc during recording.

---

## Provenance Class vs Integrity

`provenance_class` answers *how did we learn this?* `integrity` answers *how trustworthy is the artifact itself?* They are related but distinct:

```text
OBSERVED  + ORIGINAL   -> strongest evidence
REPORTED  + REPORTED   -> a party's claim; needs corroboration
INFERRED  + INFERRED   -> never sufficient alone for VERIFIED
UNKNOWN                -> insufficient information; do not upgrade
```

---

## Rules

1. An evidence record must correspond to a real artifact or statement. Do not fabricate evidence.
2. `INFERRED` evidence must never be presented as `OBSERVED`.
3. If the timestamp cannot be reliably determined, use `UNKNOWN` — do not guess.
4. `REPORTED` evidence records the claim, and attributes it to the reporting party in `source`. It does not confirm the claim.
5. Prefer a verifiable `source_locator` so a reviewer can independently inspect the raw evidence.

---

## Example (Markdown block form)

```text
EVIDENCE-001
  evidence_type:     AGENT_SESSION
  provenance_class:  REPORTED
  source:            claude-code-session
  source_locator:    session/2026-09-05T14-02-11Z.jsonl
  timestamp:         2026-09-05T14:02:11Z
  captured_at:       2026-09-05T14:40:00Z
  integrity:         REPORTED
  related_contributions: [CONTRIBUTION-001]
  description:       Session transcript shows the agent generated the initial
                     implementation of src/auth/token.ts in response to a human prompt.
  notes:             Transcript is agent-reported; corroborate with GIT_COMMIT and GENERATED_PATCH.
```

```text
EVIDENCE-002
  evidence_type:     GIT_COMMIT
  provenance_class:  OBSERVED
  source:            git
  source_locator:    3f9a1c2
  timestamp:         2026-09-05T14:38:20Z
  integrity:         ORIGINAL
  related_contributions: [CONTRIBUTION-001]
  description:       Commit 3f9a1c2 adds src/auth/token.ts (+142 lines) authored by a.dev,
                     committed 2026-09-05T14:38:20Z.
  notes:             Git author/committer metadata is trusted only as far as the repo's signing policy allows.
```
