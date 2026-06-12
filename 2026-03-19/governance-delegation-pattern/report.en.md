# Don't Copy the Authority — The Default Delegation Pattern for Governance Documents

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-19T23:50
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Introduction

A day's work began with the mechanical task of converting `var` declarations to `const`/`let` — 289 declarations across 15 files, each requiring analysis of whether the variable was ever reassigned. After that came colon spacing fixes, then comma spacing, then operator spacing. Each category fixed revealed the next — a whack-a-mole pattern where formatting inconsistencies kept surfacing.

This whack-a-mole phenomenon triggered a fundamental question: how much of the formatting landscape did the project's rule documents actually cover? A systematic audit against `@stylistic/eslint-plugin`'s 33 rules produced an uncomfortable answer: 11 covered, 22 completely missing. Worse, the instinctive response — enumerating every missing rule — would have inflated the already bloated 206-line formatting.md further, while future ESLint additions would still cause drift.

This report isn't about the formatting fixes themselves. It's about the governance design question that emerged from the process: when an authoritative source already exists, what role should governance documents play?

## Analysis

### The Allure and Cost of Per-Rule Enumeration

The initial formatting.md used a per-rule enumeration approach — each ESLint rule was re-described in markdown with correct and incorrect code examples. The appeal is obvious: the document itself becomes a complete reference manual, requiring no external lookup.

In practice, this completeness was illusory. With 33 formatting rules and only 11 covered, the gap rate was 67%. Filling those gaps would have pushed the document to roughly 400 lines, every one of them duplicating work that ESLint's own documentation had already done. This isn't knowledge creation — it's knowledge copying.

The cost of copying goes beyond redundancy. In the AI agent context, every line of a rule document consumes context window token budget. A 206-line formatting.md costs approximately 3,000 tokens — borrowed directly from the reasoning space available for actual task work. Governance document token consumption has opportunity cost: context spent re-stating ESLint documentation is context unavailable for understanding business logic.

> **Decision Point**: Full coverage of all 33 rules vs. patch only the important ones
> — Alternatives: (1) Enumerate all 22 missing rules for complete coverage (2) Add only rules with active violations (3) Switch governance models entirely
> — Outcome: After considering context cost, options 1 and 2 were both rejected — they represent the same thinking at different scales, differing only in how much to copy

### The Inversion

The turning point came from a simple observation: per-rule enumeration answers "what do we follow," but the more efficient question is "what do we deviate from."

When an authoritative source exists (such as ESLint defaults), governance documents don't need to restate the authority's full content. They only need to do two things: (1) declare which authority is followed, and (2) list specific deviations. This is the "default delegation + deviation listing" pattern.

This inversion isn't new to software engineering. Ruby on Rails' "Convention over Configuration" is the same principle applied at framework level — you don't configure every detail, only the parts that deviate from convention. From a design principle perspective, it also echoes the Open-Closed Principle: open for extension (new ESLint rules are automatically inherited), closed for modification (the document itself doesn't need updating).

In the formatting.md case, the inversion's effect was dramatic:

| Dimension | Per-Rule Enumeration | Default Delegation + Deviations |
|-----------|---------------------|-------------------------------|
| Coverage | 11/33 (33%) | 33/33 (100%) |
| Document lines | 206 | 46 |
| Context consumption | ~3,000 tokens | ~700 tokens |
| New ESLint rules | Manual sync required | Automatic inheritance |
| Maintenance burden | Every ESLint update | Near zero |

Fewer words achieving more complete coverage. This isn't trimming — it's an architectural improvement.

### The Accessor Debate as a Micro-Case

The member-ordering rule's accessor placement went through three revisions, perfectly demonstrating why manually mirroring an authoritative source is fragile.

The first version treated getters/setters as field companions, grouped with their backing `#field`. This felt intuitive — seeing the field immediately shows its accessor, making individual property comprehension fast. But when it was pointed out that accessors can be overridden by child classes, the field-companion positioning became untenable. The second version moved accessors to a distinct tier, placed between constructor and methods. Then a check of ESLint's actual default revealed that ESLint indeed classifies accessors as a separate category rather than a subset of methods — the third version aligned accordingly.

Three revisions, each involving manual reasoning followed by ESLint correction. If the document had simply declared "follow ESLint defaults" from the start, none of these iterations would have been necessary. The problem with manual mirroring isn't just initial cost — it's that every design decision requires independent derivation, and derivation tends to drift from the authority, even when the drift is unintentional.

> **Decision Point**: Introduce ESLint tooling vs. pure markdown rules
> — Alternatives: (1) Install ESLint as a project tool (2) Declare default delegation in markdown without introducing tooling
> — Outcome: Chose (2). Tool introduction has its own considerations (build pipeline, npm dependencies, team adoption) and shouldn't be coupled with governance document design. The delegation pattern works with or without tooling

### Baseline Establishment as Migration Strategy

Declaring "follow ESLint defaults" is easy, but if the codebase itself isn't compliant, the declaration is just aspiration. A migration strategy is needed to move the codebase from its current state to compliance.

"Baseline establishment + inheritance" is the implementation pattern: a one-time scan of all 33 rules, fix all violations, confirm compliance. After that, the governance document's delegation declaration has substantive backing — it's no longer aspiration but a verified description of current state.

