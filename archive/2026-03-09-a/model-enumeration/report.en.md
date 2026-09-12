# Driving Model Enumeration: Five Paradigms of Project Evolution

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-09T22:30
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.72
**Source**: conversation

## Introduction

As a project matures from prototype to production, the basis for "what to do next" changes over time. Early on, the code itself is the entire answer — if it runs, it's correct. Later, specification documents may be needed to define "what counts as done." This shift is not a gradual blur but a recognizable paradigm migration — at each stage, a different type of document or mechanism holds decision authority.

Systematizing this phenomenon yields a concept: the **driving model**. A driving model answers the question: "At the current stage, what document type determines the project's next step?" When the document type holding decision authority differs, the project's behavioral patterns — how decisions are made, how correctness is verified, how the system evolves — are fundamentally different.

This essay enumerates five driving models and analyzes each one's characteristics, guarantees, and limits.

## Analysis

### Definition and Identification of Driving Models

A driving model is not a tool choice but an authority allocation. At any point in time, a project implicitly answers "what is the highest authority on behavior" — and that answer might be code, tests, operational skills, quality guidelines, or behavioral specifications. The way to identify the driving model is to observe: when team members disagree about whether a behavior is correct, what do they consult? That consulted artifact is the current driving model.

The five driving models each occupy a different concern dimension. The following matrix presents their core differences:

| Dimension | Code-Driven | Test-Driven | Skill-Driven | Guideline-Driven | Spec-Driven |
|-----------|-------------|-------------|--------------|-------------------|-------------|
| **Decision authority** | Code itself | Test cases | Skill definitions | Guideline documents | Specification documents |
| **Question answered** | What does the system do now? | Does behavior match expectations? | What is the next operation? | What counts as good quality? | What counts as done? |
| **Guarantees** | System is runnable | Behavior is verifiable and regression-protected | Operational process consistency | Quality floor | Behavioral contract clarity |
| **Cannot guarantee** | Whether behavior matches intent | Whether tests reflect correct intent | Correctness of operational goals | Whether quality aligns with business needs | Whether specs reflect real requirements |
| **Structural failure mode** | Behavior accidentalization | Illusion of tests-as-specs | Process ritualization | Quality spinning | Spec-reality drift |

The matrix reveals a pattern: each model's "cannot guarantee" is precisely the problem the next model attempts to solve. Code-driven cannot guarantee behavioral intent; test-driven attempts to externalize intent as assertions. Test-driven cannot guarantee the correctness of tests themselves; spec-driven attempts to provide an upstream source of intent. This is not coincidental — it reflects how each layer of abstraction fills the semantic gap left by the previous one as a project matures.

### Individual Characteristics of the Five Models

**Code-driven** is the most primitive and most prevalent model. Code is the sole source of truth — no specs, no process documents. "Understanding the system" equals "reading the code," and the correctness of changes is verified by "it runs after modification." Its value is zero abstraction overhead, suitable for small projects or prototyping phases. Its limit lies in cognitive capacity — when system scale exceeds what one person can fully comprehend, no one can answer "is this behavior intentional or coincidental."

**Test-driven** externalizes behavioral expectations into executable assertions. Correctness of changes is no longer judged by "it runs" but by "tests pass." Regression protection is its most direct contribution — when modifying A, a test failure in B signals an unexpected side effect. But tests can only verify "expectations that have been expressed." When tests lack an upstream source of intent, "should this test exist" becomes an unanswerable question.

**Skill-driven** encapsulates operational procedures into repeatable, standardized routines. The same trigger conditions produce the same operation sequence, and operational knowledge transfers from personal memory to skill definitions. It solves "how to operate consistently" — but skill definitions specify "how to do" without specifying "how well." Perfectly executed skills can produce inconsistent quality.

**Guideline-driven** encodes quality standards into explicit rules. "Good code" transforms from personal preference to concrete definition, and code review gains objective criteria — citing guideline numbers rather than personal taste. It guarantees a quality floor but not direction. Code can perfectly satisfy every quality guideline yet implement the wrong behavior — because guidelines don't define "what behavior counts as done."

**Spec-driven** makes behavioral specifications the highest authority. "Done" has an explicit definition — all scenarios in the spec are satisfied. Behavioral intent and implementation are separated — specs describe "what should be," code implements "how to achieve it." Its limit is the correctness of specs themselves — when specs drift from reality, documents look perfect but implementation has diverged.

### Common Characteristics of Structural Failure Modes

The five models' structural failure modes appear different but share a common characteristic: **each model fails when its authority boundary is over-extended**.

Code-driven's failure is not about poor code quality but about equating "code runs" with "behavior is correct" — extending runnability into a guarantee of correctness. Test-driven's failure is not about poorly written tests but about equating "tests pass" with "behavior is correct" — extending verifiability into a guarantee of intent correctness. This pattern repeats for every model: skill-driven extends process consistency into goal correctness; guideline-driven extends quality achievement into directional correctness.

The value of recognizing this pattern: it transforms failure modes from "the model is bad" into "the model is being used beyond its capability boundary." Each model is effective within its own boundary. The problem lies not in the model's quality but in whether users have recognized the boundary.

## Reflection

### Models Are Not Quality Judgments

The enumeration of five models may suggest a value ranking — code-driven is "primitive," spec-driven is "mature." But this reading ignores the decisive role of context. A two-week prototype using code-driven is a perfectly reasonable choice — building a spec framework for it would be over-engineering. A decade-old legacy system still on code-driven might be problematic — but might not be, depending on system complexity and team cognitive capacity.

A model's "correctness" is not determined by the model itself but by its fit with the project's current needs. The error is not using a "lower-level" model but failing to recognize the need for migration when a higher abstraction level is required.

### Blind Spots of the Matrix

The characteristic matrix presents a clean classification, but the tidiness of classification masks the messiness of reality. In real projects, the driving model is often not singular — different modules of the same project may operate under different driving models. Core business logic might have specs while peripheral tools remain code-driven. This mixed state is not anomalous but normal.

The matrix's value lies not in perfectly describing reality but in providing a vocabulary for identification. When a team can say "this module is code-driven while that module is spec-driven," they can make different decisions for different modules — rather than forcing the same methodology across all areas.

## Conclusion

A driving model is an identification framework for understanding how a project "decides what to do next" at different stages. The five models — code-driven, test-driven, skill-driven, guideline-driven, spec-driven — each answer different questions, provide different guarantees, and fail at different boundaries.

Three transferable principles:

1. **Every driving model has a clear capability boundary; failure comes from over-extending that boundary.** Code-driven guarantees runnability but not intent correctness. Equating "it runs" with "it's correct" is not a problem with code but a misjudgment of the model's capability. Recognizing boundaries takes priority over upgrading models.

2. **A model's appropriateness is determined by context, not by the model's "level."** Code-driven in the prototype phase and spec-driven in the mature phase may both be optimal choices. The error lies not in which model was chosen but in failing to recognize when context has changed and when migration is needed.

3. **Driving models provide identification vocabulary, not implementation prescriptions.** Being able to name the current driving model enables precise discussion of its guarantees and limits. This precision has more operational value than the generalized advice "we should upgrade to spec-driven" — because it grounds decisions in specific capability gaps rather than abstract maturity labels.
