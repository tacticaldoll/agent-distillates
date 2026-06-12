# Local Completeness: Constraint Sets and Governance Safeguards for Agent Development

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-11T22:30
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Introduction

Agents are sequence completion machines. Multiple aspects of code cleanliness create causal breakpoints, and the compounding of DRY with centralized knowledge bases can escalate risks into systemic collapse. Given these premises, the question becomes: how to respond to these risks without rewriting existing code?

The response operates on two levels. The code layer requires a set of supplemental constraints to ensure agents make correct decisions within a single session. The governance layer requires additional safeguards to prevent cumulative cross-session contamination from eroding the foundations of spec-driven frameworks.

This essay first inventories the capabilities and boundaries of existing mitigation measures, then proposes code-layer constraint sets and governance-layer protection mechanisms.

## Analysis: Capabilities and Boundaries of Mitigation Measures

### Type Annotations

The real value of type annotations for agents is not correctness guarantees but **reducing jumps**. With type-annotated functions, agents don't need to trace to the implementation to know the shape of inputs/outputs. Each fewer jump reduces the probability of inference chain breakage.

However, types have clear mitigation boundaries. Classified by "whether types can mitigate":

| Risk Type | Can Types Mitigate? | Reason |
|---|---|---|
| `*args, **kwargs` passthrough | **Yes** | `TypedDict` / `Protocol` make parameter shapes explicit |
| Decorator changing signature | **Yes** | `ParamSpec` + `Concatenate` preserve the original signature |
| Mixin MRO conflict | **Partial** | `Protocol` defines expected interfaces, but MRO resolution order is still runtime behavior |
| `@property` side effects | **No** | Types only describe the shape of return values, not "whether reading triggers I/O" |
| `__getattr__` dynamic attributes | **No** | Dynamically generated parts remain blind spots |
| Metaclass | **No** | Types describe instance shapes, not "how the class construction process was rewritten" |
| Monkey Patching | **No** | Types are declaration-time contracts; the type checker does not re-verify after runtime replacement |

Types answer "what shape does this value have," not "where did this value come from" and "what happens when you access it." This is their fundamental boundary.

### Code as Doc (Good Naming)

Good naming reduces the risk of "understanding a function" but does not reduce the risk of "understanding a decision." Specifically, the blind spots of code as doc are:

| Gap | Explanation |
|---|---|
| **Why-not** | Paths that don't exist in the code — agent cannot distinguish "intentionally omitted" from "overlooked" |
| **Intent of boundary conditions** | `if (x > 0)` — is 0 an excluded valid value, or a value that can never occur? Code cannot express this |
| **Cross-file causality** | File A's implementation is due to File B's constraints; code itself cannot express this dependency direction |

The comment-free style assumes "naming is sufficient to carry causality." This usually holds for humans, because humans use intuition to fill gaps that naming cannot cover. For agents, naming can only carry **what**, not **why-not** and cross-module causality. The remedy is not returning to extensive comments, but leaving minimal necessary why/why-not annotations at **decision points**.

### Common Boundary of Mitigation Measures

Types, naming, intent tests — these measures answer **what** and **how** but do not answer **why-not** and cross-module causality. For agents, the latter are the primary sources of error.

This common boundary points to a core conclusion: no single measure can fully restore causal continuity. What is needed is a **multi-measure combination**, each covering different causal dimensions:

| Measure | Covered Dimension |
|---|---|
| Type annotations | Shape (what) |
| Good naming | Intent (what, partial why) |
| Intent tests | Expected behavior (expected what) |
| Why-not comments | Exclusion logic (why-not) |
| Binding lists | Runtime associations (who binds whom) |
| Co-located README | Cross-module causality (why this way) |

## Analysis: Code-Layer Constraint Set

### The Local Completeness Principle

Agent development does not need an entirely new paradigm, but rather a set of **supplemental constraints** imposed on existing paradigms — a supplemental filter. After each design decision passes traditional review, it goes through one more check: "can the agent understand this decision with local completeness?"

The core principle is local completeness: **within any single file, any single function, the agent can make correct decisions without jumping to other locations**.

The following constraint table contrasts traditional best practices with agent constraints:

