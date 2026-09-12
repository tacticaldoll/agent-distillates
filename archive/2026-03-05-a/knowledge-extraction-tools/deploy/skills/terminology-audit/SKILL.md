---
name: terminology-audit
description: Audit terminology consistency and native spec alignment across governance documents. Use when rules, skills, or knowledge files are created, modified, or reviewed — or when terminology drift is suspected.
argument-hint: "[scope]"
---

# Terminology Audit

Audit governance documents for terminology consistency, native spec alignment, structural skeleton compliance, and cross-reference integrity. Produces a findings table with actionable corrections.

## Scope Resolution

`$ARGUMENTS` determines audit scope:

| Argument | Scope | Files scanned |
|----------|-------|---------------|
| (empty) | Full audit | All governance documents |
| `rules` | Rules only | Rules directory (`*.md`) |
| `skills` | Skills only | Skills directory (`*/SKILL.md`) |
| `knowledge` | Knowledge base only | Knowledge base (`**/*.md`) |
| `<filename>` | Single file | The specified file |

**Full audit** always includes: project instructions, transition plan (if any), all rule files, all skill files, knowledge base index.

## Process — 4 Phases

### Phase 1: Build Reference Glossary

Before scanning, establish the authoritative term definitions from two sources:

**Source A — Platform native spec** (highest authority per meta-guideline alignment clause):

Adapt the table below to your actual platform and its terminology:

| Concept | Authoritative Term | Definition |
|---------|-------------------|------------|
| Rules directory | Rule | Modular instructions by topic — optional path-scoping |
| Skills directory | Skill | Capability extensions — actionable tasks, reference knowledge, domain expertise |
| Agents directory | Agent | Custom subagents — isolated context, independent permissions and tools |
| Project instructions | Project instructions | Persistent project instructions — auto-loaded every session |
| Skill definition file | Skill definition file | Native format with YAML front matter |

**Source B — Project meta-guideline (Level 0)**:

| Concept | Authoritative Term | Source |
|---------|-------------------|--------|
| Document levels | (per project) | Document Hierarchy |
| Mechanism types | (per project) | Mechanism Selection |
| Document types | (per project) | Document Classification |
| File structures | (per project) | Structural Skeletons |

When Source A and Source B conflict, Source A prevails (per meta-guideline alignment clause).

### Phase 2: Scan for Findings

For each file in scope, check four dimensions:

#### 2a. Terminology Consistency

Detect terms that refer to the same concept but use different wording:

| Finding type | Example |
|-------------|---------|
| **Synonym drift** | "process with input/output" vs "capability extension" for Skill |
| **Verb drift** | "distill" used in precipitate when precipitate is the downstream tool |
| **Stale term** | Old Mechanism Selection wording surviving in downstream documents |

#### 2b. Native Spec Alignment

Detect descriptions that contradict or under-represent platform native definitions:

| Finding type | Example |
|-------------|---------|
| **Definition mismatch** | Skill described as "triggered by situation" instead of "extends capability" |
| **Missing native field** | Skill skeleton omitting a key frontmatter field |
| **Role description drift** | Path role not matching native spec behavior |

#### 2c. Structural Skeleton Compliance

For each file type, check against meta-guideline's Structural Skeletons:

| File type | Required elements |
|-----------|-------------------|
| Rule | `description` frontmatter, Purpose section, Rules section, Checklist section |
| Skill | YAML frontmatter with `name` + `description`, process/output sections |
| Knowledge | YAML frontmatter: `title`, `module`, `type`, `created`, `updated`, `tags`, `summary` |

#### 2d. Cross-Reference Integrity

Detect broken or stale references between documents:

| Finding type | Example |
|-------------|---------|
| **Dead reference** | Link to non-existent file or section |
| **Stale quotation** | Inline quote of another document's text that no longer matches the source |
| **Circular dependency** | Skill A references Skill B's output, Skill B references Skill A's output |

### Phase 3: Classify Severity

