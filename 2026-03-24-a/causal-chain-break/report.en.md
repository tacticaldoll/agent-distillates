# Cognitive Load and Causal Chain Breaks: The Hidden Fault in Human-Agent Knowledge Transfer

**Structure**: Analytical Essay
**Date**: 2026-03-24T00:00
**Model**: claude-sonnet-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

---

## Introduction

An AI agent starts every session from zero. Its working memory contains only what is present in the current context window — files loaded automatically at startup, plus whatever the human provides during the session. This architectural fact has a non-obvious consequence: the quality of the agent's reasoning is bounded by the quality of what gets loaded.

But the problem isn't just completeness of information. The deeper problem is the structure of knowledge itself. When humans direct agents, they naturally transmit conclusions rather than the reasoning that produced them. This is rational — it's efficient, and humans often can't articulate why a conclusion is correct, only that it is. The result is a systematic pattern: agents receive endpoints without paths, and must reconstruct the reasoning from context clues alone.

This essay examines why this happens, what breaks when it does, and what it implies for how we structure the knowledge agents work from.

---

## Analysis

### The Stateless Agent

Unlike a human collaborator who accumulates context over weeks of work, an agent's memory resets completely between sessions. What persists is only what has been deliberately externalized — written into files that get loaded at session start.

This creates a fundamental asymmetry: the human carries a rich model of the project's history, the reasoning behind past decisions, the lessons from failed attempts. The agent carries only what was written down. When the human gives an instruction, they reason from their full model. When the agent responds, it reasons from loaded context plus inference.

The gap between the human's model and the agent's loaded context is filled by inference. Inference is not unreliable — it can produce good results — but it is not the same as knowledge. Inference can be systematically wrong in patterned ways, especially when the missing context follows a non-obvious rule.

### Cognitive Load as a Transfer Bottleneck

Cognitive load describes the mental effort required to hold and process information. When humans communicate instructions to agents, they naturally minimize their own cognitive effort — they describe what they want done, not how they arrived at wanting it.

Consider the difference between these two formulations:

- "Don't skip archaeology before implementing a new module"
- "Don't skip archaeology before implementing a new module — archaeology extracts behavioral commitments from legacy code that would otherwise be silently dropped in the rewrite"

The first is shorter and cheaper to transmit. But an agent receiving only the first can comply with the letter while violating the spirit — it knows the rule but not the stakes of breaking it. When pressured to move faster, an agent without the "why" has no basis for resisting the shortcut.

Cognitive load also explains why knowledge decays during handoffs. When a developer corrects an agent ("no, do it this way instead"), they transmit a conclusion formed from experience they never make explicit. The correction is transmitted; the reasoning is not.

### Well-Intentioned Correction as Systemic Risk

Corrections are the primary feedback mechanism in human-agent collaboration. They are almost always well-intentioned. But they systematically break causal chains.

When a human says "just skip archaeology this time, we're in a hurry," the agent learns a behavioral pattern without learning its boundary conditions. Future sessions may apply this pattern broadly when it should apply only narrowly. The human who gave the correction carried full context for why it was safe to skip in that instance — the target component had no legacy counterpart, the behavior was net-new, and so on. None of that context transferred.

The risk compounds over time. Each undocumented correction deepens a gap between what the agent does and what was originally intended. The gap is invisible — nobody records it — until an edge case exposes it.

Consider a documentation example: a document once stated that a component used a subprocess for privilege switching. This was wrong. The correction was made. But if only the corrected text survived — without recording why the original was wrong and what the correct model actually is — the next person working in this area might make the same mistake again, or make a related mistake that the documented correction doesn't cover.

Recording the causal chain ("the component performs privilege switching directly in-process — no subprocess, no fork, no intermediate process") is more durable than recording the corrected conclusion alone.

### The Causal Break

A causal break occurs when an agent's knowledge base contains a conclusion without the reasoning chain that produced it. The agent can apply the conclusion correctly in the expected case, but cannot reason correctly about edge cases, exceptions, or situations that require understanding the original intent.

Governance documents are typically evaluated on accuracy. But for agents, the more relevant standard is: does this document preserve enough causal context for an agent to reason correctly about situations the document authors didn't anticipate? An accurate document can still break causal chains if it transmits conclusions without origins.

---

## Reflection

The stateless nature of agents makes the externalization of causal chains a structural requirement, not a nice-to-have. This is qualitatively different from how documentation is typically thought about. Traditional documentation is written for human reference — summaries, how-tos, architecture overviews. These serve human memory, which has continuity between sessions and can fill gaps from experience.

Governance documents written for agents must do something different: they must supply the reasoning substrate that a human would supply from memory. This means they need to contain not just what is true, but why it is true, and what the stakes are of getting it wrong.

This reframes the question of what makes governance documents valuable. The standard is not "is this accurate?" but "does this preserve enough causal context for an agent to reason correctly about edge cases?"

The implication for correction practices is significant. When a human overrides an agent's behavior, they should record not just the override, but the reasoning. This is cognitively expensive — it requires the human to articulate reasoning they may never have made explicit — but it is the only way to prevent the override from creating a new causal break.

---

## Conclusion

**Agent reasoning quality is bounded by context quality** — Gaps in loaded context are filled by inference. Inference can be systematically wrong. The alternative is not more inference but better context.

**Corrections transmit conclusions, not reasoning** — Every undocumented correction is a potential causal break. The break is invisible until an edge case exposes it.

**Governance documents are causal chain preservation systems** — Their value is not correctness alone, but the degree to which they enable correct reasoning about situations the document authors didn't anticipate.

**Cognitive load is an adversary of knowledge transfer** — The natural tendency to minimize transmission effort strips causal context. Counter this by making the cost of stripping visible: document the stakes of each rule alongside the rule itself.

**"Why" is more durable than "what"** — A conclusion can be misapplied. A conclusion with its origin can be correctly applied to novel situations. Governance documents that record only conclusions build fragile knowledge bases.
