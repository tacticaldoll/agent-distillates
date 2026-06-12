# Introducing Governance to a Legacy Project

<!-- front matter -->
**Structure**: Experience Report
**Date**: 2026-03-05T09:09
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.66
**Source**: reconstruction

## Background

A long-running legacy project — an older frontend framework paired with a C++ backend service — relied on a single monolithic instruction file (project instructions) to carry all project conventions, engineering standards, and accumulated knowledge. As content grew, structural problems emerged: coding constraints and tool capabilities were intermixed, prescriptive instructions and descriptive knowledge shared the same level without conflict resolution, and each new rule had to find its place in the file with no criteria for what "appropriate placement" meant.

The knowledge base had the same boundary problem. Reverse-engineered architecture documents coexisted with would-be specifications in the same directory, with no mechanism to distinguish "known facts" from "rules to follow." When someone asked "which parts of this knowledge base are actually specifications?", the answer exposed a deeper gap: the system could guarantee correct processes but not correct outcomes — because there was no specification layer at all.

## Discovery

### Phase 1: Identifying the Trigger

A comprehensive audit of the existing knowledge base surfaced three structural defects. First, the index file referenced three non-existent documents — phantom references indicating the index had never been systematically maintained. Second, a document describing allowed conditions for resource operations was substantively prescriptive, yet classified as descriptive knowledge. Third, four of eight tool capabilities — responsible for code flattening, architecture rigor, business rule encapsulation, and data domain segregation — produced no artifacts and were always active in virtually every coding scenario. They behaved like constraint rules, not event-driven tools.

These findings pointed to a core gap: the system was entirely skill-driven. Skills defined "how to do"; knowledge recorded "what is"; but nothing played the role of "what to build." In other words, the system ensured process quality but could not ensure outcome correctness.

### Phase 2: Document Hierarchy Design

With the gap confirmed, the question became: "Do we need a guideline for guidelines — a meta-guideline?" Existing rule files varied inconsistently in granularity, structure, and enforcement level — some managed a single concern while others spanned SOLID principles, design patterns, and legacy transition at the same level. There were no unified MUST/SHOULD/MAY definitions and no conflict resolution rules.

> **Decision Point**: Where to place the governance meta-rule
> — Alternatives: Embed in project instructions (guaranteed every-session loading), place in custom directory (semantically correct but not auto-loaded), use path-scoped rule (precise triggering, standard mechanism)
> — Outcome: Path-scoped rule selected — auto-injected when editing rule files, combined with project instructions declaring the hierarchy to establish "constitutional awareness." No technical priority enforcement mechanism existed, but the three-layer approach (declaration + auto-injection + RFC 2119 semantics) maximized compliance

A four-level document hierarchy was established. Level 0 is the meta-rule itself, holding absolute authority while remaining minimal. Level 1 contains mandatory guidelines stored in the rules directory. Level 2 covers external specification input from outside teams, not project-managed. Level 3 holds descriptive knowledge in the knowledge base. Conflicts resolve by higher level winning unconditionally.

During hierarchy design, a content backflow review discovered the meta-rule's initial draft contained material that — by its own classification tests — belonged at Level 1: index maintenance rules, content format rules, lifecycle rules. These were extracted into separate guideline files, leaving the meta-rule with only three responsibilities: hierarchy definition, classification tests, and structural skeletons.

### Phase 3: Mechanism Classification

The confusion between skills and rules was one of Phase 1's core findings. The solution was a "Mechanism Selection" test using first-match logic: components needing isolated context or persistent memory are Agents; those extending capability through actionable tasks or reference knowledge are Skills; those enforcing mandatory constraints or governance checkpoints are Rules.

> **Decision Point**: Reclassifying four pseudo-skills as rules
> — Alternatives: Keep skill identity but add an "always-on" marker (minimal change preserving status quo) vs. reclassify as rules (semantically correct, aligned with platform native definitions)
> — Outcome: Reclassified. The distinguishing criterion was clear — skills produce artifacts and are event-driven; rules are always-on with no output. The trigger descriptions for all four confirmed this: each read "any coding, review scenario," which is precisely the characteristic of a rule, not a skill

The classification system also clarified the relationship between guideline-driven and spec-driven approaches. They are complementary, not competing: guidelines answer "how to do it correctly" (process quality); specifications answer "what counts as done" (outcome correctness). The maturity path is to establish guidelines first, then use guidelines to constrain specification quality, then reach steady-state with both.

### Phase 4: Structural Skeletons and Coupled-Section Synchronization

With the hierarchy established, each document type needed a defined structure (structural skeleton). The guideline skeleton includes fixed sections — Purpose, Rules, Checklist — with one file per concern. The skill skeleton follows platform native format. The knowledge skeleton includes typed front-matter tags.

More important was the concept of coupled-section synchronization. Certain skeleton sections are paired: a guideline's Rules section and Checklist section must have one-to-one correspondence, with every rule having a checklist item and every checklist item tracing to a rule. Modifying one section must trigger synchronization review of the other.

This concept was validated through the meta-rule's own cross-contradiction audit. Hierarchy Rule 4 ("No document may claim authority over its own level or above") conflicted with Extension Rule 5 ("Project instructions may contain transition plans that modify Level 0"). The fix was adding an exception clause to Rule 4 — precisely the kind of inconsistency that coupled-section synchronization was designed to catch.

### Phase 5: Knowledge Base Positioning and Knowledge Routing

