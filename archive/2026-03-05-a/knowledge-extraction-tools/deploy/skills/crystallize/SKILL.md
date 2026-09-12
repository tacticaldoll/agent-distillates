---
name: crystallize
description: Structure conversation content into reports. Matches content nature to appropriate format (experience report, analytical essay, technical note), applies quality gates, and generates structured output with optional deploy kit for cross-project reuse.
argument-hint: "[topic] [--generalize]"
user-invokable: true
---

# Report Crystallization

Transform conversation content into structured reports for knowledge internalization. The skill's core value is **structure selection** — matching content's natural shape to the right format, not forcing a fixed template.

Reports are **write-once internalization artifacts** — they exist to help the user absorb knowledge, not to be maintained or referenced by other documents.

## Process

### Phase 1: Input Resolution

| Source | Resolution |
|--------|-----------|
| `$ARGUMENTS` specifies topic | Use the specified topic from conversation |
| `$ARGUMENTS` includes existing report path(s) | **Re-crystallization** — existing reports as source material alongside conversation (see Phase 1b) |
| `$ARGUMENTS` includes project document path(s) | **Document crystallization** — read project documents as supplementary source material alongside conversation. No Phase 1b gate required |
| Prior distill evaluation exists | Reference the evaluation results; apply Attribution guidance (see Phase 6) |
| Neither | Scan conversation to identify the most substantial topic |

**Distill Attribution guidance** — when a prior distill evaluation exists:

| distill Attribution | Recommendation |
|---|---|
| Internalize | Proceed with crystallize (report for personal internalization) |
| Externalize | Inform user this topic is better suited for knowledge precipitation |
| Both | Proceed with crystallize; note that project-specific aspects may also warrant precipitation |

### Phase 1b: Re-crystallization Gate

When existing report paths are provided, verify all three conditions before proceeding. Failure on any condition → decline re-crystallization with explanation.

| # | Condition | Check | Fail action |
|---|-----------|-------|-------------|
| 1 | **Arc shift** | Does new content change the narrative arc, not just append detail? | Suggest standalone crystallize for the new topic |
| 2 | **Angle change** | Does the new report have a distinct perspective from the original? | No re-crystallization needed — crystallize the new topic independently |
| 3 | **Combined substance** | Do existing report + new content together pass Phase 4 Quality Gate? | Decline — insufficient for a well-formed report |

**Mechanics:**
- Read existing report(s) as source material — treat their content as facts, not as structural templates
- The new report is an independent artifact (new directory, new date) — the original is NOT modified
- Structure Selection (Phase 2) runs fresh — the new report may use a different structure than the original
- After completion, prompt the user whether to remove the superseded report directory. The skill does NOT delete automatically

### Phase 2: Structure Selection

Match content to structure using first-match:

| # | Test | → Structure |
|---|------|------------|
| 1 | Documents a multi-phase process with decisions made along the way? | Experience Report |
| 2 | Develops an argument from observation through analysis to principle? | Analytical Essay |
| 3 | Captures a specific technical finding with investigation process? | Technical Note |
| — | None match, or content too thin for any structure | **Decline** — inform user |

If a prior evaluation produced nature classifications, they serve as input:

| Evaluated Nature | Likely Structure |
|---------------|-----------------|
| Decision Record | Experience Report |
| Universal Principle | Analytical Essay |
| Problem Diagnosis | Technical Note |
| Operational Knowledge | Technical Note |
| Factual Record | Technical Note |

### Phase 2b: Batch Coordination Plan

**Trigger**: When Phase 1 + Phase 2 identify **≥ 2 reports** for the same session.

Produce a lightweight internal plan (not persisted to disk) that coordinates the batch:

| Element | Content |
|---------|---------|
| **Report list** | Topic + selected structure for each report |
| **Scope boundaries** | What each report covers and explicitly does NOT cover — prevents content overlap |
| **Reading order** | Sequence based on chronological or dependency relationships |
| **Handoff points** | Where one report's conclusion connects to the next report's starting conditions |

Each report's Phase 3–6 generation receives this plan as context. Specifically:
- **Background** uses the plan to scope starting conditions — shared prior context is established in the first report only; subsequent reports reference the outcome state, not re-explain it
- **Discovery** respects scope boundaries — content assigned to another report is not duplicated

**Constraint**: The plan is an internal coordination artifact. It is NOT written to disk and does NOT appear in any output.

### Phase 3: Structure Application

#### Writing Guidelines

Apply the readability guidelines in [references/readability.md](references/readability.md) to all report content. Key principles: introduce domain terms on first use, limit sentence complexity (≤ 2 independent concepts per sentence), maintain narrative continuity between phases, avoid orphan structural elements, and visualize complex relationships (3+ entities) with Mermaid diagrams wrapped in narrative context.

