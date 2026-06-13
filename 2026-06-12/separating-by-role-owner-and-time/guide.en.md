# Separating by Role, Owner, and Time

**Structure**: Analytical Essay
**Date**: 2026-06-12T22:32
**Model**: claude-opus-4-8
**Agent**: Claude Code VSCode Extension 2.1.169
**Source**: conversation

## Introduction

Setting up a new project to be driven by AI coding agents forces many small
process decisions early: where instructions live, how work is proposed and
verified, what gets committed and when. Over one such session, three separate
moments shared a single shape. Each time I reached for an answer that collapsed
two things that *looked* alike, and each time a short correction revealed they
played different roles. The corrections were not about facts — the code worked,
the tools were fine — they were about **placement and timing**: what an artifact
means, who owns a rule, and when a convention should harden.

This essay extracts the common error and the discipline that prevents it. The
thesis: a surprising share of process mistakes come from merging two things that
resemble each other but differ by **role**, by **owner**, or by **time**. The fix
is almost never more rules — it is drawing the distinction.

## Analysis

I take the three distinctions in the order they surfaced, generalize each past
its original setting, then name the pattern they share.

### Distinction by time: which conventions to fix up front

On a clean project the instinct is to define all conventions at once — standards,
architecture rules, the guiding charter. It feels responsible. The reframe that
corrected it: do the first real unit of work first, *then* codify.

The resolution is to sort conventions by whether they depend on direction. Some
are direction-independent — how you verify that work is done (build, test, lint,
format) holds regardless of what the project becomes, so fixing it early is free
and immediately useful. Others are direction-dependent — the architecture, the
domain charter, the module boundaries — and writing them before the first real
task means inventing constraints you cannot yet justify. Those should be
*discovered* by doing one real slice of work, then written down once they have
evidence behind them. The error to avoid is treating "responsible" as
"decide everything now"; some decisions are cheaper and truer after one pass
through the actual work.

### Distinction by owner: where a rule lives follows from whose rule it is

The second moment: I took a rule — drop a particular metadata trailer from
commit messages — and wrote it into the shared, committed guideline that every
contributor and agent reads. The correction: that is a *personal* preference for
how one person wants their tool to behave, not a team convention everyone must
follow.

The principle underneath is that **the location of a rule should follow its
ownership, not its topic.** Both rules were "about commits," so by topic they
belonged together. But a shared convention binds everyone and belongs in the
shared, versioned artifact; a personal steering preference binds only the
relationship between one user and their tool and belongs in that user's private
configuration. Filing a personal preference as team policy quietly over-claims
authority and pollutes the shared contract; filing a team rule as a private note
loses it for everyone else. The ordering question is "whose rule is this?" before
"what is it about?"

### Distinction by role: the proposed change vs the current truth

The third moment was the sharpest. In a workflow where work flows as proposed
*changes* against a *current specification*, I proposed bundling the step that
promotes a change's spec into the canonical specification together with the step
that files the finished change away. The pushback was a question: "why isn't it
implement + promote?" — that is, promotion is its own step, and it pairs with
*verification*, not with archival.

Untangling it required separating two artifacts that both look like "the spec":

- The change's *delta* — the contract for this specific change. It is written
  first, it drives the implementation, and it is what you verify the
  implementation against.
- The *canonical* spec — the cumulative record of what the system actually does
  once shipped.

Once they are separated, the timing of promotion is forced rather than
arbitrary. You promote the delta into the canonical spec only *after* the
implementation is verified against it, because the canonical spec asserts shipped
reality, and asserting behavior that is not yet built would be a lie. Promotion
is therefore triggered by "verification passed," and it is logically distinct
from archival, which is mere bookkeeping. Bundling promotion into archival hid
this: it made the trigger look like "we are done filing" instead of "the work is
proven."

## Reflection

The three look like different problems — when to standardize, where to file a
rule, how to sequence steps — but they are one problem wearing three hats. In
each case two things were similar enough to merge — two commit rules, two spec
artifacts, "all the conventions" — and the merge erased a distinction that
mattered: by **time** (stable vs direction-dependent), by **owner** (shared vs
personal), by **role** (contract-to-verify-against vs record-of-what-shipped).

A useful tell is that the corrections all arrived as *questions*, not objections:
"why isn't it X + Y?", "isn't that a personal preference?", "shouldn't we do the
first one first?" A clarifying question is often the signal that two roles have
been collapsed. The right response is to name the second role, not to defend the
merge.

The discipline has a cost, and over-applying it is its own failure mode.
Splitting everything into separate artifacts, owners, and phases produces
ceremony and friction. These three distinctions earned their keep because each
separated things with genuinely different lifecycles: a personal preference
changes independently of team policy; a contract is consumed once while
shipped-truth accumulates forever; a verification command is stable while an
architecture is still forming. So the test for whether a distinction is worth
drawing is not "are these conceptually different?" but "do they change, get
owned, or solidify on different schedules?" If two things move together, keeping
them together is correct.

## Conclusion

Three transferable rules, and the meta-rule that generates them:

1. **Sort conventions by direction-dependence.** Fix the direction-independent
   ones (how you verify done-ness) up front; let the first real slice of work
   reveal the direction-dependent ones before you write them down.
2. **Route a rule by its owner, not its topic.** Shared conventions go in the
   shared, versioned artifact; personal tool-steering preferences go in personal
   configuration. Ask "whose rule is this?" first.
3. **Keep the contract and the shipped-truth separate.** The per-change contract
   is what you verify against; the canonical record is what has shipped. Promote
   one into the other only after verification, and treat that promotion as its
   own step rather than a side effect of closing the change.
4. **Meta-rule: when two things tempt you to merge them, check whether they
   change, are owned, or solidify on different schedules.** If they do, keep them
   separate; if they move together, merging is right. Treat a clarifying question
   as a hint that two roles have quietly been collapsed into one.
