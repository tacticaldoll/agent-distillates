# Brownfield Dual-Layer Structure: Building Honest Boundaries Between Observation and Intent

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-09T23:30
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Introduction

When a brownfield project introduces a specification framework, a structural contradiction surfaces immediately. Specifications carry the semantics of "how the system should work" — prescriptive. But nearly all knowledge a brownfield project possesses comes from reverse engineering, recording "how the system happens to work" — descriptive. The two may be textually similar, but their authority nature is entirely different: one is a contract, the other an observation report.

Directly placing reverse-engineering observations into the specification directory grants them the authority status of specifications. Formally they are "specs" — located in the spec directory, using spec format, processed by spec tools. Substantively they remain observations — no one has reviewed whether these behaviors are design intent, no one has confirmed they should be preserved rather than corrected.

This is false authority: form confers authority, but substance does not support it.

False authority is not unique to brownfield specification efforts. It appears repeatedly in different carriers: ineffective governance mechanisms continuing to exist create the illusion that "governance is in place"; passing test cases being equated with "correct behavior" when the tests themselves may reflect wrong expectations; expired process documents continuing to be followed because "the document says so." The common structure: a mechanism's formal shell is intact, but its content no longer supports the authority it claims.

False authority is more dangerous than lacking authority. When specifications are absent, everyone knows the gap exists — this awareness itself motivates action. When false specifications exist, everyone believes specifications are in place — until someone discovers the team has been protecting a "specified bug." Honestly labeling the nature of knowledge — even when acknowledging that most behaviors are not yet specified — is a more pragmatic choice than pursuing formal completeness.

The dual-layer structure is the design response to false authority.

## Analysis

### Core Principle of the Dual-Layer Design

The dual-layer structure's core principle: **within the same spec file, use structural position to distinguish two fundamentally different kinds of knowledge**.

| Layer | Semantics | Source | Language Style | Authority Level |
|-------|-----------|--------|---------------|-----------------|
| Architecture | How the system works | Reverse-engineering observation | Descriptive — is / does / has | Factual record — can be overridden by code |
| Requirements | How the system should work | Reviewed design intent | Prescriptive — MUST / SHOULD / MAY | Behavioral contract — code must comply |

Co-locating both layers in the same file is a deliberate design choice, not laziness. They describe two facets of the same domain — separating them into independent files would break cognitive continuity. When understanding a module, readers need to simultaneously see "what it currently does" and "what it should do"; the contrast between the two is itself valuable information. When Architecture aligns with Requirements, readers gain confidence; when gaps exist, readers know where to focus attention.

The two layers naturally differ in granularity. Architecture operates at the module level — data flows, component interactions, architectural patterns — providing a bird's-eye view. Requirements operate at the scenario level — Given/When/Then behavioral contracts — providing a street-level view. This granularity difference is a feature, not a defect: readers approaching a module naturally follow a cognitive path from bird's-eye (understanding the landscape) to street-level (understanding specific contracts). If both layers shared the same granularity, one would be redundant.

### Graduation Mechanism: From Observation to Contract

The dynamic aspect of the dual-layer structure is graduation — under what conditions an Architecture entry is promoted to Requirements.

Graduation should not be triggered by periodic review. "Monthly review of Architecture sections to decide which can be promoted" becomes a ritual lacking context — judgments made without specific modification context are unreliable. The more natural trigger is **implementation contact**: when you must make a judgment about a behavior's nature because you are modifying it.

Two scenarios form natural graduation trigger points. The first is a delta change proposal. When a proposer writes "change behavior X from A to B," this action implicitly confirms A as the previously intended behavior — otherwise the modification's nature is "bug fix," not "behavior change." The act of proposing forces the author to clarify the nature of what is being modified — this clarification is the natural point where graduation judgment occurs. The second is pre-implementation specification alignment. Before modifying a module's code, comparing against specifications — if Architecture describes a behavior but Requirements has no corresponding scenario — the developer must judge: should this behavior be preserved? If yes, write a Requirement scenario to protect it. If uncertain, keep it in Architecture.

