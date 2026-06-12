# Structural Constraint and Agent Error Surface: Designing Extension Points for AI Collaboration

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-27T18:30
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Introduction

During an architectural refactoring, an AI agent helped centralize scattered classification functions into declarative data tables. The refactoring itself completed successfully, but post-implementation review revealed five agent-introduced issues — not logic errors, but naming residue, reference omissions, and semantic default bias. These issues shared a common characteristic: they all occurred where the agent had more freedom, and did not occur in structurally constrained operations.

This observation raises a question: how does the constraint level of code structures affect AI agent error rates? If the effect is significant, then "agent-friendliness" should be a design dimension when designing extension points.

## Analysis

### Three Failure Modes

The five observations reduce to three failure modes, each reflecting a cognitive characteristic of agents.

**Terminology drift.** The agent named a new module after a concept that had just been dissolved in the refactoring. The agent inherited the old term as a high-frequency token from the conversation context and did not proactively question whether it remained precise. In scenarios where an old concept has been dissolved, carrying over its terminology is the agent's default behavior — the old term's frequency in the context window far exceeds the new term's.

**Reference completeness.** After batch-renaming a type, one reference in a function pointer type signature was missed. Similarly, when updating governance documentation, the old terminology was updated in main paragraphs but cross-references in other sections went undetected. Agents in batch operations tend to process "primary locations" — import statements, type definitions, function signatures — while missing references in atypical positions (fn pointer type annotations, terminology within prose paragraphs).

**Semantic default bias.** A builder function was initially implemented to always return a successfully constructed object, while the spec required returning nothing when a precondition was unmet (conditional construction). The agent defaulted to the happy path — a build function should build something — ignoring the semantics of conditional operation. This deviation was caught during the verification phase.

### Error Surface and Structural Constraint

The three failure modes point to a common principle: **an agent's error surface is inversely proportional to the structural constraint of the extension point.**

```
error surface ∝ 1 / structural constraint
```

This relationship is clearly visible across three architectural stages observed in the same system. In the decorator chain stage, adding a new kind required understanding wrapping topology, chain ordering, and copying path resolution logic from existing implementations — a large error surface where the agent must simultaneously handle multiple unconstrained decision points. In the scattered classifier stage, each kind wrote its own resolution logic — a medium error surface, and one kind did in fact miss two of three path forms. In the data table stage, adding a kind requires only one fixed-structure table entry plus a build function — a small error surface, because the table's structure itself constrains what the agent can do.

| Architecture | Decisions Required for Extension | Error Surface |
|---|---|:---:|
| Decorator chain | Wrapping topology + ordering + logic duplication | Large |
| Scattered classifiers | Logic duplication + ordering position | Medium |
| Data table | Add one table line + write build function | Small |

### The Same Principle Family

"Structural constraint narrows error surface" is not a new discovery — it is a specific instance of a known principle family.

In type theory, "make illegal states unrepresentable" uses the type system to eliminate illegal states: if the type does not permit a certain combination, code cannot express that combination. Parse-don't-validate uses a parsing step to convert unvalidated strings into typed values: once in the typed domain, subsequent code cannot operate on unvalidated data. Both share the same mechanism — eliminating entire error classes through structural constraint rather than checking each error instance individually.

The data table's extension point does something similar: the table structure defines "one row = pattern + build function pointer," and the agent can only fill in values conforming to the type. By contrast, an unconstrained classification function is an arbitrary function body — the agent can do anything inside it, including incorrectly duplicating resolution logic.

## Reflection

### Convergent Tasks vs Divergent Tasks

Structural constraint is not universally beneficial. The distinction lies in task convergence.

**Convergent tasks** have a known answer shape; the work is filling in content. Adding a new resource kind, filling in a guard condition, implementing a build function — these are all convergent tasks. Constraint helps agents here: narrowing the search space, reducing the number of decisions to make.

**Divergent tasks** have an unknown answer shape; the work is exploring structure. The design exploration phase of the same refactoring was a classic example: the agent freely explored six approaches — shared helpers with distributed guards, centralized declarative tables, self-registration, algebraic data type dispatch, and more — requiring multiple rounds of challenge and revision to converge on the final design. Had the agent been constrained from the start to "just add a helper," the centralized table approach would never have been discovered.

The key qualifier: **extension points are inherently convergent tasks.** The definition of extension is adding a new variant within an existing structure — the shape is known (defined by the structure), the difference is known (defined by the requirement), and the work is filling in their intersection. Therefore, choosing a constrained structure for extension points is not sacrificing flexibility — it is aligning with the task's convergent nature.

### Expanding the Design Dimension

Traditional extension point design considerations include extension cost, type safety, and testability. This report's observation suggests adding a dimension: **agent error surface.** When teams use AI agents for collaborative development, the structural constraint level of extension points directly affects the probability of agent-introduced errors.

This is not about lowering standards for agents — the verification phase should catch all deviations. But the cost of interception is proportional to the number of deviations. If the extension point's design itself reduces deviations, the verification burden is lighter and overall development efficiency is higher.

## Conclusion

**Structural constraint narrows agent error surface — in convergent tasks.** This is an extension of "make illegal states unrepresentable" to AI collaboration: not eliminating illegal states through types, but eliminating the agent's illegal operation space through extension point structural design.

**Extension points are inherently convergent tasks.** Shape is defined by structure, difference is defined by requirement, and the agent's work is filling in the intersection. Choosing a constrained structure for convergent tasks is not limitation — it is alignment.

**Three agent failure modes can be mitigated by structural constraint:** Terminology drift — constrained structures reduce the degrees of freedom requiring naming. Reference completeness — centralization reduces the number of scattered references. Semantic default bias — fixed-structure table entries leave no room for happy-path assumptions.

**Agent safety is a design dimension for extension points.** Alongside type safety and extension cost. As AI collaboration becomes the norm, structural constraint serves not only human maintainability but also agent correctness.