| Dimension | Traditional Best Practice | Agent Constraint |
|---|---|---|
| DRY | Eliminate all duplication | Allow structural duplication with **different intents** to coexist; only eliminate duplication with identical intent |
| Abstraction layers | Layer by responsibility | Implementation reachable in at most 2 jumps; beyond that, add type annotations or intent comments at intermediate layers |
| Knowledge base | Centralized management | Only store knowledge not derivable from code (why-not, cross-module causality); knowledge derivable from code is **prohibited** |
| Spec authority | Architecture — code wins | Architecture updates require provenance markers — recording which change triggered it and what evidence supports it |
| Design patterns | Choose pattern by problem | Prefer statically traceable patterns; runtime-binding patterns must include a **binding list** (who binds whom, where) |
| Naming | Self-explanatory | Self-explanatory + **scope markers** — function names must express impact scope (`updateLocalCache` vs `update`) |
| Comments | As few as possible | Decision points must have why/why-not; the rest remain minimal |

### Signal Recovery Plan for Existing Projects

For existing projects that are already highly encapsulated, rewriting is not feasible. The remediation principle is **don't unwrap encapsulation, add signals** — supplement the causal signals that agents need but that encapsulation deleted, on top of existing encapsulation.

Prioritized by blast radius, highest risk first:

| Priority | Target | Recovery Method | Risk Reason |
|---|---|---|---|
| 1 | Functions on DRY shared paths | `@context` markers + why-not comments | Errors affect globally |
| 2 | Runtime binding points | Binding lists | Agent completely cannot see these |
| 3 | Async boundaries | Intermediate state change comments | Logic dead zones |
| 4 | Module entry points | Co-located README | Cross-module causality |
| 5 | Remaining encapsulation | Type annotations | Gradual supplementation |

Equally important is what not to do: don't unwrap encapsulation (that's rewriting, not remediation), don't write into centralized knowledge bases (signals must be co-located with code, otherwise you're back to staleness risk), don't supplement what code can derive (only supplement what the agent cannot derive).

### Coverage Boundary of the Constraint Set

Code-layer constraints can prevent causal breakpoints from occurring within a single session but cannot address cumulative effects across sessions. Specifically, the following risks exceed the code layer's coverage:

- **Contamination writeback of spec entries** — agent makes an incorrect modification based on stale knowledge in session A, and session B's reconciliation solidifies the error as Architecture
- **Knowledge base maintenance consistency** — no mechanism drives cross-session staleness detection
- **Architecture → Requirement promotion decisions** — authority escalation is irreversible; code-layer constraints cannot prevent it

These risks require governance-layer responses.

## Reflection: Governance-Layer Safeguards

The code-layer constraint set addresses the agent's causal breakpoint problem within a single session. But cumulative cross-session contamination — especially contamination solidification in spec-driven frameworks — requires additional safeguards from governance mechanisms.

### Architecture Update Provenance Markers

Every Architecture-layer update must include a provenance marker. The provenance marker answers three questions: Which code change triggered this update? What evidence supports the judgment that code behavior has changed? What was the Architecture description before the update?

Architecture updates without provenance cannot be verified by subsequent sessions. When an agent sees an Architecture entry, it cannot distinguish "this is a verified observation" from "this is an erroneous writeback from a previous session." Provenance markers allow any session to trace back to the change source and judge whether the basis is still valid.

Comparing the effect with and without provenance markers:

```markdown
# Without provenance (agent cannot verify)
### Architecture: Cache API Response Structure
Cache API returns `{ items: [...], total: N }`

# With provenance (agent can trace back to verify)
### Architecture: Cache API Response Structure
Cache API returns `{ items: [...], total: N }`
<!-- provenance: commit abc123, verified against service/cacheAPI.cpp:L45-L60, 2026-03-10 -->
```

### Knowledge Routing Contamination Protection

Knowledge routing rules need an explicit contamination protection constraint: **only knowledge not derivable from code may be written**. Knowledge derivable from code is prohibited from entering the knowledge base.

The purpose of this constraint is not merely avoiding redundancy. More importantly, it avoids the staleness risk of "code changed but knowledge base wasn't synced." Every entry in the knowledge base that is derivable from code is a potential contamination entry point — after code evolves, these entries become "erroneous existence" type causal breakpoint sources, and because they exist in the "authoritative" knowledge base, the agent will trust them preferentially.

Knowledge types suitable for the knowledge base:

| Type | Example | Why Code Cannot Derive It |
|---|---|---|
| Why-not decisions | "WebSocket not used due to firewall restrictions" | Code cannot show excluded alternatives |
| Cross-module causality | "Module A's format is due to Module B's parser requirements" | Dependency direction is not visible in code |
| Historical constraints | "Legacy API preserved because third-party integration hasn't migrated" | Time-dimension decisions are not expressed in code |

### The Asymmetric Effect of RFC 2119 Keywords

RFC 2119 defines the semantic strength of keywords like MUST, SHALL, SHOULD, and MAY. In human teams, these keywords are communication tools — even when MUST is written, a senior engineer can still use judgment to question "is this spec outdated?" But for agents, keywords are non-overridable instruction intensity gradients:

