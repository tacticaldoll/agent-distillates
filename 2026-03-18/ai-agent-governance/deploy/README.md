# AI Agent Governance — Deploy Kit

Governance model for multi-submodule projects using Claude Code's `.claude/rules/` mechanism.

## What's Included

```
deploy/
    rules/
        common/
            governance-model.md     # Meta-rule: how to organize .claude/rules/
    README.md                       # This file
```

## Deployment (Manual)

Rules require manual deployment — copy to `.claude/rules/`:

```bash
# From your project root
mkdir -p .claude/rules/common
cp deploy/rules/common/governance-model.md .claude/rules/common/

# Then create your project-specific rule files:
# .claude/rules/common/architecture-decisions.md
# .claude/rules/common/engineering-disciplines.md
# .claude/rules/<submodule>/<stack>-conventions.md (with paths: frontmatter)
```

## Setup Steps

1. **Copy `governance-model.md`** to `.claude/rules/common/`
2. **Create rule files** for your project's specific constraints in `common/`
3. **Create submodule-scoped rules** with `paths:` frontmatter for stack-specific conventions
4. **Update your CLAUDE.md** to reference the governance model (remove inline rules, add §Governance Model section)

## Customization

- Add rule files to `common/` for cross-project constraints
- Create `<submodule>/` directories for scoped rules
- Use `paths:` frontmatter to control loading scope
- Follow the autonomy path when submodules need independence