#### A. Experience Report

For content that documents **"what was done and why."**

| # | Section | Role |
|---|---------|------|
| 1 | Background | What existed before; what triggered the work |
| 2 | Discovery | Chronological phases, including trial-and-error; embeds Decision Point callouts when detected |
| 3 | Decisions | Summary table of all decisions *(auto — see Decision Point sub-pattern below)* |
| 4 | Supplementary Knowledge | Related concepts that inform the decisions *(optional — include only if substantive)* |
| 5 | Key Lessons | Transferable insights derived from the experience |

##### Decision Point Sub-Pattern

When the conversation contains decision flows, embed them as callouts **inline within Discovery** at the chronological point where each decision occurred. This preserves causal context — the reader sees the decision in the moment that triggered it.

**Callout format** within Discovery sections:

```markdown
> **Decision Point**: [one-line decision statement]
> — Alternatives: [what else was considered, and why rejected]
> — Outcome: [consequence of this decision — success, failure, or follow-up]
```

**Auto-detection** — scan conversation for these signals:

| Signal | Example |
|--------|---------|
| Alternatives evaluated | "3 options… recommend option 1" |
| Error → correction cycle | "changed → rejected by validator → reverted" |
| Explicit trade-off discussion | "A benefits from… but B is better because…" |
| User overrides or redirects direction | "not structural overlap — it's a terminology issue" |

**Activation rule**: When **≥ 2 Decision Points** are detected, enable this sub-pattern. Otherwise, keep the standard Discovery narrative without callouts.

**Density management** — when Decision Points are numerous, vary presentation to maintain narrative rhythm:

| Decision Points in report | Strategy |
|---------------------------|----------|
| 2–4 | **Standard** — full callout format for all |
| 5–7 | **Selective** — full callout for pivotal decisions; minor decisions woven into narrative prose |
| ≥ 8 | **Narrative-first** — most decisions as flowing prose; reserve full callout for the 2–3 most consequential decisions only |

**Consecutive callout limit**: No more than 2 full-format callouts in sequence without an intervening narrative paragraph (≥ 2 sentences).

See [references/readability.md](references/readability.md) for narrative-woven format examples.

**Decisions section behavior**:

| Decision Points detected | Decisions section |
|--------------------------|-------------------|
| ≥ 3 | **Summary table** — one-row-per-decision quick reference (detail lives in Discovery callouts) |
| < 3 | **Omit** — inline callouts are sufficient; no standalone section |

#### B. Analytical Essay

For content that argues **"what should be true and why."**

| # | Section | Role |
|---|---------|------|
| 1 | Introduction | Concrete trigger + problem statement |
| 2 | Analysis | Framework applied to the problem |
| 3 | Reflection | Broader implications, tensions, edge cases |
| 4 | Conclusion | Transferable principles |

#### C. Technical Note

For content that explains **"how something works or was solved."**

| # | Section | Role |
|---|---------|------|
| 1 | Problem | What was encountered |
| 2 | Investigation | What was tried, what was found |
| 3 | Finding | The core technical insight |
| 4 | Application | How to apply this finding *(optional)* |

### Phase 4: Quality Gate

Before generating, verify each section:

| Check | Fail Action |
|-------|------------|
| Section has < 2 substantive points | Merge with adjacent section |
| Section repeats another section's content | Deduplicate — keep where it fits best |
| Required section has no content | Re-evaluate structure selection — may be wrong fit |
| Overall report has < 3 sections with substance | **Decline** — content insufficient for standalone report |
| Decisions section duplicates Discovery callouts verbatim | Keep callouts as source of truth; Decisions section must be summary-only or omitted |
| Section has more structural lines (tables/callouts/lists) than prose lines | Add narrative context — transitions, implications, or scene-setting. NOT padding; only genuine connective tissue |

**Background alignment review** — after the full report is drafted, re-read Background against the completed Discovery section:

| Check | Fail Action |
|-------|------------|
| Background mentions prior work that Discovery never builds on | Remove — it is history, not a starting condition |
| Discovery assumes context that Background did not establish | Add the missing starting condition to Background |
| Background recaps the process of prior work instead of its outcome state | Rewrite to state what existed and what tension it created (see [readability.md § Background as Scene-Setting](references/readability.md)) |

**Constraints**: This review is the **terminal pass** — no further Quality Gate re-runs after it. Any text added or rewritten during this review must comply with [readability.md](references/readability.md) in-place (carry-forward, not loop-back).

### Phase 5: Generalization *(opt-in)*

Activated by `--generalize` flag or explicit user request:

