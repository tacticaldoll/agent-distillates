---
name: precipitate
description: Precipitate knowledge into the project knowledge base. Use after completing a feature, investigation, bug fix, or significant discussion to capture reusable knowledge. Also use to audit and reorganize existing knowledge files. Auto-triggers after significant work sessions to capture reusable insights before context is lost.
argument-hint: "[scope] [topic]"
---

# Knowledge Precipitation

Extract insights from conversations, code changes, research, and project instructions into a structured knowledge base — a queryable repository that compounds development velocity over time.

## Output Routing (Phase-Aware)

If your project has a transition plan, check its current phase before writing. Output destination depends on transition phase:

| Phase | Routing Rule |
|-------|-------------|
| Early phases | Write to knowledge base (default) |
| Co-location phase | Co-locatable knowledge → co-located `README.md` in source tree; cross-cutting knowledge → knowledge base |
| Spec-driven phase | New knowledge → co-located `README.md` only; existing knowledge base files frozen |

**Co-location test**: Can the knowledge be owned by a single source directory? If yes → write to that directory's `README.md`. If no (cross-cutting) → write to knowledge base.

## Scope Resolution

Determine scope from `$ARGUMENTS` or conversation context:

| Argument | Scope | Location |
|----------|-------|----------|
| (empty) | Auto-detect from recent work | Nearest relevant knowledge directory |
| `<module>` | Specific module | `knowledge/<module>/` |
| `all` | Full audit across all knowledge directories | All knowledge directories |

## Process — 5 Phases

### Phase 1: Collect

Gather raw knowledge from all available sources:

1. **Conversation history** — Insights, decisions, debugging findings from this session
2. **Code changes** — What was built, modified, or deleted and why
3. **Project instructions** — Identify knowledge blocks that should live in the knowledge base instead (detailed architecture, data flows, module descriptions)
4. **Existing knowledge base** — Read the index first, then relevant files to avoid duplication

### Phase 2: Classify

For each knowledge item, determine:

| Dimension | Options |
|-----------|---------|
| **Module** | Which functional module does this belong to? |
| **Type** | `arch.md` (architecture) · `tech/<topic>.md` (technology) · `business/<topic>.md` (specs/logic) · `experience/<topic>.md` (lessons learned) |
| **Action** | Create new file · Update existing file · Merge into existing file · Delete obsolete file |

Decision rules:
- Architecture diagrams, component relationships, data flow → `arch.md`
- Build system, library usage, APIs, data structures → `tech/`
- Specifications, business rules, user-facing behavior → `business/`
- Debugging insights, pitfalls, remediation history → `experience/`

### Phase 3: Write

Every knowledge file MUST include YAML front matter:

```yaml
---
title: <concise title>
module: <module name>
type: arch | tech | business | experience
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
tags: [keyword1, keyword2, keyword3]
summary: <one-line summary for agent pre-screening — must be scannable without reading the file>
---
```

Content rules:
- **English only** — All knowledge files are AI-readable documents
- **Tables over prose** — Structured data is faster to scan
- **Mermaid diagrams** — Visualize flows, hierarchies, and relationships
- **One concept per file** in `tech/`, `business/`, `experience/` subdirectories
- **Verify against codebase** — Never write knowledge from assumption; confirm against actual source code
- **Concise** — If a section can be a table row, make it a table row

### Phase 4: Index

Update the knowledge base index for EVERY file created, modified, or deleted. The index is the mandatory first read for any agent.

Index format:

```markdown
---
title: Knowledge Base Index
updated: <YYYY-MM-DD>
purpose: Quick lookup index — read this FIRST before diving into individual files.
---

# Knowledge Base Index

## How to Use

1. **Read this index first** to identify which files are relevant to your task
2. **Read only the files you need** — avoid loading everything
3. **After precipitating new knowledge**, update this index accordingly

## Module: <module-name>

| File | Tags | Summary |
|------|------|---------|
| [<module>/arch.md](<module>/arch.md) | tag1, tag2 | One-line summary |
```

**Index sync validation** — After updating, verify:
- Every `.md` file under the knowledge base has a corresponding index entry
- No index entry points to a non-existent file
- Tags and summaries are current

### Phase 5: Prune

1. **Project instructions cleanup** — Remove detailed knowledge that has been precipitated into the knowledge base. Project instructions should retain ONLY:
   - Brief overview (2-3 sentences)
   - Build commands (quick reference)
   - Development principles and key conventions
   - Knowledge Base section pointing to knowledge directory

2. **Redundancy removal** — Delete or merge knowledge files that overlap. One source of truth per concept.

3. **Stale cleanup** — Remove knowledge that no longer reflects the codebase.

## Output Report

After precipitation, produce a summary:

```
## Precipitation Report

### Created
- `knowledge/<path>` — <what was added>

### Updated
- `knowledge/<path>` — <what changed>

### Deleted
- `knowledge/<path>` — <why removed>

### Project Instruction Changes
- <section removed/slimmed> — precipitated to `knowledge/<path>`

### Index
- `knowledge/index.md` — <entries added/updated/removed>
```

## Rules

- Knowledge MUST be verified against actual codebase before writing
- NEVER duplicate knowledge across knowledge files — one source of truth
- NEVER leave project instructions bloated with knowledge that exists in the knowledge base
- The index MUST stay in sync with actual files at all times
- Prefer updating existing files over creating new ones
- Empty scaffold directories (with no .md files) should be removed to reduce noise

## Auto-Trigger Conditions

This skill activates **automatically** when:

- A feature implementation, bug fix, or investigation has just been completed
- The conversation produced debugging insights, architectural decisions, or technical discoveries worth preserving
- Project instructions contain detailed knowledge blocks (data flows, module descriptions, architecture details) that should live in the knowledge base instead
- Existing knowledge files appear stale, inconsistent with current code, or contain redundant information
- The user requests knowledge organization, documentation cleanup, or knowledge base maintenance

**Detection rule**: If the session produced reusable technical knowledge that would benefit future sessions, trigger precipitation before the session ends. The cost of losing insights is higher than the cost of precipitating.
