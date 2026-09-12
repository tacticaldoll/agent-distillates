# Classification Dispatch Evolution: Root Cause of Decorator Chain Semantic Disconnection, Three-Stage Trade-offs, and the Impossible Triangle

<!-- front matter -->
**Structure**: Experience Report
**Date**: 2026-03-27T18:30
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Background

A system needs to resolve user-supplied paths into resource descriptors carrying operation behaviors. The system has 16 resource types — network shares, ISO mounts, filesystem snapshots, external devices, and more — each with distinct identification conditions and operation behaviors. Some types share the same path resolution logic but differ in guard conditions; others use entirely different path schemes.

The initial design used the GoF Decorator pattern deployed as a chain. After operating for a while, a subtle problem emerged: when the system asked a resource "what kind are you?", both an ISO mount and a filesystem snapshot answered "readonly" — their identity had been overwritten by a behavior name. This is semantic disconnection.

This report documents the three-stage evolution from discovering this problem to the final solution, and the structural constraint that emerged along the way.

## Discovery

### Act One: Why Decorator Chain Swallows Identity

The Decorator Chain's structure was a vertical-horizontal superposition: the chain (horizontal) handled classification — iterating each decorator's `try_resolve()` in sequence, with the first to claim winning; nesting (vertical) handled behavior composition — the outer decorator overrode specific operations (e.g., writes return read-only errors), while unoverridden operations delegated to the inner layer. Both directions are individually correct — the problem was that they were superimposed on a single interface, and `kind_name()` lived on that same interface.

Concretely, the ISO mount's composition was `ReadonlyDecorator { inner: SyscallDecorator }`. The snapshot's composition was also `ReadonlyDecorator { inner: SyscallDecorator }`. When external code called `kind_name()`, the outermost `ReadonlyDecorator` answered "readonly" — a behavior name, not a kind identity. ISO and snapshot became indistinguishable.

The root cause was not a flaw in nesting or chaining themselves, but the superposition of three orthogonal concerns — horizontal classification, vertical behavior composition, and identity query — onto a single abstraction. The outer layer in the vertical direction was named by behavior, obscuring the kind identity produced by the horizontal direction.

> **Decision Point**: Split the dual-layer decorator interface into three separate concerns: an operations trait (pure operations), free functions for classification, and an enum for identity
> — Alternatives: Add kind_name override on Decorator trait — treats symptom, not cause; every decorator needs manual override
> — Outcome: Identity problem solved, but introduced new scattering problems

### Act Two: The Cost of Scattering

The migration solved the identity problem. Each kind got a named ops struct with a 1:1 corresponding enum variant. Identity queries now returned "iso" instead of "readonly".

But classification functions were scattered across each kind file, each writing its own path resolution logic. One kind had complete handling of three path forms (relative path, symlink, absolute path). Another kind hand-rewrote the path extraction logic but only handled one form — missing two forms entirely.

This was not an isolated case. Of the 16 resource types, 6 are share-based, sharing the same path resolution logic but needing different guard conditions (type-code checks, hardware abstraction layer calls). If each kind wrote its own classifier, there would be 6 near-identical copies of path resolution code, each potentially missing some form.

Another problem was that the classification registry's ordering implicitly controlled priority. Each kind's classifier appeared self-contained, but whether it could successfully claim depended on its position in the array. This "self-containment is an illusion" situation made reasoning difficult for maintainers.

> **Decision Point**: Centralize path resolution into a shared helper module; kinds retain only ops + build
> — Alternatives: Keep per-kind classifiers but extract shared helper — still has scattered classification, half-centralized
> — Outcome: Path resolution consistency problem eliminated; the missing path forms gap fixed

### Act Three: How Far to Centralize?

Having decided to centralize path resolution, the next question was whether classification rules themselves should also be centralized.

The initial proposal was helper + distributed guard: a shared resolver unifies path resolution, but each kind still has its own guard function. The catch-all fallback kind needed to be made explicit — from implicit "placed last" to an explicit line of code.

> **Decision Point**: Completely dissolve per-kind classifiers — kinds hold no "is this me?" logic; classification rules centralized as declarative tables in the dispatch module
> — Alternatives: Kind self-registration (guard as const exported from kind file, dispatch module collects references) — more self-describing but classification knowledge still spans two locations; keep per-kind guard functions — still has scattered classification
> — Outcome: Kind files slimmed to pure ops + build (thin facade); classification rules auditable in a single table

The final design uses two-phase declarative dispatch: a pattern table handles prefix/contains matching for pattern-based kinds, a guard table handles share-based kinds with predicate conditions, and the default share kind is an explicit fallback.