> **Decision Point**: One-time script vs. reusable skill
> — Alternatives: (1) Build a reusable skill for repeated execution (2) One-time script, discard after use
> — Outcome: Chose (2). Baseline establishment is inherently one-time. If compliance verification is needed later, introducing ESLint tooling is more appropriate than maintaining a handcrafted grep-based skill

### Authority Scope Boundaries

The default delegation pattern has one boundary that must be made explicit: the authority's scope covers formatting rules only.

When initially drafting the Authority section, the boundary wasn't sufficiently constrained. An agent might generalize "ESLint first" to behavioral rules — converting `==` to `===` (`eqeqeq`), removing unused parameters (`no-unused-vars`). These are semantic changes, not formatting. `==` in legacy code represents intentional type coercion; unused parameters may be retained for API contracts.

The final document explicitly delineates two zones: the domain governed by ESLint formatting defaults (spacing, punctuation, layout), and behavioral rules that require explicit project decisions (with a specific exclusion list and risk descriptions). This boundary isn't redundant protection — it's a necessary condition for the delegation pattern to operate safely.

## Reflection

### The Role of Governance Documents

This experience reveals two fundamentally different role models for governance documents:

**Textbook model**: The document itself is the complete knowledge source. Readers need no external reference. The advantage is self-containment; the disadvantage is the need to stay synchronized with the authoritative source — and synchronization inevitably lags.

**Policy declaration model**: The document declares which authority to follow and records only deviations. Knowledge lives at the authoritative source; the governance document is merely a pointer plus a set of diffs. The advantage is that it never goes stale (authority updates are automatically inherited); the disadvantage is that readers must consult external sources.

For AI agents, the policy declaration model has an additional structural advantage: the agent already "knows" ESLint defaults as part of its training data. A single statement "follow ESLint defaults" activates knowledge the agent already possesses, rather than spending 3,000 tokens re-transmitting what it already knows. This makes the policy declaration model's efficiency advantage even more pronounced in the agent context.

### Cross-Ecosystem Validation

The default delegation pattern isn't unique to the JavaScript ecosystem. Across different languages and toolchains, the same pattern appears repeatedly — and is repeatedly overlooked.

The Python community has PEP 8, a widely accepted style guide. Yet many teams still write 50-page internal wikis restating every PEP 8 rule, simply because "we need our own style guide." A more effective approach is a one-line declaration: "follow PEP 8, except: max line length = 120." Deviations are clear; everything else is inherited.

Go demonstrates the extreme form of default delegation. `gofmt` has no configuration options — formatting is enforced by the tool, with no possibility of deviation. Not even a delegation declaration is needed, because there's no space to deviate from. This shows that when the authoritative source is simultaneously the enforcement tool, the governance document's necessity can be entirely eliminated.

Java's Checkstyle sits between the two. Teams can choose to rewrite the entire Google Java Style Guide as an internal document, or reference `google_checks.xml` in their `.checkstyle.xml` and override only 3 rules. The latter is not only shorter — when Google updates the style guide, the team automatically receives the update. The former permanently lags.

Kubernetes' Helm charts provide an analogy from configuration management. A `values.yaml` can exhaustively list every default value with annotations, or it can specify only the overrides and inherit the rest from chart defaults. The latter is exactly how Helm designers intended the tool to be used.

These examples, arranged together, outline a delegation spectrum from weak to strong:

| Stage | Form | Document Role | Example |
|-------|------|--------------|---------|
| 0 | No authoritative source | Document is the authority | Company-internal custom naming conventions |
| 1 | Authority exists, copied per-rule | Textbook | Team wiki restating PEP 8 |
| 2 | Authority exists, declared + deviations | Policy declaration | formatting.md, Checkstyle config |
| 3 | Authority + enforcement tool | Tool configuration | ESLint `.eslintrc`, Helm values |
| 4 | Authority is the tool, no deviation space | No document needed | `gofmt`, `rustfmt` |

Most projects remain at stage 1, while stage 2 is already a significant improvement. Whether to advance to stage 3 or 4 depends on toolchain maturity and team readiness.

### Applicability Boundaries

The default delegation pattern has clear prerequisites: a recognized, stable authoritative source that the agent's training data covers. ESLint formatting rules perfectly satisfy all three conditions. But if the authoritative source is unstable (such as experimental rules from a niche linter), or unrecognized by the agent (such as company-internal custom standards), per-rule enumeration may still be necessary — this is the stage 0 scenario in the delegation spectrum.

Similarly, behavioral rules cannot be delegated to ESLint not because ESLint's recommendations are poor, but because behavioral changes require deep understanding of codebase semantics — a project-level decision that exceeds the formatting authority's scope.

## Conclusion

When a recognized authoritative source exists, the optimal strategy for governance documents is not to copy the authority's content, but to declare delegation to the authority and record only deviations. This principle compresses to a single sentence:

**Don't copy the authority — delegate to it.**

In practice, this means: establish a baseline once (verify codebase compliance), then maintain a thin document declaring the default plus listing deviations. The document transforms from "textbook" to "policy declaration" — thinner, more complete, less prone to staleness, and minimally consuming of AI agent context.
