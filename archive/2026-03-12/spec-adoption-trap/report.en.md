# Spec Adoption Without the Waterfall Trap

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-12T16:00
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Introduction

The most common fear when introducing specifications to an existing project is: "Does this mean we have to write all the specs before we can touch the code?" The fear has historical roots — the waterfall model works exactly that way. But spec-driven development and waterfall are different things. Waterfall demands phase completeness: specs must be finished before design, design before coding. Spec-driven development demands only one thing: when a spec exists, code must align with it.

This distinction seems small, but it determines whether adoption is viable. This essay starts from the concept of "spec debt," dismantles five common delta development myths, describes the correct operating mode during a spec-sparse period, and explores the transition point from "creating specs" to "managing spec changes."

## Analysis

### Spec Debt: Unwritten Behavior

Technical debt is a widely accepted concept — known quality issues in code that remain unaddressed. Spec debt is the same thinking extended: behaviors that have been implemented but never explicitly described in specifications.

Spec debt does not mean the system is wrong. It means the system's correctness cannot be verified — because no definition of "correct" exists. When a feature lives only in code with no corresponding spec description, anyone modifying it (including AI) can only judge whether they've broken expected behavior by "reading the existing code." This shares the same risk structure as technical debt: the debt doesn't cause immediate problems, but as modifications accumulate, the probability of breaking undocumented behavior steadily grows.

The key difference lies in how the debt is repaid. Technical debt requires dedicated refactoring time. Spec debt can be repaid incrementally during feature development — each time you touch a feature, record observed behavior as the spec's Architecture layer, and record confirmed intent as the Requirements layer. This is the foundation of incremental formalization, discussed later.

### Five Delta Development Myths

The delta flow is the core mechanism for spec change management: propose a change describing additions, modifications, and removals to spec items, review it, then merge into the baseline spec. The flow itself is simple, but five common myths have formed around it.

**Myth One: Deltas require a complete baseline.** A delta is defined as "change relative to a baseline," so a complete baseline spec must exist first? Wrong. A delta only needs change relative to "specs that already exist." If a domain has only three requirements explicitly recorded, modifications to those three constitute a valid delta. Baseline completeness and delta validity are independent dimensions.

**Myth Two: Sparse periods preclude deltas.** Since specs are still sparse, isn't using a delta flow over-engineering? It depends on what you're modifying. If the behavior you're changing happens to have a spec description, the delta flow is the right tool — regardless of how low overall spec coverage is. If the behavior has no spec, then indeed no delta is needed, because there's no baseline to reference. The criterion is "does the specific behavior being modified have a spec," not "is overall coverage sufficient."

**Myth Three: Specs must be snapshotted before code changes.** This is residual waterfall thinking. Spec snapshots make sense during periods of frequent change — for example, freezing specs before a version release. During sparse periods, specs themselves are fragmentary; snapshotting fragments adds no value. A spec's snapshot strategy should be a byproduct of feature development, not a prerequisite.

**Myth Four: All changes need formal proposals.** The formality of the delta flow should match the scope of impact. Correcting a factual error in the Architecture layer — just fix it directly, no proposal needed. Adding or modifying a behavioral contract in the Requirements layer — the proposal flow provides change traceability. Proportionality is key: the weight of governance should match the weight of risk.

**Myth Five: The delta flow is heavyweight.** This usually stems from over-imagining the process. A minimal delta proposal needs only three elements: the spec file path being changed, ADDED/MODIFIED/REMOVED markers, and the specific content of the change. This is shorter than most pull request descriptions. The flow's purpose isn't to add ceremony — it's to ensure "why it changed" and "what changed" are recorded together.

### Sparse Period Operating Mode

With the above myths understood, the operating mode for spec-sparse periods becomes clear. Three core principles apply:

First, **reconcile on demand, not on schedule.** Spec-code reconciliation only makes sense when specs exist. When you need to modify a feature, first check whether specs cover it. If specs exist, reconcile to confirm code-spec alignment; if not, skip reconciliation and use code as the source of truth. Don't write specs just to execute the reconciliation process.

