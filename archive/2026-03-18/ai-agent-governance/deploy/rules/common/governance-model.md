# Governance Model

Engineering rules live in `.claude/rules/` — auto-loaded by Claude Code each session.

## Directory Structure

```
.claude/rules/
    common/                              # Always loaded — cross-project rules
        governance-model.md              # This file
        architecture-decisions.md        # Binding constraints
        engineering-disciplines.md       # Coding disciplines
        logging-standards.md             # Logging level classification
        code-review.md                   # Review checkpoints
    <submodule>/                         # Loaded when working in <submodule>/
        <stack>-conventions.md           # Stack-specific rules with paths: scoping
```

## Governance Hierarchy

| Level | Source | Governor |
|-------|--------|----------|
| `.claude/rules/common/` | Cross-project rules | Root project |
| `.claude/rules/<submodule>/` | Root project's rules for a submodule | Root project |
| `<submodule>/.claude/rules/` | Submodule's own rules | Submodule |
| `<submodule>/CLAUDE.md` | Submodule overview and build commands | Submodule |

When working inside a submodule, all applicable levels load automatically.

## Submodule-Scoped Rules

Use `paths:` frontmatter to restrict loading scope:

```markdown
---
paths:
  - "<submodule>/**"
---

# Conventions for <submodule>
...
```

## Autonomy Path

When a submodule needs full self-governance:
1. Move rules from `.claude/rules/<submodule>/` into the submodule's own `.claude/rules/`
2. Remove the directory from root `.claude/rules/`
3. No structural change needed — just file relocation

## Knowledge Routing

When new knowledge is produced, route by type:

| Knowledge Type | Destination |
|---------------|-------------|
| Binding constraint (always enforced) | `.claude/rules/common/` |
| Stack-specific convention | `.claude/rules/<submodule>/` |
| Requirement specification | Spec management system |
| Stable reference (catalog, mapping) | Reference directory |
| Project overview, build commands | `CLAUDE.md` |
