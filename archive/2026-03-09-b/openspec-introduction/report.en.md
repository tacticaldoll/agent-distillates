# Structure as Semantics: OpenSpec's Spec-Driven Development Model

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-09T21:30
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Introduction

When a long-running legacy project completes its governance framework — rules and skills separated, knowledge base treated as transitional debt, document hierarchy established — the next question emerges: how to transition from "having process constraints but no specifications" to "spec-driven development." The existing knowledge base records the system's current state — architecture, data flows, component interactions — yet cannot answer a fundamental question: is a given behavior a design intent or a historical accident?

This gap cannot be filled by more governance rules. Governance answers "how to do things correctly"; specifications answer "what counts as done." What is missing is a structure to carry specifications — not just a file format, but a framework that lets directory structure itself carry semantics.

OpenSpec was evaluated in precisely this context. It is a spec-driven development framework whose core assertion can be condensed into one sentence: **specifications are the sole source of behavioral truth, and directory structure is the sole mechanism for declaring authority**.

## Analysis

### Specifications as Truth Source

OpenSpec's first fundamental assumption is the distinction between "descriptive" and "prescriptive." Descriptive answers "how the system works" — the output of reverse engineering belongs to this category. Prescriptive answers "how the system should work" — only reviewed design intent belongs here. The two may be textually similar, but their authority is entirely different: descriptions record facts; specifications define contracts.

The `specs/` directory is OpenSpec's core structure. All files under `specs/` are automatically treated as prescriptive documents — they describe "how the system should work," not "how the system happens to work." This semantic is not declared by some meta-rule but determined by the directory location itself.

The specification format uses a three-layer structure. The outermost layer is the `### Requirement:` heading, defining a behavioral requirement. The middle layer uses RFC 2119 keywords (MUST / SHOULD / MAY), precisely controlling each statement's enforcement level — MUST is an inviolable contract, SHOULD is a conditional expectation, MAY is an explicitly optional item. The innermost layer is `#### Scenario:` with Given/When/Then tripartite format (from Behavior-Driven Development, BDD), converting abstract requirements into verifiable concrete scenarios.

This three-layer design serves two audiences simultaneously: human readers understand intent through requirement titles and scenario descriptions; automated tools execute verification through RFC 2119 keywords and Given/When/Then structure. Specifications are not just documents — they are bidirectional contracts.

### The Delta Change Model

OpenSpec's second core concept addresses a practical problem: specifications are not static; they need to evolve. Directly modifying files in `specs/` is the most intuitive approach, but this blurs change boundaries — differences between before and after require a diff to see, and reviewers cannot understand the change's intent without historical comparison.

Delta specs are OpenSpec's answer to this problem. When changing established behavior, proposers do not modify `specs/` directly but create a delta description under `changes/<change-name>/`. Delta specs use three semantic sections:

- **ADDED** — New requirements or scenarios that did not previously exist
- **MODIFIED** — Changes to existing requirements, including before-and-after comparison
- **REMOVED** — Requirements or scenarios being removed, including the rationale for removal

Each proposal under `changes/` has an independent lifecycle: proposed, reviewed, accepted or rejected. Accepted proposals are merged back into `specs/`, then archived to `changes/archive/`. This forms a complete traceability chain — at any time, examining `archive/` answers "why did this behavior become what it is now."

The delta model's deeper value goes beyond process improvement. It elevates "change" from a side effect of file editing to a first-class citizen of system evolution — changes have their own names, their own directories, their own lifecycles, and can be independently discussed, reviewed, and traced.

### Structure as Governance

The first two concepts — specifications as truth source and delta changes — both depend on a deeper design principle: directory structure itself carries semantics, requiring no additional governance text to declare authority.

Consider a concrete comparison. A traditional layered governance framework requires a meta-rule to declare: "Level 2 is specifications, with prescriptive authority; Level 3 is knowledge, with descriptive status; higher levels win." These declarations introduce triple maintenance burden: maintaining the meta-rule itself, maintaining each document's level designation, and arbitration costs when conflicts arise. More fundamentally, these semantic declarations depend on all participants correctly understanding and following the level definitions — if someone places descriptive content at Level 2, the level system does not automatically prevent it.

OpenSpec's approach encodes semantics into physical structure. Content under `specs/` is prescriptive because it is in `specs/` — this "because" is not logical inference but definition itself. Content under `changes/` is a proposal because it is in `changes/`. Content under `archive/` is history because it is in `archive/`. No meta-rule needed to declare these semantics, no labels needed to mark document nature, no conflict resolution mechanism needed to arbitrate levels.

