# Concept Adoption: Authority Conflicts and Selective Absorption When Integrating External Frameworks

<!-- front matter -->
**Structure**: Experience Report
**Date**: 2026-03-09T22:00
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Background

A legacy project was transitioning from reverse-engineered knowledge to spec-driven development. A governance framework was already in place — rules constrained coding quality, skills handled workflows, and the knowledge base was designated as transitional debt. What was missing was a structure to carry behavioral specifications. OpenSpec was evaluated as a candidate framework, offering a complete toolchain: directory structure (`specs/` + `changes/` + `archive/`), specification format (Requirement + Scenario + RFC 2119), CLI tooling (`openspec init/propose/accept`), and a JSON Schema-based validation engine.

The initial assumption was "integrate OpenSpec" — importing it as a complete framework. But the analysis revealed a deep contradiction: OpenSpec and the project's existing governance framework gave incompatible answers to the question "who is the ultimate authority on behavior." This report documents the decision journey from discovering the contradiction to selective adoption.

## Discovery

### Phase One: The Emergence of Authority Conflict

The initial evaluation focused on compatibility issues. OpenSpec's core assumption is that `specs/` is the single source of truth for behavior, and all other documents — rules, knowledge, code comments — are secondary. Meanwhile, the project's meta-rule (the existing governance framework's core mechanism, representing the other side of the authority conflict) defined a four-tier document hierarchy: Level 0 meta-rules have absolute authority, Level 1 guidelines are secondary, Level 2 specifications follow, and Level 3 knowledge is lowest.

The conflict's structure: OpenSpec assumes `specs/` is the highest authority; the meta-rule assumes Level 0 is the highest authority, with specifications as merely Level 2. The two systems are not operating in different domains — they give mutually exclusive answers to the same question ("which document holds the highest behavioral authority").

> **Decision Point**: Whether the authority conflict can be resolved through tier adjustment
> — Alternatives: Map OpenSpec's `specs/` as Level 2, letting the meta-rule continue governing it (maintain status quo, minimal change); elevate `specs/` to Level 0 peer status (restructure meta-rule); acknowledge the two are incompatible and choose one
> — Outcome: Chose the third option. The mapping approach violates OpenSpec's design intent — it assumes specifications are the highest authority, and demoting them to Level 2 effectively castrates its core value. The elevation approach introduces two Level 0s — the conflict resolution mechanism itself would need a higher arbiter, creating infinite regression. The incompatibility is structural, not configurational

This discovery redirected the evaluation — from "how to integrate" to "how to choose."

### Phase Two: Validating the Scaffolding Hypothesis

After the authority conflict was exposed, the natural question was: since the two are incompatible, which one should be retained?

Intuitively, the meta-rule is a carefully designed governance infrastructure — abandoning it means abandoning the entire tier system. But after tracing the rationale behind every definition in the meta-rule, a different picture emerged: the meta-rule's four tier definitions were not targeting essential system needs but specific problems of the transitional knowledge base (an instance of "the governed object disappearing" in the scaffolding validation).

Level 3 definitions governed the transitional knowledge base — but that knowledge base was being migrated. Level 2 definitions bridged external specification inputs — but `specs/` would replace this bridging mechanism. Levels 0 and 1 existed to give Levels 2 and 3 a conflict resolution framework — but if Levels 2 and 3 lose their governed objects, the conflict resolution framework also loses its purpose.

Every node in the dependency chain pointed to transitional problems rather than enduring needs. This meant the meta-rule was scaffolding, not infrastructure.

> **Decision Point**: The meta-rule's positioning — infrastructure or scaffolding
> — Alternatives: Infrastructure (retain and evolve, adjusting Level definitions for the new structure) vs. Scaffolding (extract useful fragments then retire)
> — Outcome: Scaffolding. All three validations passed: governed objects are disappearing (knowledge base being migrated), dependency graph points to transitional tasks, removal causes no functional degradation (rules and skills operate independently). Attempting to retain and evolve the meta-rule is finding new justification for a mechanism no longer needed — that is speculative design

### Phase Three: Decoupling Concepts from Tools

After confirming the direction — "choose OpenSpec, retire the meta-rule" — the next question was the scope of adoption. OpenSpec is a complete framework, encompassing both a concept layer (directory structure, specification format, delta change model) and a tool layer (CLI commands, JSON Schema validation).

The project already had a mature operational mechanism. A specification alignment skill compared specs against code before implementation; a knowledge precipitation skill routed knowledge to the correct location after work completion. Both were native skills in the AI agent environment, deeply integrated with the skill ecosystem (these two skills serve as evidence for "the existing toolchain already covers operational needs").

Introducing the OpenSpec CLI would mean two parallel paths existing at every operational node: a skill-driven path (AI auto-execution) and a CLI-driven path (manual command execution). Both paths operate on the same specification directories, but they do not share state — skills don't know what the CLI has done, and the CLI doesn't know what skills have done.

> **Decision Point**: Full adoption or concept-level adoption
> — Alternatives: Full adoption (install npm package, use CLI, configure schema) vs. Concept-level adoption (take only directory structure and format, operate with existing skills)
> — Outcome: Concept-level adoption. OpenSpec's core value lies in its mental model (specs as truth source, delta changes, structure as semantics), not in its CLI. The CLI's functions — initializing directories, generating proposal templates, executing accept/archive — can all be handled by existing skills. Introducing the CLI adds operational path fragmentation, not capability expansion

### Phase Four: Execution — Four-Step Transition

After decisions were complete, the transition executed in four steps.

Step one established the OpenSpec directory structure, converting ten previously accumulated specification requests into OpenSpec format, organized into four subdirectories by domain (the following four steps serve as the concrete execution record of "concept-level adoption").

Step two retired the meta-rule. Before deletion, two migratable knowledge fragments were extracted — a mechanism selection test and a guideline format specification — written to the project instruction file, then the meta-rule file was deleted. Related rules were updated in sync, replacing transitional knowledge base references with specification directory references.

Step three migrated all eight cross-cutting knowledge files from the transitional knowledge base to the specification directory. Each spec file adopted a dual-layer structure: an Architecture section carrying confirmed behavior (from reverse engineering) and a Requirements section carrying unconfirmed design intent (using RFC 2119 + Given/When/Then). After migration, the transitional knowledge base was deleted.

Step four updated workflow connections. The specification alignment skill was redirected to read from the specification directory; the knowledge precipitation skill's output routing was changed to the specification directory or co-located READMEs. Finally, the first complete change cycle was verified — creating a proposal, completing work, then archiving — confirming the end-to-end process was viable.

## Supplementary Knowledge

### The General Pattern of Selective Absorption

Adopting external frameworks exists on a spectrum, with multiple meaningful positions between full rejection and full adoption.

| Level | Description | Applicable When |
|-------|-------------|-----------------|
| Conceptual inspiration | Understand the framework's mental model, adopt no concrete mechanisms | The framework's problem domain doesn't match current needs |
| Concept adoption | Adopt the framework's mental model and structures, not its toolchain | Existing toolchain already covers the framework's operational needs |
| Tool integration | Adopt the framework's toolchain, running alongside existing systems | Framework tools provide capabilities the existing system lacks |
| Full import | Replace existing system with the framework | Existing system doesn't exist or is comprehensively inferior to the framework |

Each level is a legitimate choice — what matters is not how much is adopted but whether the reasoning for the choice is clear. Concept-level adoption is not "half-baked" integration but a precise trade-off made after analyzing the incremental value of the tool layer.

### Authority Mutual Exclusion as a Design Constraint

Two systems giving mutually exclusive answers to the same question is not necessarily a design flaw in either one. More commonly, each has made internally consistent choices within different design contexts. OpenSpec, in a spec-driven context, assumes specifications are the highest authority; layered governance, in a governance context, assumes the tier system is the highest authority. Both are self-consistent within their respective contexts.

The conflict is not about who is "wrong" but about the fact that a system cannot simultaneously have two "highest authorities" — this is a logical constraint, not a quality judgment. Recognizing this is more valuable than attempting reconciliation: reconciliation typically means one side pretends to accept the other's framework while still operating according to its original logic in practice.

## Decision Summary

| # | Decision | Rationale |
|---|----------|-----------|
| 1 | Authority conflict is irreconcilable | OpenSpec views specs as highest authority; meta-rule views tier system as highest authority. Mapping or elevation both introduce new contradictions |
| 2 | Meta-rule is scaffolding, eligible for retirement | Three validations: governed objects disappearing, dependency graph points to transitional tasks, no functional degradation upon removal |
| 3 | Concept-level adoption of OpenSpec | Core value lies in mental model not CLI; existing skills cover operational needs; CLI introduces path fragmentation |
| 4 | Dual-layer spec structure | Architecture (descriptive) + Requirements (prescriptive) — honestly distinguishing known behavior from design intent |
| 5 | Extract before demolish | Mechanism selection test and format specifications extracted to project instruction file before meta-rule deletion |

## Key Lessons

1. **A framework's value often concentrates in its mental model rather than its toolchain.** OpenSpec's most valuable contributions — specs as truth source, delta changes, structure as semantics — are all concept-level insights. The CLI and Schema are one implementation of these concepts, but not the only one. When the existing environment already has mature operational mechanisms, concept adoption captures the framework's value more precisely than tool integration.

2. **Authority mutual exclusion is a logical constraint, not a quality judgment.** When two systems' authority claims are mutually exclusive, the response should not be "which is better designed" but "a system cannot have two highest authorities." This transforms the problem from quality assessment to a selection problem — what's needed is not improving one of them but choosing the more suitable one for the current context.

3. **Selective absorption requires clear separation surfaces.** A framework's concept layer and tool layer can be separated, but the prerequisite is clearly identifying which parts are concepts (implementable with different tools) and which are tools (bound to a specific implementation). If a framework's concepts and tools are deeply coupled — a concept can only be realized through the framework's tools — then concept-level adoption is not viable. OpenSpec's concepts and CLI are loosely coupled, making selective absorption possible.

4. **Extract before demolish; never search while tearing down.** When retiring a mechanism, systematically identify migratable knowledge fragments and place them in new locations first, then execute the deletion. The risk of "search while tearing down" is omission — discovering after deletion that a fragment had value, but now it must be recovered from version history. Extraction is preventive; recovery is remedial — the cost difference between the two far exceeds intuition.
