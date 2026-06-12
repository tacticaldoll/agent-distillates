# Document Purpose Purity: One Document, One Expectation

**Structure**: Analytical Essay
**Date**: 2026-03-24T00:00
**Model**: claude-sonnet-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

---

## Introduction

Every document creates an expectation in its reader before they read it. The expectation is formed by the document's type (specification, reference, changelog), its location in the information architecture, and its stated purpose. When the document consistently fulfills that expectation, readers build accurate mental models of what it contains and how to use it.

The problem examined here is what happens when a single document serves multiple purposes — when a reference document contains changelog entries, or when a mapping catalog mixes stable facts with time-sensitive status notes. The document can remain individually accurate, but the mixture dilutes its usefulness and corrupts the mental model readers build of it.

---

## Analysis

### Mental Models and Document Types

Readers don't engage with documents neutrally. They approach with a mental model: "this is a reference document, so it contains stable facts I can rely on; this is an experience document, so it captures lessons from a specific event; this is a spec, so it defines what must be true."

These mental models are efficient. They let readers find what they need quickly and interpret what they find correctly. A reference document reader isn't primed to discount information that may be outdated. An experience document reader isn't expecting facts they need to maintain.

When a document mixes types, it creates a mismatch between the mental model the reader brings and the content the document actually contains. The reader either applies the wrong interpretive frame, or spends cognitive effort determining which frame applies to each section. Either outcome degrades the document's utility.

Consider a mapping catalog whose "Notes" column contains both behavioral constraints from legacy code (archaeology findings) and design remarks about the new architecture (implementation decisions). A reader examining legacy constraints needs a historically skeptical posture; a reader examining new architecture notes needs a forward-looking design orientation. Mixing both in the same column requires readers to continuously switch between modes, with no visual or structural cue indicating when to switch.

### Mixed-Purpose Maintenance and the Staleness Asymmetry

Different document types have different update cadences. Reference documents are updated rarely — they contain stable facts that don't change often. Changelogs are updated frequently — they track events as they occur. Specifications evolve with requirements.

When these are mixed in a single document, the fast-changing content creates pressure to touch the document frequently. Each touch is an opportunity to inadvertently alter or fail to update the stable content. The stable content becomes suspect: was this not updated because it's still accurate, or because the person updating the changelog didn't notice it needed updating?

This is the staleness asymmetry: the presence of frequently-updated content makes it harder to trust the rarely-updated content in the same document, even when the rarely-updated content is correct.

A concrete example: a reference document describing a component's design contains a changelog recording early misconceptions and corrections. The changelog is accurate — it records what really happened. But its presence implies that other parts of the document may also need similar corrections, and readers have no way to know which parts have been corrected and which haven't. This uncertainty is introduced by mixed purpose, not by the content itself.

### Separation of Concerns in Information Architecture

The software engineering principle of separation of concerns — different responsibilities belong in different modules — applies directly to information architecture. Each document should have a single, well-defined responsibility. This makes documents:

- **Predictable** — readers know what to expect before they read
- **Maintainable** — changes have a clear home; nothing gets updated in the wrong place
- **Trustworthy** — the document's accuracy can be evaluated against a single standard

When a document accumulates responsibilities — a reference document that also tracks implementation status, a mapping catalog that also records changelogs — each accumulated responsibility is a violation of this principle. The document becomes harder to maintain, harder to trust, and harder to use.

### Repair Strategies

Mixed-purpose documents are usually the result of incremental accumulation, not intentional design. A reference document was the right place for some information; then adjacent information was added for convenience; then the document grew in a direction its original purpose didn't anticipate.

Two repair strategies address this:

**Content routing**: identify which purpose each piece of content serves, then move content to the document type that matches its purpose. A changelog entry in a reference document belongs in version control history or an experience document. Status tracking in a mapping catalog belongs in a project backlog.

**Column splitting**: when a single column mixes content types — a "Notes" column containing both legacy constraints and new design decisions — split it into typed columns. This preserves the convenience of co-location while restoring the interpretive clarity that typed content provides.

The repair strategy depends on whether the mixed content needs to stay together (column splitting) or can be separated (content routing).

---

## Reflection

The invisible cost of mixed-purpose documents is that no individual decision looks wrong. Adding a changelog entry to a reference document is a reasonable impulse — it's contextually related and convenient. The problem emerges only at the document level, when the accumulation of individually reasonable additions has produced a document that serves no single purpose well.

This is why mixed-purpose documents are persistent: they're created incrementally and the damage is diffuse. No single addition breaks anything; the document just becomes slightly less useful with each addition.

The repair also tends to be delayed: recognizing that a document has mixed purposes requires stepping back from the content to observe the document's role in the information architecture. This is a different kind of attention than the attention needed to write or update content.

One practical heuristic: if you're unsure where a piece of content belongs, ask what kind of document a reader would expect to find it in. That expectation identifies the correct home.

---

## Conclusion

**Every document creates a mental model before it's read** — The document's type, location, and stated purpose set reader expectations. Content that violates those expectations creates interpretive friction.

**Mixed-purpose documents degrade all their purposes** — A document serving two purposes serves neither as well as a dedicated document would. The dilution is multiplicative, not additive.

**Different update cadences create staleness asymmetry** — Frequently-updated content makes infrequently-updated content in the same document appear suspect, regardless of accuracy.

**Separation of concerns applies to information architecture** — Each document should have a single, well-defined responsibility. This makes it predictable, maintainable, and trustworthy.

**Mixed-purpose documents accumulate incrementally** — No single addition looks wrong. Repair requires architectural observation, not content review.