| Severity | Criteria |
|----------|----------|
| **Error** | Contradicts native spec or meta-guideline; produces incorrect behavior if followed |
| **Warning** | Inconsistent terminology that could cause confusion but doesn't produce wrong behavior |
| **Info** | Minor style inconsistency or improvement opportunity |

### Phase 4: Produce Report

## Output Format

```markdown
# Terminology Audit Report

**Date**: [YYYY-MM-DD]
**Scope**: [scope description]
**Files scanned**: [N]
**Findings**: [N errors, N warnings, N info]

## Reference Glossary Snapshot

[Table of authoritative terms used for this audit — from Phase 1]

## Findings

| # | Severity | File | Line | Dimension | Finding | Correction |
|---|----------|------|------|-----------|---------|------------|
| 1 | Error | ... | ... | Stale term | Uses old wording | Update to current authoritative term |
| ... | | | | | | |

## Summary by Dimension

| Dimension | Errors | Warnings | Info |
|-----------|--------|----------|------|
| Terminology Consistency | N | N | N |
| Native Spec Alignment | N | N | N |
| Skeleton Compliance | N | N | N |
| Cross-Reference Integrity | N | N | N |
```

## Known Discrepancies

Runtime schema may contradict official documentation. When this occurs, **runtime behavior is the authoritative source** (per code-as-documentation precedence: code > docs > knowledge). Record confirmed discrepancies here to prevent re-discovery.

| Field / Concept | Documentation says | Runtime says | Confirmed by |
|-----------------|-------------------|-------------|--------------|
| *(example)* `field-name` | `documented-spelling` | `runtime-spelling` | Runtime schema validator (YYYY-MM-DD) |

## Rules

- **Reference glossary first** — Always build the glossary from authoritative sources before scanning. Never audit against memory or assumption
- **Runtime over docs** — When official documentation contradicts runtime schema validation, runtime prevails. Check Known Discrepancies before flagging findings
- **Native spec prevails** — When project terms diverge from platform native spec, the native term is correct (per meta-guideline alignment clause)
- **Evidence-based** — Every finding must cite file and line number
- **Actionable corrections** — Every finding must include a specific correction, not just "fix this"
- **No content review** — This skill audits terminology and structure, not the correctness of technical content
- **No side effects** — This skill produces a report only. It does NOT modify files. Corrections are applied by the user or in a separate step after review
- **Honest scope** — If a file falls outside the audit dimensions (e.g., pure source code), skip it rather than force-fitting

## Auto-Trigger Conditions

This skill activates **automatically** when:

- Rules, skills, or knowledge files are created or substantially modified
- Meta-guideline (Level 0) is modified — downstream terminology may become stale
- The user asks about terminology consistency, naming conventions, or spec alignment across governance documents
- A new platform spec update is discussed that may affect existing definitions

**Detection rule**: Trigger when document structure or terminology is the subject of discussion, not when document content is the subject.

## Experience: External Authority Verification Levels

**Context**: A meta-guideline declared "native definitions prevail" but did not define how to verify what the native definition actually is. A first audit assumed official documentation was the authority, applied a bulk rename, and was immediately contradicted by the platform's runtime schema validator.

**Generalizable principle**: Any governance framework that aligns to an external authority MUST define a verification precedence for that authority. Documentation alone is insufficient — it may lag behind or contradict the runtime.

**Verification precedence for external platform native spec**:

| Priority | Source | Nature |
|----------|--------|--------|
| 1 | Runtime schema validator (IDE/platform diagnostics) | Actual behavior — what the system accepts |
| 2 | Source code (if accessible) | Design intent — what the system was built to do |
| 3 | Official documentation | Descriptive — what the system is said to do |

**Operational implication for this skill**: Phase 1 (Build Reference Glossary) MUST prefer runtime-verified definitions over documentation-sourced definitions. When a new glossary term is sourced from documentation, flag it as unverified until confirmed by runtime behavior or schema validation.
