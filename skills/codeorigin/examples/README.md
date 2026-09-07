# Examples

These examples demonstrate the **same CodeOrigin model** applied across different agents, collaboration patterns, and languages. The records are illustrative — the identifiers, agents, and artifacts are examples, not real projects.

Every example obeys the same rules: evidence is classified (OBSERVED / REPORTED / INFERRED / UNKNOWN), confidence tracks evidence, verification is independent, unknown stays UNKNOWN, and collaboration is represented as multiple linked contributions.

| Example | Shows |
|---|---|
| [`single-agent/`](single-agent/) | One AI agent + human collaboration on one artifact (Claude Code, TypeScript) |
| [`multi-agent/`](multi-agent/) | A change handed off across multiple agents (Kiro → Codex → Claude Code → Human) |
| [`multi-language/`](multi-language/) | The identical model across .NET, Python, and Go with different agents/frameworks |

## Reading the Records

Records are shown in the Markdown block form used throughout the schemas. In a real project they might live in a provenance store, Git notes, a sidecar file, or a database — CodeOrigin is Markdown-first for the *contract*, not prescriptive about storage.
