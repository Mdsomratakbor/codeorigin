# CodeOrigin

**A portable collection of Markdown-first skills that enable software projects to record, attribute, trace, verify, and report AI and human contributions across different coding agents, SDD frameworks, programming languages, and development environments.**

CodeOrigin is a stable **AI/Human contribution, provenance, attribution, evidence, and traceability skill layer**. It is designed to be dropped into virtually any software development workflow.

> Track who — and what — really wrote your code.

**Topics:** `provenance` · `attribution` · `contribution-tracking` · `ai-attribution` · `traceability` · `evidence` · `markdown` · `skills` · `agent-agnostic` · `ai-assisted-development`

---

## Core Principle

> The project implementation is replaceable. The AI agent is replaceable. The programming language is replaceable. The SDD framework is replaceable. The development environment is replaceable. CodeOrigin's AI/Human contribution tracking and provenance model is the stable foundation.

The following are **replaceable**:

- AI Agent (Kiro, Codex, Claude Code, OpenCode, Cursor, Copilot, custom agents, ...)
- Programming Language (.NET, Python, Java, TypeScript, Go, Rust, ...)
- IDE / Development Environment
- SDD Framework (Spec Kit, OpenSpec, custom, or none)
- Git Provider
- CI/CD Platform
- Implementation Technology

The following are **stable**:

- Contribution Model
- Attribution Model
- Provenance Model
- Evidence Model
- Traceability Model
- Verification Model

---

## What CodeOrigin Is

- Agent-agnostic, framework-agnostic, language-agnostic, IDE-agnostic, vendor-neutral, repository-agnostic.
- Markdown-first: the canonical contract is human- and machine-readable Markdown.
- Portable: a single `SKILL.md` can be copied into another project without the rest of the repo.
- Composable: skills are independently usable but combine into a complete record.
- Extensible: new skills and adapters can be added without changing the canonical model.

## What CodeOrigin Is NOT

- NOT an SDD framework.
- NOT a coding agent.
- NOT a project management system.
- NOT a replacement for Spec Kit, OpenSpec, Kiro, Codex, Claude Code, OpenCode, Cursor, Copilot, or any other tool.

CodeOrigin only tracks the **provenance and contribution dimension** of development. It consumes evidence produced by external systems; it does not duplicate their functionality.

---

## The Stable Layer

```text
              CODEORIGIN
                  │
       ┌──────────┼──────────┐
       │          │          │
 Attribution  Provenance  Evidence
       │          │          │
       └──────────┼──────────┘
                  │
             Traceability
                  │
             Verification
                  │
          AI/Human Accounting
                  │
                  ▼
        Any Project / Any Agent
```

---

## Questions CodeOrigin Helps Answer

- Who contributed to this change?
- Was the change produced by a human, AI, or both?
- Which AI agent participated? Which human reviewed or modified the result?
- What evidence supports the attribution?
- What percentage of a change can be attributed to AI or humans, and by what measurement?
- Which commits, files, patches, or artifacts are associated with an AI agent?
- Which human approved the change?
- What was generated, modified, reviewed, rejected, or accepted?
- How did a contribution evolve from generation to final implementation?
- Can the claimed contribution be independently verified?
- What information is known, inferred, or unknown?

---

## Repository Layout

This repository *is* CodeOrigin. Copy the whole repository into any project's skills directory to adopt the entire system as one unit, or copy an individual `<skill>/SKILL.md` for just one skill.

```text
.
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── SECURITY.md
├── CHANGELOG.md
│
├── contribution-tracking/SKILL.md      # The portable skills (each folder self-contained)
├── ai-attribution/SKILL.md
├── human-attribution/SKILL.md
├── agent-provenance/SKILL.md
├── change-provenance/SKILL.md
├── evidence-collection/SKILL.md
├── contribution-traceability/SKILL.md
├── contribution-verification/SKILL.md
├── agent-activity/SKILL.md
├── human-review/SKILL.md
├── ai-human-accounting/SKILL.md
├── contribution-reporting/SKILL.md
├── provenance-audit/SKILL.md
│
├── protocols/              # How the model is applied
│   ├── contribution.md
│   ├── provenance.md
│   ├── attribution.md
│   ├── evidence.md
│   └── agent-events.md
│
├── schemas/                # The canonical, stable data contracts
│   ├── contribution-record.md
│   ├── provenance-record.md
│   ├── attribution-record.md
│   ├── evidence-record.md
│   └── agent-event.md
│
├── adapters/               # OPTIONAL: how each ecosystem feeds evidence in
│   ├── kiro.md
│   ├── codex.md
│   ├── claude-code.md
│   ├── opencode.md
│   ├── cursor.md
│   ├── copilot.md
│   ├── spec-kit.md
│   └── openspec.md
│
└── examples/               # Same model demonstrated across environments
    ├── single-agent/
    ├── multi-agent/
    └── multi-language/
```

