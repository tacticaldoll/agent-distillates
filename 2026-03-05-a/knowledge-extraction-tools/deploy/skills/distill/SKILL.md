---
name: distill
description: Evaluate conversation content for extractable knowledge. Scans discussion topics, assesses depth and maturity, classifies knowledge nature and attribution tendency. Produces analysis table as reference — no prescriptive actions, no project coupling.
user-invokable: true
---

# Conversation Distillation

Scan the current conversation to identify topics with extractable knowledge value. Produce a structured analysis table as reference for the user's own decision-making.

## Principles

1. **Evaluation only** — Produce analysis results. Do not suggest next steps, target paths, or follow-up actions.
2. **Project-agnostic** — Do not reference any project's directory structure, governance framework, or specific tooling. The output must be portable across projects.
3. **Non-prescriptive** — Present findings as reference. The user decides what to do — or not do — with each topic.

## Process

### Phase 1: Thread Identification

Scan the conversation chronologically. Identify distinct discussion topics by semantic shift — a new subject, a new question, or a new problem constitutes a new thread.

**Granularity rule**: Split compound topics only when they reach independent conclusions. Keep them merged when they share a single conclusion chain.

### Phase 2: Per-Topic Assessment

For each identified topic, apply three independent assessments:

#### 2a. Nature Classification

Classify using first-match:

| # | Test | Nature |
|---|------|--------|
| 1 | Records a decision process with alternatives evaluated? | Decision Record |
| 2 | Derives principles applicable beyond the current project? | Universal Principle |
| 3 | Solves a specific problem with root cause analysis? | Problem Diagnosis |
| 4 | Documents a procedure or how-to? | Operational Knowledge |
| 5 | Describes what exists or what was observed? | Factual Record |

#### 2b. Maturity Rating

| Rating | Criteria |
|--------|----------|
| ★☆☆ Nascent | Initial observation — no iteration, no conclusion |
| ★★☆ Developing | Has structure, some iteration, partial conclusion |
| ★★★ Mature | Multiple iterations, refined conclusion, clear formulation |

**Indicators that raise maturity**: trial-and-error cycles, challenge-and-revision exchanges, explicit trade-off comparison, convergence on a principle.

#### 2c. Attribution Tendency

| Tendency | Signal |
|----------|--------|
| Internalize | Universal principle, methodology, thinking pattern — value is in understanding, not in enforcement |
| Externalize | Project-specific decision, configuration, architecture constraint — value is in persistence, not in memory |
| Both | Core insight should be understood by the person; specific application should be documented in the project |

### Phase 3: Volume Estimate

Estimate whether the topic has sufficient content for a standalone artifact:

| Estimate | Criteria |
|----------|----------|
| Sufficient | 3+ refined points, clear structure, enough depth for a standalone piece |
| Marginal | Has substance but better as a section within a larger piece |
| Insufficient | Brief mention, no depth, not worth extracting |

## Output Format

```markdown
# Conversation Distillation

**Date**: [YYYY-MM-DD]
**Topics identified**: [N]

| # | Topic | Nature | Maturity | Attribution | Volume | Notes |
|---|-------|--------|----------|-------------|--------|-------|
| 1 | ... | Decision Record | ★★★ | Externalize | Sufficient | ... |
| 2 | ... | Universal Principle | ★★☆ | Internalize | Marginal | ... |
| ... | | | | | | |
```

The table is the complete output. No recommendations section. No next-step suggestions.

## Rules

- **Zero coupling** — Do not reference any project's directory structure, governance framework, skill inventory, or toolchain
- **No side effects** — Do not create files, modify files, or invoke other skills
- **No prescriptions** — Do not suggest what the user should do with the results
- **Honest assessment** — If content is insufficient, say so. Do not inflate maturity or volume to justify extraction
- **Completeness** — Every identified topic must appear in the table, including low-value ones. Omission is a form of prescription

## Auto-Trigger Conditions

This skill activates **automatically** when:

- The user asks what was discussed, what was learned, or what's worth capturing from the conversation
- The user reflects on conversation value (e.g., "what should I take away from this session?")
- The user asks whether a topic has enough depth for a report or document
- The user signals end of session and asks for a summary of insights (value-oriented, not content-oriented)

**Detection rule**: Trigger when the user's intent is to assess conversation **value**, not conversation **content**. A request for "summarize our discussion" is content recall. A request for "what's worth extracting?" is value assessment. Only the latter triggers this skill.