1. Identify project-specific proper nouns (product names, internal paths, team names)
2. Replace with generic descriptions ("a legacy project", "the configuration file")
3. Verify no project-specific references leak through

**Default is off.** Project-specific context is often valuable — do not remove it unless asked.

When `--generalize` is active, produce the generalized version as a separate file (`report.en.md`) alongside the default language report.

### Phase 6: Derivative Assessment

After generating the report, assess whether derivative outputs are warranted.

#### Diagram Extraction

If the report contains Mermaid diagrams, extract each as a standalone `.mmd` file under `diagrams/`.

#### Deploy Kit Contribution

When a report documents tools or governance mechanisms with cross-project value, the generalized versions belong in a `deploy/` kit under the report's topic directory. The kit uses a **dual-track** approach aligned with Claude Code Plugin spec: skills are Plugin-installable; rules require manual deployment because the Plugin format has no native always-on rules mechanism.

**Assessment checklist** — consider adding to `deploy/` when ALL conditions pass:

| Condition | Check |
|-----------|-------|
| Maturity ★★★ | Tool has been iterated multiple times with confirmed design stability |
| Self-contained | Tool is understandable without the report context |
| Generalizable | Project-specific references can be replaced with configurable defaults |

Determine output type and deployment track using Mechanism Selection:

| Characteristic | → Type | Target | Deployment |
|---|---|---|---|
| Enforces mandatory constraint or governance checkpoint | Rule | `deploy/rules/` | **Manual** — copy to `.claude/rules/` |
| Extends capability — actionable task, reference knowledge, or domain expertise | Skill | `deploy/skills/` | **Plugin** — auto-loaded via `--plugin-dir` |

**Plugin manifest**: Auto-generate `.claude-plugin/plugin.json` from report front matter. The manifest covers `skills/` only — `rules/` are outside Plugin scope. See [references/deploy-kit.md](references/deploy-kit.md) for manifest generation rules and deployment guide template.

**Deployment guide**: Auto-generate `deploy/README.md` with dual-track instructions — Plugin install for skills, manual copy for rules.

**Source selection** — when a report documents tools that exist as project files (skills, rules), the deploy kit packages the **full existing files generalized**, not report-derived summaries:

| Condition | Source | Result |
|-----------|--------|--------|
| Tool exists as a project file AND passes assessment checklist | Read the actual project file, generalize per Phase 5 rules | Complete, deployable tool with all process phases, rules, and supporting files intact |
| Tool is described in the report but has no project file | Extract from report content | New file capturing the principle — may be less complete |

### Phase 7: Batch Reading Guide

When the same session produces **≥ 2 reports**, finalize the reading guide from the Phase 2b batch plan and the completed reports.

**Trigger**: Automatic when ≥ 2 report directories are created in the same crystallize session (same date prefix).

**Content**:

1. **Reading order** — Finalized from Phase 2b plan, verified against actual report titles. Each entry includes Structure type and a one-line summary, with a connection hint leading to the next report
2. **Cross-report connections** — Handoff points from Phase 2b plan, refined with actual section names from completed reports. Each connection is labeled with a relationship type
3. **Session context** — One-sentence summary of the overall session theme that produced these reports

**Relationship types** for cross-report connections:

| Type | Meaning | Arrow |
|------|---------|-------|
| **facet** | Different perspectives on the same event or topic | ←→ |
| **causal** | One report's conclusion drives another's starting condition | → |
| **refinement** | One report deepens or concretizes a concept from another | → |

**Constraint**: The guide references reports by title and section name only. It is a **navigation index**, not a downstream consumer — it does NOT quote or depend on report content.

### Phase 8: Report Index Maintenance

After all reports (and optional reading guide) are generated, update the persistent report index.

**Trigger**: Automatic after every crystallize invocation.

**Behavior**: Append or update entries for all reports generated in this session. If the session date section already exists, merge — do not duplicate.

**Index entry data sources**:

