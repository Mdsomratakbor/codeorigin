# Changelog

All notable changes to CodeOrigin are documented here. The project uses semantic versioning per skill and for the canonical schemas.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

### Added

- Initial bundle scaffold under `codeorigin/`: README, LICENSE, CONTRIBUTING, SECURITY, CHANGELOG.
- Canonical schemas: contribution-record, provenance-record, attribution-record, evidence-record, agent-event.
- Protocols: contribution, provenance, attribution, evidence, agent-events.
- Core skills (each `<skill>/SKILL.md`, all 14 §24 sections): contribution-tracking, ai-attribution,
  human-attribution, agent-provenance, change-provenance, evidence-collection, contribution-traceability,
  contribution-verification, agent-activity, human-review, ai-human-accounting, contribution-reporting,
  provenance-audit.
- Adapters: kiro, codex, claude-code, opencode, cursor, copilot, spec-kit, openspec.
- Examples: single-agent, multi-agent, multi-language.

- Standard Agent Skills frontmatter (`name` + `description`) on every `SKILL.md`, making the pack
  installable and auto-discoverable by Kiro, Claude Code, GitHub Copilot, and other agents.
- README Install and Usage sections with per-agent install paths and a worked run sequence.
- Interop adapters: `adapters/agent-trace.md` and `adapters/ai-attestation.md`, which consume existing
  attribution formats (Agent Trace, ai-attestation) as evidence rather than competing with them.
- README repositioning: CodeOrigin framed as the evidence/verification/audit layer, with a
  "Related Work & Interop" section distinguishing it from attribution formats and detectors.

### Notes

- The entire system can be copied into any project as one unit, or a single `<skill>/SKILL.md` can be
  copied for one skill.
