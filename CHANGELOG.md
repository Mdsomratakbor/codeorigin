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

### Notes

- The entire system is packaged as a single self-contained bundle folder (`codeorigin/`) so it can be
  copied into any project as one unit, or a single `<skill>/SKILL.md` can be copied for one skill.