The graduation judgment standard is: **can a meaningful Given/When/Then scenario be written for this behavior, and does the team agree the scenario describes "what should happen" rather than "what coincidentally happens"?** "Meaningful" means the scenario is not a line-by-line translation of code but an expression of behavioral intent. "Team agrees" means graduation is not an individual judgment but a consensus. If either condition cannot be met — the behavior is too vague to be scenario-ized, or the team has no consensus on "whether it should" — the entry stays in Architecture, awaiting more information.

This means graduation is incremental, local, and driven by implementation needs. There is no need to promote all observations to specifications at once — only areas being touched need judgment. This matches brownfield reality: you cannot understand the entire legacy system's intent at once, but you can make definitive judgments about modified areas with each change.

### Degradation Defense: Preventing Infinite Accumulation of Observation Debt

The dual-layer structure's structural risk is degradation: Architecture continually expands (recording observations is low-cost) while Requirements stays thin (confirming intent is high-cost). Teams keep "recording what they see" but never make the "is this design intent" decision.

In the early stages of brownfield migration, Architecture being far larger than Requirements is a normal transitional state. Reverse engineering produces abundant observations while intent confirmation takes time and domain knowledge. The ratio might be 9:1 or even more extreme — this is not degradation but an honest reflection of knowledge maturity. Degradation occurs when the **ratio does not converge over time**: if the team has modified substantial code over a period but skipped the graduation judgment each time, the Architecture-to-Requirements ratio stagnates, meaning the graduation mechanism is not functioning.

The defense is not ratio monitoring. Setting metrics like "Architecture entries must not exceed 70%" incentivizes hasty promotion — packaging observations as specifications to improve numbers, which is precisely the false authority the dual-layer structure aims to prevent.

A more effective defense is **embedding graduation judgment into existing workflow nodes**. Specification alignment triggers automatically before implementation — if the touched area has only Architecture and no Requirements, it flags this gap for the developer to decide. Delta change proposals require explicitly identifying the nature of modified behaviors — is it changing intent or correcting accident. During code review, if the modification involves behavior described only in Architecture, reviewers can follow up. Three nodes form distributed pressure: each node's cost is low — one judgment, one annotation — but the cumulative effect is Requirements gradually filling in as implementation contact accumulates.

### Entries That Never Graduate and the Granularity Heuristic

Not all Architecture entries are graduation candidates. Some entries record pure implementation details — "Module A communicates with Module B via a specific protocol" — this is not a behavioral contract but an architectural fact. Such entries belong either permanently in Architecture as architectural knowledge or are migrated to a more suitable location (e.g., a co-located README).

Distinguishing "behavioral observations pending graduation" from "pure architectural knowledge" can use a granularity heuristic: **if an Architecture statement can be rewritten as a Given/When/Then scenario without losing meaning, it is an ungraduated behavioral observation**. If rewriting would distort its nature — because it describes structure rather than behavior — it is architectural knowledge, and never graduating is the correct outcome.

This heuristic also serves the degradation defense: when Architecture contains many entries that pass the granularity test but have never graduated, this is a more precise degradation signal — with more diagnostic value than simple entry count ratios.

### Interaction Between Dual-Layer and Delta Model

When a delta change proposal modifies an object that exists only in Architecture, a semantic gray zone emerges. The delta model's three semantic sections — ADDED, MODIFIED, REMOVED — assume the operated object is a confirmed behavioral contract. But Architecture entries are not contracts; they are observations.

Modifying an Architecture entry is not "changing confirmed behavior" (the behavior has not been confirmed) nor entirely "first specification" (something is indeed being changed). What this situation requires is the semantics of: **building a contract on top of an observation** — doing two things simultaneously: confirming the original observation's intentionality and modifying that intent.