Second, **byproduct-driven spec growth.** Every feature development is an opportunity to observe system behavior. Developers naturally clarify "what the system actually does" and "what the system should do" while understanding existing code. Recording these understandings — the former becomes Architecture, the latter becomes Requirements — allows specs to grow incrementally as a byproduct of feature work. This is fundamentally different from "write specs first, then code."

Third, **fall through to code.** When specs don't exist, the knowledge discovery sequence naturally falls through to the next layer: check specs first, then co-located documentation (READMEs), then search code. The sparse period simply means falling through to code happens more frequently. This isn't failure — it's the expected graceful degradation path by design.

### From Creation to Management: The Transition Point

When does a domain shift from the "creating specs" phase to the "managing spec changes" phase? The answer: when the domain's spec density is sufficient to constrain implementation decisions.

This threshold is per-domain, not global. Frontend UI specs might already be dense enough to manage changes through the delta flow, while backend service specs might still be sparse enough that every touch is still in "creation" mode. Forcing a unified spec management phase across the entire project means prioritizing organizational uniformity over actual productivity — which is the core error of the waterfall model.

Signals for the transition include: a domain's specs being referenced by a second person (indicating specs have readers, not just authors), reconciliation discovering "code doesn't match spec" rather than "spec doesn't exist" (indicating spec coverage has begun constraining implementation), and modifying one requirement requiring consideration of its impact on other requirements (indicating requirements have formed structure among themselves).

### Incremental Formalization: From Tacit to Explicit

Incremental formalization is the natural repayment mechanism for spec debt. The core concept is simple: during each feature development, some portion of the developer's understanding of system behavior can be converted into explicit spec descriptions. This conversion doesn't require a separate "documentation writing" time block — it's a byproduct of the understanding process.

This differs fundamentally from traditional documentation sprints. A documentation sprint is an independent activity whose goal is "write down what's already known." Incremental formalization is a side effect of feature development, whose goal is "record what was just understood at the moment of deepest understanding." The timing difference produces a quality difference — specs recorded during development are more accurate than documentation written from later recollection.

Incremental formalization also has boundaries. Not all understanding is worth formalizing. The criterion returns to the spec's dual-layer structure: if the understanding concerns what the system "actually does" (fact), place it in the Architecture layer at minimal cost; if it concerns what the system "should do" (intent), it needs product confirmation before entering the Requirements layer. Architecture can be formalized unilaterally; Requirements need cross-role confirmation — this distinction prevents developers from mistakenly elevating their personal understanding of the system into behavioral contracts.

## Reflection

Behind these observations lies a common tension: the gap between the ideal state of spec management (complete coverage, comprehensive reconciliation, formal delta flows) and the current state (sparse coverage, selective reconciliation, mixed modes). The waterfall trap demands bridging this gap before work can begin. The incremental approach lets the work itself become the means of bridging.

This also explains why the advice to "finish writing all specs first" almost inevitably fails in brownfield projects. A brownfield project's behavior is already defined by code — retroactively writing specs for existing behavior either becomes a natural-language transcription of code (valueless) or a reinterpretation of code (high risk). The correct posture is to acknowledge spec debt's existence, then incrementally repay it with every opportunity to touch the system.

Another noteworthy point is the effect of proportionality on team adoption. If the delta flow feels like extra burden, teams will circumvent it. If it's lighter than a pull request description, teams will naturally use it. The success criterion for process design isn't "rigorous enough" but "low enough friction that not using it becomes more inconvenient."

## Conclusion

The viable path for spec adoption rests on four pillars. First, recognize spec debt — like technical debt, it's a manageable accumulated risk, not a defect that must be immediately zeroed out. Second, the delta flow's threshold is "the behavior being modified has a spec," not "overall coverage is sufficient." Third, spec growth comes from feature development byproducts, not dedicated documentation sprints. Fourth, the creation-to-management transition is per-domain, using that domain's spec density as the criterion.

The common characteristic of these four pillars: they all embed spec management into existing workflows rather than layering new phases on top. This is the fundamental difference from waterfall — not sequential phases, but integrated practice.
