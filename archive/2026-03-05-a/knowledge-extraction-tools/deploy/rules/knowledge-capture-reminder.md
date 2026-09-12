---
description: Proactively suggest distillation when conversation accumulates extractable knowledge, before context compression loses detail
---

# Knowledge Capture Reminder

## Purpose

Context compression is an invisible system event — neither the user nor the AI receives advance notice. Once compressed, detailed conversation content (alternatives evaluated, trial-and-error sequences, trade-off reasoning) is reduced to summaries, making high-quality report generation impossible for those topics. This rule provides heuristic signals for the AI to proactively suggest distillation before that window closes.

## Rules

### 1. Heuristic Signals

When **any** of the following signals are observed, suggest running `distill` to the user:

| Signal | Threshold | Rationale |
|--------|-----------|-----------|
| Independent decision topics | ≥ 3 topics with alternatives evaluated | Multiple mature topics accumulating — capture before detail is lost |
| Challenge-revision depth | Single topic with ≥ 3 rounds of user challenge → AI revision | Deep iteration produces high-maturity knowledge that compresses poorly |
| Topic shift after depth | User moves to a new subject after extended discussion on a previous one | The previous topic's detail will be pushed out of context first |
| Explicit session signal | User says they are switching tasks, ending for the day, or pausing | Last opportunity before context goes cold |

### 2. Suggestion Format

Keep the suggestion **brief and non-blocking** — one sentence, not a paragraph:

> "This session has accumulated several decision topics with trade-off analysis. Consider running `/distill` to evaluate what's worth capturing before context compression."

### 3. Frequency Limit

Do not suggest more than **once per signal category** in the same session. If the user declines or ignores the suggestion, do not repeat for the same signal. A new signal category (e.g., topic shift after a previous challenge-depth signal) may trigger a new suggestion.

### 4. No Auto-Invocation

This rule suggests distillation — it does NOT auto-invoke the distill skill. The user decides whether and when to distill.

## Checklist

- [ ] **Signal detected**: Does the current conversation state match any heuristic signal?
- [ ] **Not already suggested**: Has this signal category already been suggested in this session?
- [ ] **Non-blocking**: Is the suggestion brief (one sentence) and does not interrupt the user's current task flow?
- [ ] **No auto-invocation**: Is the suggestion a recommendation only, not an automatic skill execution?