The practical handling: delta proposals mark such modifications as ADDED (adding a new Requirements entry), noting in the proposal which Architecture entry it supersedes. This is more honest than MODIFIED — because what is being modified is not an existing contract but an observation being contractualized for the first time. MODIFIED implies "changing confirmed behavior"; ADDED makes explicit "establishing a new behavioral contract."

## Reflection

### The Dual-Layer Structure's Own Scaffolding Nature

A question worth pursuing: is the dual-layer structure itself infrastructure or scaffolding?

If the brownfield project continues maturing — more behaviors confirmed as intent, more Architecture entries graduating to Requirements — will the Architecture layer gradually empty? If so, the dual-layer structure ultimately converges to single-layer (all Requirements), and the Architecture layer's existence was transitional.

But complete convergence is unlikely, for two reasons. First, new reverse-engineering observations will continue being produced — even as old observations graduate, exploration of other system areas generates new Architecture entries. Second, the "never-graduating architectural knowledge" discussed earlier is permanent — structural descriptions do not disappear because behaviors get specified.

The more accurate expectation: the Architecture layer's composition will change over time — shifting from predominantly "behavioral observations pending graduation" to predominantly "pure architectural knowledge." The dual-layer structure will not disappear, but the Architecture layer's nature will evolve from "unconfirmed behaviors" to "confirmed structures." This means the dual-layer structure is infrastructure, but the specific function it serves evolves as the brownfield migration progresses.

### The Honest Structural Limit

The dual-layer structure provides a framework for distinguishing observation from intent, but it cannot force users to make honest judgments within that framework. If the team systematically skips every graduation judgment node — or more subtly, marks insufficiently reviewed observations directly as Requirements — the dual-layer structure degrades to form. The distinction between Architecture and Requirements becomes a heading difference rather than a substantive one.

This limit is structural: **any mechanism requiring human judgment has a quality ceiling determined by users' willingness to be honest**. The dual-layer structure lowers the cost of making honest judgments (providing clear locations, formats, and trigger points) but does not eliminate the need for judgment. Frameworks can make honesty easier but cannot make honesty automatic.

## Conclusion

The brownfield dual-layer structure is a design response to the false authority problem. It uses structural position within the same spec file to distinguish descriptive knowledge (Architecture) from prescriptive knowledge (Requirements), making knowledge maturity visible rather than concealed.

Four transferable principles:

1. **False authority is more dangerous than lacking authority because it eliminates the motivation to act.** Lacking specifications motivates "we need specs" awareness; false specifications create "specs are in place" illusion. Any practice that conflates form with substance — writing observations as specs, treating expired governance as effective protection, equating passing tests with correct behavior — is a variant of false authority. Recognizing the gap between form and substance is the prerequisite for establishing real authority.

2. **Graduation is implementation-driven, not audit-driven.** Batch reviews detached from specific modification contexts become rituals. An effective graduation mechanism is embedded in existing workflows — making a judgment each time a behavior is touched, rather than periodically scanning all entries. Incremental, local judgments match brownfield reality better than comprehensive, periodic audits.

3. **Granularity difference is a design feature, provided readers understand different layers' reading purposes.** The bird's-eye layer (architectural overview) and street-level layer (behavioral scenarios) serve different cognitive needs. Forcing both layers to use the same granularity — either writing architecture as scenarios or scenarios as overviews — would damage each layer's expressiveness. Allow granularity difference while using a heuristic ("can it be rewritten as a scenario") to identify misplaced entries.

4. **Structure can make honesty easier but cannot make honesty automatic.** The dual-layer structure lowers the threshold for making "is this observation or intent" judgments — providing clear locations, formats, and trigger points. But the judgment itself still requires human participation and honesty. This is a structural limit shared by all mechanisms requiring human judgment; acknowledging it is more pragmatic than pretending the framework can solve it automatically.