---

## Core Vocabulary

**Contributor types**

```text
HUMAN
AI_AGENT
AUTOMATED_TOOL
UNKNOWN
```

**Evidence provenance classes** (never conflate these)

```text
OBSERVED   - directly recorded from a trusted source
REPORTED   - stated by a party (agent or human), not independently confirmed
INFERRED   - deduced from indirect signals; never treated as fact
UNKNOWN    - insufficient information
```

**Confidence levels**

```text
HIGH
MEDIUM
LOW
UNKNOWN
```

**Verification status**

```text
VERIFIED
PARTIALLY_VERIFIED
UNVERIFIED
CONTRADICTED
UNKNOWN
```

---

## Non-Negotiable Rules

1. **Never fabricate** agent identity, human identity, authorship, contribution percentage, timestamps, sessions, commits, evidence, reviews, or approvals.
2. **Unknown stays UNKNOWN.** Do not infer an agent from code style. Do not infer human authorship merely because no AI record exists.
3. **Evidence is not inference.** Always classify as OBSERVED / REPORTED / INFERRED / UNKNOWN.
4. **Verification is independent.** An agent claiming it did something is not proof.
5. **Collaboration is first-class.** A change may be human-directed, AI-generated, human-modified, and human-approved; preserve the chain.
6. **LOC is not intellectual contribution.** "AI generated 70% of changed lines" must not become "AI contributed 70% of the engineering work."

---

## Install

CodeOrigin follows the open [Agent Skills](https://agentskills.io) convention: each skill is a folder containing a `SKILL.md` that starts with YAML frontmatter (`name` + `description`). Agents preload each skill's name and description and load the full body when it becomes relevant. Install by copying skills into the location your agent scans.

### Whole pack

```bash
# clone alongside your project
git clone https://github.com/Mdsomratakbor/codeorigin.git

# Kiro
mkdir -p your-project/.kiro/skills
cp -r codeorigin/* your-project/.kiro/skills/codeorigin/

# Claude Code
mkdir -p your-project/.claude/skills
cp -r codeorigin/* your-project/.claude/skills/codeorigin/

# GitHub Copilot (repo-scoped skills folder)
mkdir -p your-project/.github/skills
cp -r codeorigin/* your-project/.github/skills/codeorigin/
```

### One skill only

Because each skill is self-contained, you can copy just one:

```bash
cp -r codeorigin/ai-attribution your-project/.kiro/skills/ai-attribution
```

The schemas and protocols it references are included in the same repo; copy `schemas/` and `protocols/` too if you want the skill's links to resolve locally.

### As a git submodule (to track upstream updates)

```bash
git submodule add https://github.com/Mdsomratakbor/codeorigin.git .codeorigin
```

## Usage

Once installed, an agent (Kiro, Claude Code, Copilot, OpenCode) discovers each skill by its `description` and loads it when your task matches — for example, "record who wrote this change" activates `contribution-tracking`. You can also invoke a skill directly by name where your agent supports it.

A typical run for one change:

1. **Collect evidence** (`evidence-collection`) — the spec, the agent session, the commit.
2. **Attribute** (`ai-attribution` / `human-attribution`) — who did what, at what confidence.
3. **Record the contribution and its lineage** (`contribution-tracking`, `change-provenance`).
4. **Verify** (`contribution-verification`) — corroborate reported claims against observed evidence.
5. **Report** (`contribution-reporting`) — a human-readable summary that preserves confidence and verification.

Provenance records are Markdown and can live wherever you like — a `.provenance/` folder, Git notes, or a sidecar file. See [`examples/`](examples/) for a full worked run; the [Spec Kit](adapters/spec-kit.md), [OpenCode](adapters/opencode.md), and [Kiro](adapters/kiro.md) adapters describe how those tools feed evidence in.

## Getting Started

1. Read the [schemas](schemas/) to understand the canonical model.
2. Read the [protocols](protocols/) to understand how records are produced and linked.
3. Copy the skill(s) you need (each is a `<skill>/SKILL.md` folder in this bundle) into your project.
4. Optionally read the relevant [adapter](adapters/) for your coding agent to learn how it can supply evidence.
5. See [examples/](examples/) for the same model applied across different agents and languages.

## Portability

Each skill lives in its own `<skill>/SKILL.md` folder and is self-contained. To adopt everything, copy the whole repository. To adopt one skill, copy that single `SKILL.md`. Skills reference the schemas and protocols by name, but degrade gracefully when used standalone.

## License

See [LICENSE](LICENSE). CodeOrigin is open source.