Scale projection supported this choice. With all 16 kinds enabled: decorator chain would need 16 decorator structs plus complex wrapping topologies; scattered classifiers would have multiple path resolution copies; a data table would have ~20 entries, each a single line.

> **Decision Point**: Separate the two tables — pattern rules (string patterns) and share guards (metadata predicates) use different structures
> — Alternatives: Unified rule enum wrapping both matching mechanisms — over-abstraction, the two families have fundamentally different matching signatures
> — Outcome: Each structure is clean; adding a new kind requires one line in the correct table

### Epilogue: The Unexpected Harvest of Naming Precision

After implementation, review discovered that a newly created module's filename carried over the dissolved classification concept. Renaming revealed ambiguity with an existing kind module — both related to "share" but with different responsibilities. A third, more precise name was chosen to match the struct it contained.

This three-round naming process yielded a principle: file names must precisely reflect their single responsibility and must not be ambiguous with other modules at the same level. Type names should correspond to their containing file name. Parameter names must follow structural changes — stale terminology in function signatures creates semantic ghosts.

## Decisions

| # | Decision | Rationale | Alternatives Rejected |
|---|----------|-----------|----------------------|
| D1 | Split decorator interface into ops trait + classification functions + identity enum | Three orthogonal concerns should not share one interface | Add kind_name override on decorator interface |
| D2 | Centralize path resolution into shared helper | 6 share-based kinds share identical logic; scattered copies lead to omissions | Keep per-kind classifiers + extract shared functions |
| D3 | Completely dissolve per-kind classifiers; centralize as declarative tables | Classifier "self-containment" is an illusion; ordering is global knowledge | Kind self-registration; keep per-kind guard functions |
| D4 | Separate pattern table and guard table | Two families have fundamentally different matching mechanisms | Unified rule enum |
| D5 | Explicit fallback for catch-all kind | Catch-all semantics differ from guard; one explicit line is clearer than implicit last-position | Add catch-all to guard table tail |

## Supplementary Knowledge

### The Impossible Triangle

The three-stage evolution reveals a structural constraint: **no facade, no god function, no scattered classification** cannot all be satisfied simultaneously.

| | No Facade | No God Fn | No Scattered Classification |
|---|:---:|:---:|:---:|
| Decorator chain | ✓ | ✓ | ✗ |
| Self-registration | ✓ | ✓ | ✗ |
| God function | ✓ | ✗ | ✓ |
| Data table | ✗ | ✓ | ✓ |
| ADT dispatch | ✗ | ✓ | ✓ |

The closure condition of this triangle depends on language capabilities. In languages lacking type discovery or reflection (such as Rust), classification rules cannot be automatically collected from kind definitions and must be manually registered — manual registration is some form of centralization. Languages with annotation scanning (Java, C#) can approach breaking this triangle: classification rules attached as annotations on kinds, with the framework scanning and collecting automatically; kinds remain self-contained while classification remains auditable in one pass.

Under static-dispatch language constraints, data table sacrifices self-containment (kinds become thin facades) — the lowest-cost trade-off. Kind files remain 1:1, remain self-describing, and build functions remain in kind files.

### Scale Degradation Matrix

| Approach | 4 kinds (current) | 16 kinds (full) | Degradation Mode |
|----------|:---:|:---:|---|
| Decorator chain | 4 decorators + simple wrapping | 16 decorators + complex topology | Wrapping combinatorial explosion |
| Scattered classifiers | 4 path resolution copies | 16 copies (6 near-identical) | Copy → omission |
| Data table | ~8 table lines | ~20 table lines | Linear growth, manageable |

## Key Lessons

**Vertical-horizontal superposition is the root cause of semantic disconnection.** Nesting (vertical behavior composition) and chaining (horizontal classification) are each correct; superimposing them on one interface causes the problem. When a single abstraction carries horizontal classification, vertical behavior composition, and identity query responsibilities simultaneously, the outer layer's naming in the vertical direction (typically by behavior) obscures the identity produced by the horizontal direction.

**"Self-containment" can be an illusion.** Each kind owning its own classifier appears self-contained, but registry ordering implicitly controls who can claim. True independence requires eliminating global dependencies — and classification priority is inherently global knowledge.

**Classification globality is an inescapable constraint.** Regardless of the pattern used, mutual exclusion and priority ordering of classification rules is global knowledge. Any correct solution requires some form of centralization — the difference is only in expression (imperative if-else, declarative table, type-level dispatch) and how identity is preserved.

**Naming must follow structural changes.** After dissolving a concept, carrying over its terminology leaves semantic ghosts in the codebase. File names, type names, and parameter names are all carriers of semantics — their precision directly affects subsequent developers' (and AI agents') understanding.