The knowledge base's physical location triggered a discussion about its identity. A proposal to make it a hidden directory (dot-prefix) was analyzed and rejected — the knowledge base's nature is closer to project documentation (like `docs/`) than tool configuration (like `.git/`), and should remain visible. But the more fundamental question was the knowledge base's positioning itself.

A **code-as-documentation** principle redefined the knowledge base from "permanent knowledge repository" to **debt indicator**. Each knowledge base entry represents a gap in the code's self-documentation capability — reverse-engineered knowledge exists because the code itself cannot express the same information. Permissible scenarios were narrowed to three: cross-cutting analysis (no single source directory owns the knowledge), legacy code compensation, and experience reports.

This repositioning required a knowledge routing table specifying the correct destination for each type of knowledge. Code behavior belongs in source code itself, component design in co-located READMEs, skill operational lessons in skill definition files, and cross-cutting analysis in the knowledge base. The default is the highest-priority destination — the knowledge base is a last resort, not a default.

The lookup order (discovery rule) became phase-dependent: the current phase checks the knowledge base first then code; after knowledge base decomposition, co-located READMEs first then residual knowledge base entries; upon reaching spec-driven development, specifications first then co-located documents.

### Phase 6: Transition Plan

With the governance framework established, a migration path from reverse-engineered knowledge to spec-driven development was needed. The knowledge base itself served as the migration checklist — each entry was a debt item pointing to a source directory lacking self-documentation capability.

The transition plan was organized into five phases. Phase 0 (reverse-engineer knowledge base) and Phase 1 (establish coding standards) were previously completed. Phase 2 (governance restructuring) was the present work — skill-to-rule migration, meta-rule establishment, code-as-documentation rule creation. Phase 3 (decompose knowledge base) measures progress by knowledge base entry reduction rather than documentation growth — entries are absorbed into code, co-located, marked as cross-cutting, or marked obsolete. Phase 4 is the last thing the engineering side can do: converting unresolvable cross-cutting entries into specification input examples for the external team. Phase 5 requires external specification input and cannot be initiated unilaterally.

The knowledge precipitation tool's core function — writing to the knowledge base — conflicted with the knowledge base's new positioning as debt. This conflict was deliberately deferred rather than pre-emptively fixed, following the principle of avoiding speculative design. The conflict was registered as a Phase 4 prerequisite: during Phase 3 the tool can still write to the knowledge base (new entries become Phase 4 specification material), but output routing must change before Phase 4 begins.

Final cross-session continuity verification confirmed the understanding path was complete: project instructions serve as the sole entry point, declaring the governance hierarchy pointing to the meta-rule, engineering standards pointing to rule files, the transition plan pointing to the transition document, and the knowledge base lookup order referencing the transition document's phase evolution table. No prior knowledge is required for a new session — all guidance is reachable.

## Decisions Summary

| # | Decision | Rationale |
|---|----------|-----------|
| 1 | Level 0 remains minimal | Rapid development phase — bloated Level 0 creates modification bottleneck (requires extension rule authorization) |
| 2 | Four pseudo-skills reclassified as rules | Skills = produce artifacts + event-driven; Rules = always-on + no output |
| 3 | Knowledge base = debt indicator, not permanent knowledge | Strangler pattern applied to documentation — each entry represents a gap in code self-documentation |
| 4 | Level 2 (Spec) is external input | Reverse-engineered content describes "what is," not "what should be" — only external authority can provide authoritative specs |
| 5 | Transition phases track knowledge base decomposition, not README creation | Metric is knowledge base reduction, not documentation growth |
| 6 | Extension Rule 3: short-lifecycle paths must not host long-lifecycle guidelines | Formalizing the lifecycle mismatch lesson |
| 7 | Discovery rule is phase-dependent | Static lookup order conflicts with evolving knowledge locations across phases |
| 8 | Meta-rule placed as path-scoped rule | Precise triggering — auto-loaded only when editing rule files |
| 9 | Knowledge base remains visible (not hidden directory) | Nature is closer to project documentation than tool configuration |
| 10 | Precipitation tool conflict deferred | Avoid speculative design — registered as Phase 4 prerequisite |

## Key Lessons

1. **The limits of a monolithic instruction file are structural, not volumetric.** The problem is not that the file is too long, but that different natures of content (constraints, tools, knowledge) require different enforcement mechanisms and lifecycles. Splitting must follow nature boundaries, not topic boundaries.

2. **Classification tests outperform classification labels.** Rather than labeling document types and then deciding treatment based on labels, building a first-match test flow means the test itself is the definition — eliminating the risk of labels drifting out of sync with definitions.

3. **The debt perspective inverts knowledge base value judgments.** When a knowledge base shifts from "asset" to "debt indicator," growth is no longer good. Each new entry represents a gap where code cannot express itself, and reducing entries is progress.

4. **Transition plans need explicit external dependency boundaries.** There is a clear ceiling on what engineering can do unilaterally — converting reverse-engineered knowledge into specification input examples is the last step. Crossing that boundary requires external authority involvement, and acknowledging this is more pragmatic than pretending self-sufficiency.

5. **Phase-dependent rules are more precise but more fragile than static rules.** A phase-evolving lookup order solves the conflict between static rules and evolving reality, but introduces a new maintenance responsibility — every phase transition must update the lookup order. The transition document becomes the single source of truth for this synchronization.

6. **Conflict registration beats speculative fixes.** When a design conflict is discovered (such as a precipitation tool contradicting the knowledge base's new positioning), recording the conflict and marking when to resolve it is safer than pre-emptive modification — pre-emptive fixes are speculative design for scenarios that haven't occurred, and the best resolution often needs validation from the actual situation.
