# Causal Discontinuity in Sequence Completion Machines: The Fundamental Limits of Agent Reasoning

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-11T22:30
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Introduction

In software engineering, we habitually regard AI agents as "developers who reason but sometimes reason incorrectly." This mental model determines how we design code, organize knowledge, and build governance mechanisms. But what if this mental model is fundamentally wrong?

This essay argues from the computational nature of agents for a foundational proposition: agents are not reasoning machines but sequence completion machines. This essential difference gives rise to a widely overlooked risk — causal breakpoints. Understanding this risk is a prerequisite for the subsequent discussion of code design constraints and governance safeguards.

## Analysis: The Nature of the Sequence Completion Machine

### The "Existence = Visible in Tokens" Proposition

Large language models (LLMs) are conditional probability sequence generators — the selection of the next token is based on the statistical distribution of preceding tokens. Output that appears to be reasoning is essentially "what typically follows similar context in the training corpus."

This nature brings three consequences, each contrasting with human developers:

| Human Developer | Agent |
|---|---|
| Derives unseen conclusions from rules | **Matches** the closest output from seen patterns |
| Can handle novel combinations never seen in the corpus | Novel combinations rely on **interpolation** of seen patterns; quality depends on similarity of constituent fragments |
| Stops to question when encountering contradictions | Selects the one with higher statistical weight in context when encountering contradictions; does not realize contradictions exist |

From this, a foundational proposition can be derived: **for a sequence completion machine, existence = having a corresponding token in the preceding text**. Information not in the context window is equivalent to nonexistent for the agent. Not "seen but misunderstood," but **simply not in the input**.

This proposition explains a common puzzle: why can agents sometimes write complex code yet make errors on seemingly simple judgments? The answer: their "capability" depends on the quality and completeness of tokens in the context window, not on some intrinsic "understanding." When tokens are complete, performance is excellent; when tokens are missing, it acts blindly — and cannot distinguish between these two states.

### The Double Summarization Mechanism: Why Encapsulation Is Especially Dangerous for Agents

When humans encapsulate code, they remove context that "an experienced person could derive," retaining structure, naming, and interfaces. This is the first summarization — human summary.

When agents read code, they convert text into a token sequence in the context window. This is the second summarization — machine summary. The two summarizations have different discard criteria:

| Summarization Stage | Discard Criteria | Retained Content | Assumed Reader |
|---|---|---|---|
| Human encapsulation (first) | "What an experienced person could derive" | Structure, naming, interfaces | A human with domain intuition |
| Agent reading (second) | "What does not appear in the token sequence" | Literal tokens in the preceding text | A state machine with only token sequences |

The critical point: the two losses are not additive but **multiplicative**. Human encapsulation precisely discards "derivable context" — but the agent cannot derive it. What the first summarization removes is exactly the lifesaving information for the second summarization. In other words: **what humans consider "redundant" is precisely what the agent needs most**.

A comparison illustrates this compounding effect:

```javascript
// Before encapsulation (humans find it verbose, but agent can fully trace causality)
function processOrder(order) {
    // Only pending and unshipped orders can be canceled —
    // shipped orders go through the return flow (see returnOrder), not here
    if (order.status === "pending" && !order.shipped) {
        cancelOrder(order);
    }
}

// After encapsulation (humans find it clean, but agent cannot see why-not)
function processOrder(order) {
    if (order.isCancellable()) {
        cancelOrder(order);
    }
}
```

The encapsulated version is cleaner for humans — `isCancellable()` is self-explanatory. But the agent cannot see the exclusion logic "shipped orders go through the return flow." If the agent needs to modify the cancellation conditions, it cannot determine whether the change would conflict with the return flow, because that causal chain was already deleted in the first summarization.

This is the concrete meaning of "highly encapsulated clean code is itself a human summary; introducing an agent creates a double summarization." The most ideal code for an agent is code that humans find "verbose" — with complete local context, explicit intent, and moderate duplication. Such code, after passing through the agent's context window summarization, still retains sufficient decision-making basis. Highly encapsulated clean code, after double summarization, leaves only skeleton without flesh.