| Keyword | Human Developer | Agent |
|---------|----------------|-------|
| MUST / SHALL | Comply, but can question based on experience | **Absolute compliance** — no ability to question; chooses to comply with spec when encountering contradictions |
| SHOULD | Tends to comply, understands exceptions exist | **High-weight compliance** — unless explicit exception condition tokens exist in the context window |
| MAY | Chooses based on circumstances | **Low-weight ignore** — unless context explicitly demands, tends to skip |

This asymmetry has two consequences. First, MUST's binding force on agents far exceeds its force on humans — humans have "final veto power" (intuitive judgment that spec may be outdated), agents do not. Second, MAY's binding force on agents is far weaker than on humans — humans consider whether MAY-marked options apply, while agents directly ignore them when lacking explicit context.

This gradient difference directly impacts spec authoring strategy:

| Scenario | Incorrect Choice | Correct Choice | Reason |
|----------|-----------------|----------------|--------|
| Observational behavior (code currently does this) | MUST | Architecture-layer description, no keywords | Observation is not a contract; MUST solidifies and blocks evolution |
| Rules with exceptions | MUST | SHOULD + explicitly list exception conditions | Agent cannot judge exceptions on its own; MUST forces it to enforce incorrect behavior in exception scenarios |
| Optional features | Unlabeled | MAY + trigger condition description | Unlabeled = agent doesn't know the option exists; MAY + conditions = agent considers at appropriate moments |
| Core invariants | SHOULD | MUST | Core invariants need maximum binding force; SHOULD gives agent unwarranted flexibility |

Comparing correct and incorrect keyword usage:

```markdown
# Incorrect: observational behavior with MUST (once code evolves, agent enforces outdated behavior)
### Requirement: Cache TTL
Cache entries MUST expire after 300 seconds.

# Correct: observations go to Architecture, contracts use SHOULD to preserve flexibility
### Architecture: Cache TTL
Cache entries currently expire after 300 seconds.
<!-- provenance: verified against cacheManager.cpp:L120, 2026-03-10 -->

### Requirement: Cache TTL
Cache entries SHOULD expire within a configurable TTL. The default TTL is defined in Architecture.
```

In other words, **keyword choice itself is a contamination firewall**. SHOULD rather than MUST preserves the agent's room to maneuver in contradiction scenarios; Architecture-layer descriptions without keywords prevent behavioral observations from being accidentally solidified into contracts.

### Requirement Promotion Gate

The promotion from Architecture → Requirement is the irreversible turning point in the contamination chain. Once contaminated behavior acquires RFC 2119 authority (MUST / SHALL), the aforementioned asymmetric effect activates — the agent complies absolutely, rejecting any repair attempt as a "spec violation."

Therefore this promotion **must** have a human gate — it cannot be automatically executed by agents, and it cannot happen implicitly during reconciliation. Gate checkpoints:

1. **Multi-session cross-verification** — Has the observation been confirmed in at least 2 independent sessions? A single session's observation may be based on contaminated code
2. **Independent code evidence** — Is there direct code evidence (tests, type signatures) supporting this behavior, rather than relying solely on reconciliation's automatic judgment?
3. **Requirement consistency** — Is the new Requirement logically consistent with existing Requirements? Contradictions are a strong contamination signal
4. **Keyword strength review** — Does the RFC 2119 keyword chosen during promotion match the behavior's certainty? Rules with exceptions should not use MUST

### Self-Protection of Governance Tools

Governance tools (reconciliation, precipitate, crystallize) are themselves tools operated by agents. When processing content involving architectural decisions, paradigm conflicts, or semantic boundaries, they require additional quality protection.

Specifically, the quality gate needs to include a check: outputs involving the above topics must include contrastive examples — incorrect/correct or before/after code or logic comparisons. The purpose of this requirement is to prevent abstract descriptions from losing discriminative power after multiple transmissions. Abstract descriptions can be interpreted arbitrarily, but concrete code comparisons are unambiguous — they fix the precise boundary between "correct" and "incorrect."

## Conclusion

Agent development does not need an entirely new paradigm. It needs a set of supplemental constraints imposed on existing paradigms — a filter with local completeness as its core principle. Code-layer constraints ensure causal continuity within a single session; governance-layer safeguards intercept cumulative cross-session contamination.

The two defense layers each have their own coverage and boundaries. The code layer cannot address spec contamination; the governance layer cannot address causal breakpoints within a single function. They are not substitutes but complements — missing either layer allows the other's protections to be bypassed.