This does not mean structural governance is foolproof. It can still be misused — placing descriptive content in `specs/` is just as easy as labeling it Level 2. The difference is in the visibility of failure modes: a misplaced file can be spotted during directory browsing, while a mislabeled level requires checking metadata one by one. Structural governance does not eliminate errors but makes them easier to see.

### RFC 2119: The Precision Ladder

OpenSpec's choice of RFC 2119 as the specification language is not accidental. The most common problem when describing behavior in natural language is ambiguity — "the system will handle this case" can be interpreted as either "must handle" or "might handle." RFC 2119 defines five precision levels, each with different compliance obligations:

| Keyword | Semantics | Compliance Obligation |
|---------|-----------|----------------------|
| MUST / SHALL | Absolute requirement | Cannot be violated; violation means non-compliance |
| MUST NOT / SHALL NOT | Absolute prohibition | Cannot be performed; performance means non-compliance |
| SHOULD / RECOMMENDED | Conditional expectation | Follow by default, but may deviate with sufficient reason |
| SHOULD NOT / NOT RECOMMENDED | Conditional objection | Avoid by default, but may adopt with sufficient reason |
| MAY / OPTIONAL | Explicitly optional | Implementer decides |

These five levels form a precision ladder. In specs without RFC 2119, "the system should automatically rename conflicting files" — "should" might mean MUST (must implement) or SHOULD (recommended but deviable). With RFC 2119, the same statement becomes two completely different contracts, and the implementer's obligation is clearly distinguishable.

### Given/When/Then: Scenarios as Acceptance Criteria

Behavior-Driven Development's (BDD) Given/When/Then tripartite format provides verifiable concrete scenarios for each requirement. The three sections each carry different semantic roles:

- **Given** (precondition) — The scenario's initial state, describing what state the system is in before the behavior occurs
- **When** (trigger event) — The specific action or event that causes the behavior
- **Then** (expected result) — What state the system should be in after the behavior completes

This structure's value lies in eliminating the translation cost between requirements and tests. In traditional processes, requirement documents are written by PMs, test cases are designed by QA based on their understanding of requirements — two translations, two opportunities to introduce deviation. Given/When/Then makes requirements themselves the skeleton of test cases; PMs, developers, and QA see the same description.

## Reflection

### The Greenfield Assumption and Brownfield Reality

OpenSpec's design contains an implicit greenfield assumption: write specs first, then write code. But the brownfield reality is that code already exists, and the vast majority of behaviors lack specifications. Directly placing reverse-engineered knowledge into `specs/` creates false authority — formally they are "specifications," but substantively they are merely descriptions of the current state.

This tension is not an OpenSpec defect but a structural challenge every spec framework faces in brownfield environments. The resolution direction is honestly distinguishing the nature of two kinds of knowledge: confirmed behaviors (Architecture sections, using descriptive language) and unconfirmed design intent (Requirements sections, using RFC 2119 + Given/When/Then). The dual-layer structure acknowledges the difference rather than concealing it.

### Applicability Boundaries of Structural Governance

The principle of structure-as-governance has applicability boundaries. It works best at coarser semantic granularity — "is this document a spec or a proposal" can be expressed through directory location, while "is this rule MUST or SHOULD" requires text-level marking. Directory structure carries classification semantics, not fine-grained enforcement levels.

In other words, structural governance and textual governance are not either-or. Structural governance handles classification questions ("what nature is this document"), textual governance handles degree questions ("how mandatory is this requirement"). OpenSpec's design uses both simultaneously — directory structure determines document classification, RFC 2119 keywords determine statement enforcement.

## Conclusion

OpenSpec's core contribution is not inventing a new specification language — RFC 2119 and Given/When/Then are both established industry practices. Its contribution lies in combining these practices into a structure-as-semantics framework: directory location determines document nature, the delta model makes change a first-class citizen, and the archival process forms a traceable decision history.

Three transferable principles:

1. **Semantics should be encoded in structure before text.** Structure is harder to violate than text — a file's physical location can be verified during directory browsing, while a document's metadata labels require individual checking. The maintenance cost of governance text grows linearly with document count; structural governance costs remain relatively fixed.

2. **Change deserves its own identity.** When change is merely a side effect of diffs, its boundaries, intent, and rationale are hidden in version history. When change is an independent, named, archivable entity, it becomes discussable, reviewable, and traceable. The delta model's value is converting the implicit process of evolution into explicit decision records.

3. **Description and prescription are different authority levels; conflating them is more dangerous than lacking specs.** Without specs, everyone knows specs are missing; when descriptions masquerade as specs, everyone thinks specs exist. The former motivates action; the latter conceals problems. Honestly labeling the nature of knowledge — even when acknowledging that most behaviors are not yet specified — is a more pragmatic choice than pursuing formal completeness.
