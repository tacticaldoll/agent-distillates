# Deploy Kit Reference

Reference material for crystallize Phase 6 deploy kit generation. The deploy kit uses a **dual-track** approach: skills are Plugin-installable; rules require manual deployment.

## Official Spec Context

Per the [Claude Code Plugins Reference](https://code.claude.com/docs/en/plugins-reference):

- **`skills/`** is the recommended component directory for extending Claude Code capabilities
- **`commands/`** is labeled **"legacy; use `skills/` for new skills"** in the official File Locations table
- **No native `rules/` mechanism** exists in the Plugin format — Plugins cannot provide always-on governance rules that behave like `.claude/rules/`

This dual-track design follows the official guidance: use `skills/` for Plugin-compatible components, and provide a separate manual path for rules that the Plugin system cannot represent.

## Dual-Track Directory Structure

```
deploy/
├── .claude-plugin/
│   └── plugin.json              # Auto-generated manifest (skills only)
├── skills/                      # Plugin track — auto-loaded via --plugin-dir
│   └── <name>/
│       └── SKILL.md
├── rules/                       # Manual track — copy to .claude/rules/
│   └── <name>.md
├── README.md                    # Dual-track deployment guide
└── CHANGELOG.md                 # Version history (optional)
```

### Track Comparison

| Track | Directory | Deployment | Behavior after install |
|-------|-----------|------------|----------------------|
| **Plugin** | `skills/` | `claude --plugin-dir ./deploy` | Auto-loaded, model can invoke based on description |
| **Manual** | `rules/` | Copy to `.claude/rules/` | Always-on, loaded every session unconditionally |

### Why Not `commands/`

The official spec still supports `commands/` but labels it legacy. More importantly, `commands/` produces user-invokable slash commands — semantically wrong for always-on governance rules. Rules need `.claude/rules/` placement to get always-on behavior, so the honest approach is to provide them as `rules/` files with manual deployment instructions.

## Plugin Manifest Generation

Auto-generate `.claude-plugin/plugin.json` from session metadata. The manifest covers **skills only** — rules are outside Plugin scope.

```json
{
  "name": "<session-topic-kebab-case>",
  "version": "1.0.0",
  "description": "<one-line summary from report topic>",
  "keywords": ["<extracted-from-report-content>"],
  "license": "MIT"
}
```

**Field generation rules:**

| Field | Source | Rule |
|-------|--------|------|
| `name` | Session topic | Convert to kebab-case, max 64 chars, lowercase + numbers + hyphens only |
| `version` | Fixed | `1.0.0` for first generation |
| `description` | Report title + structure | One sentence: "[Structure type] covering [topic]" |
| `keywords` | Report Key Lessons | Extract 3–5 domain terms from lessons |
| `license` | Default | `MIT` — user may override |

## Rule File Format

Rules in `deploy/rules/` follow the standard `.claude/rules/` format:

```markdown
---
description: <rule description>
---

# <Rule Title>

## Purpose
...

## Rules
...

## Checklist
...
```

This format is directly compatible with `.claude/rules/` — copy the file as-is, no transformation needed.

## Generalization Process

When generating deploy kit files from project-specific tools:

1. **Replace project-specific proper nouns** with generic placeholders:
   - Product names → "the project", "the application"
   - Internal paths → configurable path variables or generic descriptions
   - Team names → "the team", "developers"

2. **Remove project-specific references** that don't generalize:
   - Specific file paths (e.g., `context/frontend/arch.md`)
   - Project-specific tool names unless they are the subject of the skill
   - Internal jargon not defined within the document

3. **Preserve structural patterns** that are universally applicable:
   - Decision tables, classification flows, quality gates
   - Process phases and their relationships
   - Checklist items that apply broadly

## Deployment Guide Template

Generate `deploy/README.md` using this template:

```markdown
# [Kit Name]

[One-paragraph description of what this kit provides and its origin]

## Requirements

- **Agent environment**: Claude Code (skills use `$ARGUMENTS`, `user-invokable` frontmatter, `.claude/` directory conventions)
- **Plugin install**: Requires `claude --plugin-dir` support
- **Rules**: Require `.claude/rules/` directory (Claude Code native mechanism)

Other agent environments (Cursor, Gemini CLI, Windsurf) are not supported.

## Skills (Plugin Install)

Install skills via Claude Code Plugin system:

​```bash
claude --plugin-dir ./deploy
​```

Skills are auto-loaded and namespaced as `/[kit-name]:[skill-name]`.

### Included Skills

| Skill | Description |
|-------|-------------|
| `[skill-name]` | [one-line description] |

## Rules (Manual Install)

Rules require manual deployment — the Plugin format has no native always-on rules mechanism.

Copy each rule file to your project's `.claude/rules/` directory:

| Source | Target | Description |
|--------|--------|-------------|
| `rules/[name].md` | `.claude/rules/[name].md` | [one-line description] |

Rules become active immediately in the next Claude Code session.

## Version History

See [CHANGELOG.md](CHANGELOG.md) for version history.
```

Populate the tables automatically from the actual files generated in the deploy/ directory.