### The Causal Breakpoint Spectrum

Based on the "existence = visible in tokens" proposition, causal breakpoints can be classified into three types. Each type manifests a different defect pattern in the agent's token sequence, with increasing severity:

| Type | Mechanism | Example | Severity |
|---|---|---|---|
| **Deleted** | Originally existing context removed by encapsulation | Local context disappears after DRY eliminates duplication; abstraction layers hide implementation details deep in the jump chain | Clues remain (function names, interface signatures); agent can attempt to trace via jumps |
| **Never existed** | Critical information never appeared as tokens in the code text | State changes during async operations; subscriber lists for runtime event bindings | Completely without clues; agent doesn't know what it's missing |
| **Erroneously exists** | Information exists in the token sequence but does not match reality | Stale descriptions in centralized knowledge bases; spec entries written back after contamination | Most dangerous — agent treats errors as facts and cannot self-verify |

The "deleted" type is a product of encapsulation and DRY. The "never existed" type is a product of runtime binding and async. The "erroneously exists" type is a product of knowledge base maintenance failure. Each requires a different handling strategy in subsequent risk analysis and response plans.

## Reflection: Causal Continuity as an Independent Constraint

### Causal Continuity vs. Semantic Boundary

Causal continuity and semantic boundary are two concepts that are often conflated. Distinguishing them is crucial for understanding agent risk.

Semantic boundary answers a spatial question: "Where does this concept's responsibility end?" Causal continuity answers a temporal question: "Is the basis for this decision visible in the token sequence?" Their intersection produces four states:

| | Causally Continuous | Causally Broken |
|---|---|---|
| **Clear semantic boundary** | Agent can operate correctly | Agent knows where to change, but changes incorrectly |
| **Unclear semantic boundary** | Agent changes correctly, but in the wrong place | Complete loss of control |

Semantic boundary is what DDD, modularization, and encapsulation are already doing. Causal continuity is an **additional constraint needed in the agent era** — it requires that critical information not only exists in the correct module but also appears as tokens within the agent's readable scope.

### Carrier Forms of the Causal Chain

Having clarified causal continuity, a natural question is: does the agent "cannot use a comment-free style"? The answer needs precision — what the agent needs is not "comments" but **the causal chain being continuous in the token sequence**.

Comments are just one means of supplementing the causal chain. Different means carry different dimensions of causality:

| Means | Carried Causal Dimension |
|---|---|
| Comments | Natural language tokens supplementing why / why-not |
| Type annotations | Structured tokens supplementing shape (what) |
| Good naming | Compressed causal tokens (what, partial why) |
| Intent tests | Executable causal tokens (expected behavior) |

The comment-free style assumes "naming is sufficient to carry causality." This usually holds for humans, because humans use intuition to fill gaps that naming cannot cover. For agents, naming can only carry **what**, not **why-not** and cross-module causality.

So the conclusion is not "you must write comments," but rather: **any means will do, as long as the causal chain does not break in agent-visible tokens.**

### Unified Root Cause

Returning to the opening proposition, all preceding discussions can be unified into a single table. Every category of agent risk can be reduced to the same root cause — critical information is not in the token sequence:

| Risk | Root Cause Reduction |
|---|---|
| DRY removes local context | Deleted tokens not in preceding text → nonexistent |
| Stale knowledge base | Erroneous tokens in preceding text → treated as fact |
| Runtime binding | Binding relationships not in static text → nonexistent |
| Async dead zones | Intermediate state changes not written in code → nonexistent |
| Design pattern indirection layers | Jump targets exceed context window → nonexistent |

## Conclusion

An agent is not "a reasoner who sometimes reasons incorrectly," but "a sequence generator that can only do statistical matching based on visible tokens." This essential difference gives rise to causal continuity as a new constraint dimension independent of semantic boundaries. In the agent's token sequence, causal breakpoints have three forms (deleted, never existed, erroneously exists), each requiring different prevention strategies.

The core principle is: **for a sequence completion machine, what is not in the token sequence does not exist.** This is the single root cause of all agent risks.
