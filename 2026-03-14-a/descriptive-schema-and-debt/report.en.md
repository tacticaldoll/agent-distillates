---
title: "Surviving the Spec Sparse Period: Descriptive Schema and Debt Cleansing Guide"
description: "Proposing a methodology to utilize Descriptive Schemas with observational markers to contain the current state and transform technical debt during periods lacking complete specifications."
---

<!-- front matter -->
**Structure**: Technical Note
**Date**: 2026-03-14T16:50
**Model**: Gemini 3 Flash
**Agent**: Antigravity IDE 1.19.6.0
**Source**: conversation

## Problem

During the early stages of a project lacking a Single Source of Truth (SSOT), or when facing intricate Legacy Systems, development teams frequently fall into a Spec Sparse Period. In this phase, the system architecture runs on ad-hoc modules without a complete, binding specification (Spec) to guide new development.

Forcing the adoption of Spec-Driven Development (SDD) during this phase leads to two dead-ends:
1.  **Anarchy**: Engineers act autonomously—some reverse-engineer code, others invent their own interface types. The system quickly breeds innumerable, incompatible terminologies, ultimately culminating in massive Semantic Collapse.
2.  **Bureaucratic Paralysis**: Teams are forced to halt feature development, sinking into endless specification meetings. Deciding whether a timestamp should be a string or an integer can consume weeks. This induces severe Analysis Paralysis, zeroing momentum.

The core question: If we cannot rely on a perfect spec to drive development, how do we prevent the system from collapsing and steadily converge knowledge and architecture through this painful transition?

## Investigation

To answer this, we examined various methodologies handling contradictions between knowledge and specification. The critical breakthrough lies in altering the spec's "Protection Level" and "Alignment Perspective".

Acknowledging that demanding a perfect spec during the Spec Sparse Period is an unrealistic fantasy, how should we proceed with daily coding and AI Agent collaboration?

The answer: **Embrace the chaos, but contain it structurally.** We not only need an eventual mandatory spec, but we urgently need a surrogate spec that "tolerates defects, conflicts, and disputes". We define this as the Descriptive Schema.

This Schema's purpose is not to dictate hard laws of "how things must be done," but to serve as a "current state health diagnostic". More importantly, when deploying multiple AI Agents to reverse-engineer or scan legacy code in the background, we must provide them with a definitive Exploration Boundary. We instruct Agents: when identifying undocumented interface properties, write them entirely into this descriptive schema, and flag any deviations with a conflict tag (`x-conflict`) for future resolution.

## Finding

Investigations reveal that efficiently reducing knowledge debt and transitioning smoothly to spec-driven development relies on precise Attribution Routing and debt-cleansing procedures. The following are three concrete debt transformation action frameworks:

### Action 1: Transforming "Vision" into "Descriptive Schema"

Early projects often document future expectations (e.g., expecting to support GraphQL pagination next quarter). If this "planned" design is written into the formal definition file as an "implemented" spec, it induces Forward-looking Spec Drift.

The correct cleansing approach is to write it into the Schema but enforce a marker indicating it as an unimplemented vision.

```yaml
# [Correct/High Resolution] Revealing vision as an observational target
PaginationGraphLinks:
  description: "[PLANNED VISION] Future vision structure for associated graphs. Not yet implemented. Agents must not assume its existence during inference."
  x-governance-level: "Level-3-Vision"
  properties: 
    # ...
```

*Finding Benefit*: The team acquires a tangible target for structural discussion. Concurrently, during compile-time or Agent context ingestion, this object is explicitly downgraded or rejected, eradicating hallucinations caused by future specs.

### Action 2: Converting "Old Requirements and Conflicts" into TODO Lists

When reverse scanning reveals that the current system returns a rudimentary comma-separated string, while outdated requirement docs claim it returns a complete "detail array object", a conflict surfaces immediately.

Avoid Speculative Fixes within the schema—do not forcibly declare it a beautiful array to appease expired documentation. That constitutes a Factually Erroneous Technical Debt. 

The correct cleansing approach strips this "gap between spec and reality" from the schema's properties, transferring it into a lifecycle-bound work item (e.g., an Issue Tracker Ticket or a code-level `TODO`), while downgrading the schema back to the cruelly realistic "string description".

*Finding Benefit*: Gaps and debts no longer hide deep within documents; they enter trackable scheduling systems. It becomes a pending engineering task rather than a long-term misleading system lie.

### Action 3: Replacing Human Style Guides with Agent Rules

Many projects maintain dozens of pages of human-readable "Development Style Guides" dictating rules like "No Pinyin in variable naming" or "Do not expose internal models directly from the repository layer". Forcing humans or Agents to read and memorize this Prescriptive Knowledge is futile.

The correct cleansing approach translates all these guidelines into configuration files for constraint tools (e.g., Linter rule sets, or validation gate scripts built into workflow agents). Subsequently, completely destroy those plaintext style guides.

*Finding Benefit*: This thoroughly implements idioka (mistake-proofing). Written principles are 100% executed by machines, completely sealing the space for prescriptive knowledge drift, ensuring baseline quality never regresses.

Executing these three core actions untethers the system from perfect, unrealistic specs. It is replaced by an honest Descriptive Schema accurately flagging conflicts, fortified by automated rule guardrails resolutely defending the system's baseline. This is the sole path to survive the Spec Sparse Period and reach system homeostasis.