| Field | Source |
|-------|--------|
| Date | Session date |
| Sequence (#) | Phase 2b reading order (single report = 1) |
| Topic | Report title |
| Structure | Phase 2 structure selection result |
| Key Themes | 3–5 domain terms extracted from report Key Lessons or Conclusion |
| Relationships | Phase 2b handoff points, typed using relationship taxonomy |

## Output

### Directory Structure

Every crystallize invocation creates a directory under the reports directory and updates the persistent index:

```
reports/
  index.md                               # Persistent cross-session index (Phase 8)
  YYYY-MM-DD/
    guide.md                             # When ≥ 2 reports in same session
    <topic>/
      report.md                          # Default: project's preferred language
      report.en.md                       # When --generalize is active
      diagrams/                          # If report contains diagrams
        <name>.mmd
      deploy/                            # Per-report toolkit (opt-in, dual-track)
        .claude-plugin/
          plugin.json                    # Auto-generated manifest (skills only)
        skills/                          # Plugin track — auto-loaded via --plugin-dir
        rules/                           # Manual track — copy to .claude/rules/
        README.md                        # Dual-track deployment guide
```

### Report File Format

```markdown
# [Report Title]

<!-- front matter -->
**Structure**: [Experience Report | Analytical Essay | Technical Note]
**Date**: [YYYY-MM-DDTHH:MM]
**Model**: [model ID]
**Agent**: [agent environment and version]
**Source**: [conversation | reconstruction]

## [Section 1 — per selected structure]
...

## [Section N]
...
```

### Report Index Format

```markdown
# Report Index

Auto-maintained by crystallize. Contains report metadata for cross-session
discovery and deduplication. Does not quote report content.

## [YYYY-MM-DD]

**Theme**: [one-sentence session theme]
**Reports**: [N]

| # | Topic | Structure | Key Themes |
|---|-------|-----------|------------|
| 1 | [title] | [Experience Report / Analytical Essay / Technical Note] | [3–5 terms] |
| 2 | [title] | [structure] | [terms] |

**Relationships**:
- 1 ←→ 2 (facet): [description]
- 1 → 3 (causal): [description]
```

Sessions are listed in reverse chronological order (newest first). The Relationships subsection is omitted when a session produces only one report.

### Language Selection

Configure these defaults to match your project's preferred languages:

| Output | Language | Reason |
|--------|----------|--------|
| report (default) | Project's preferred language | User internalization, conversation language |
| report (`--generalize`) | English | Portable, shareable |
| batch reading guide | Same as default report language | Navigation for user |
| deploy kit (rules/skills/README) | English | AI-readable, cross-project portable |
| diagrams | English labels | Technical convention |

## Rules

- **Structure follows content** — Never force content into a structure. If no structure fits, decline with explanation
- **No padding** — Thin sections stay thin or merge. Never inflate content to fill a template
- **Always directory output** — Every crystallize invocation creates a directory under the reports path. No flat file mode
- **Reports are not referable** — No other document (project instructions, knowledge base, rules, skills) may reference or depend on content in reports. If an insight needs to be referenced, it belongs in the knowledge base or co-located docs
- **Generalization is opt-in** — Default preserves project-specific context
- **Deploy kit requires quality gate** — Do not add tools to `deploy/` unless all 3 conditions pass (maturity, self-contained, generalizable)
- **Honest decline** — If content is insufficient for a well-formed report, say so directly rather than producing a hollow report
- **One report, one structure** — Do not mix structures. If content spans multiple natures, produce separate reports or let the user choose the dominant angle
- **Distill attribution guidance** — When distill results are available, respect Attribution classification. Externalize topics should be redirected to precipitation
- **Re-crystallization is not editing** — Re-crystallization produces a new independent report. The original report is never modified. This preserves the write-once principle
- **Re-crystallization requires arc shift** — Do not re-crystallize when new content only adds minor detail. All three Phase 1b conditions must pass
- **Batch plan coordinates, not constrains** — The Phase 2b batch plan prevents content overlap and establishes handoff points between reports. It is an internal coordination artifact — not persisted, not referenced by output
- **Batch guide is navigation, not reference** — The reading guide references reports by title and section name for navigation purposes. It does NOT quote report content or create downstream dependencies
- **Narrative over structure** — Reports are for human internalization. When a section reads like a specification (more tables and callouts than prose), add narrative connective tissue. This is not padding — it is the difference between a reference document and a readable report
- **Deploy kit prefers full source** — When a report documents tools that exist as project files, the deploy kit packages the full files generalized, not report-derived summaries
- **Deploy kit uses dual-track** — Skills are Plugin-installable; rules require manual deployment. This separation reflects the Plugin spec: Plugins support `skills/` natively but have no always-on rules mechanism
- **Index is metadata, not reference** — The report index contains metadata for discovery and deduplication. It does NOT quote report content or create downstream dependencies
- **Relationship types are exhaustive** — All cross-report connections must use one of three types: facet, causal, refinement

## Auto-Trigger Conditions

This skill activates **automatically** when:

- The user asks to write a report, essay, or structured document from conversation content
- The user asks to structure discussion insights into a written artifact
- The user has evaluated conversation topics and selects specific ones for report generation
- The user asks to merge, reconstruct, or update existing reports with new session content

**Detection rule**: Trigger when the user's intent is to produce a structured written artifact from conversation content. Do not trigger for simple summarization — summaries don't need structure selection methodology.
