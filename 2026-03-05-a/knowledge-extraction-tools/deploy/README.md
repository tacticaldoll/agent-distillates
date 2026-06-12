# Knowledge Pipeline

A toolkit for extracting, structuring, persisting, and auditing project knowledge from AI-assisted conversations. Designed for teams that use AI coding assistants and want to systematically capture the decision rationale, design trade-offs, and lessons learned that live in ephemeral conversation sessions.

The toolkit implements a three-stage pipeline: **evaluate** (distill) → **structure** (crystallize) → **persist** (precipitate), with a terminology audit tool for governance document consistency.

## Requirements

- **Agent environment**: Claude Code (skills use `$ARGUMENTS`, `user-invokable` frontmatter, `.claude/` directory conventions)
- **Plugin install**: Requires `claude --plugin-dir` support
- **Rules**: Require `.claude/rules/` directory (Claude Code native mechanism)

Other agent environments (Cursor, Gemini CLI, Windsurf) are not supported.

## Skills (Plugin Install)

Install skills via Claude Code Plugin system:

```bash
claude --plugin-dir ./deploy
```

Skills are auto-loaded and available via `/knowledge-pipeline:<skill-name>` commands.

### Included Skills

| Skill | Description |
|-------|-------------|
| `distill` | Evaluate conversation content for extractable knowledge. Scans topics, assesses depth/maturity, classifies knowledge nature and attribution tendency. Output: analysis table only — no prescriptive actions |
| `crystallize` | Structure conversation content into reports. Matches content to format (Experience Report, Analytical Essay, Technical Note), applies quality gates, optional generalization and deploy kit generation |
| `precipitate` | Precipitate knowledge into the project knowledge base. Extracts insights from conversations and code changes into structured, queryable knowledge files |
| `terminology-audit` | Audit terminology consistency and native spec alignment across governance documents. Four-dimension scan with severity classification and actionable corrections |

## Rules (Manual Install)

Rules require manual deployment — the Plugin format has no native always-on rules mechanism.

Copy each rule file to your project's `.claude/rules/` directory:

| Source | Target | Description |
|--------|--------|-------------|
| `rules/knowledge-capture-reminder.md` | `.claude/rules/knowledge-capture-reminder.md` | Proactively suggest distillation when conversation accumulates extractable knowledge, before context compression loses detail |

Rules become active immediately in the next Claude Code session.

## Quick Start

1. **Install the plugin**: `claude --plugin-dir ./deploy`
2. **Copy rules**: `cp deploy/rules/*.md .claude/rules/`
3. **Start a work session** — the knowledge-capture-reminder rule will automatically suggest distillation when your conversation accumulates sufficient decision topics
4. **Run `/distill`** when suggested — produces an analysis table of extractable knowledge
5. **Run `/crystallize [topic]`** to structure insights into a report
6. **Run `/precipitate`** to deposit project-specific knowledge into your knowledge base
7. **Run `/terminology-audit`** periodically to check governance document consistency

## Version History

### 1.0.0 (2026-03-05)

- Initial release
- Four skills: distill, crystallize, precipitate, terminology-audit
- One rule: knowledge-capture-reminder
- Crystallize supports: Experience Report, Analytical Essay, Technical Note structures
- Crystallize features: batch coordination, reading guide, report index, deploy kit generation
- Terminology audit features: four-dimension scan, external authority verification precedence
