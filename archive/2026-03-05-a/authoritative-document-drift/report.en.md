# When Authoritative Documents Are Wrong

<!-- front matter -->
**Structure**: Experience Report
**Date**: 2026-03-05T09:21
**Model**: claude-opus-4-6
**Agent**: Claude Code VSCode Extension 2.1.66
**Source**: reconstruction

## Background

Multiple governance documents and knowledge extraction tools were operational — rules referenced skills, skills referenced rules, all claiming alignment with the external platform's specification. The meta-rule explicitly stated: "When project conventions diverge from native definitions, native definitions prevail." The system appeared complete, but had never been systematically audited for internal consistency.

The knowledge extraction pipeline had just been designed, providing tools to evaluate, structure, and persist conversation knowledge. During the process of using these tools, numerous documents were created and modified. Terminology flowed across documents — the same concept could use different wording in different places, and specification updates could miss downstream references. Building a terminology audit capability to detect such drift was a natural need.

## Discovery

### Phase 1: The First Audit

The terminology audit was designed as a four-dimension scan: terminology consistency, native spec alignment, structural skeleton compliance, and cross-reference integrity. Upon creation, it was immediately executed against all governance documents.

The first audit scanned 15 files and produced 7 findings. Two errors were stale Mechanism Selection wording in the crystallize skill — "triggered by situation, produces artifact" used to describe skills, while the current authoritative definition was "extends capability — actionable task, reference knowledge, or domain expertise." Two warnings were verb drift in the precipitate skill — "distill knowledge" appeared where "extract knowledge" was meant, since precipitate is downstream of distill and should not claim to perform distillation.

These corrections were straightforward, applied item by item and committed. But one informational finding was flagged as "address later" — a field spelling issue across 11 skill definition files: `user-invokable` (with k) appeared in all files, while the audit suspected the correct spelling was `user-invocable` (with c).

### Phase 2: The Runtime Contradiction

To confirm the spelling suspicion, the team consulted the external platform's official documentation. The documentation explicitly stated the field name as `user-invocable` (with c). Based on the documentation's authority, the team began changing `user-invokable` to `user-invocable` across all 11 skill definition files.

Nine files were successfully modified. But then the IDE's schema validator — a built-in YAML frontmatter validation feature in the external platform's VSCode extension — injected diagnostic messages:

> "Attribute 'user-invocable' is not supported in skill files. Supported: ...user-invokable..."

> **Decision Point**: The runtime validator rejected the spelling confirmed by official documentation
> — Alternatives: Trust official documentation (assume the validator has a bug) vs. trust runtime behavior (what the system actually accepts is correct)
> — Outcome: Trust runtime behavior. All nine modified files were reverted to `user-invokable`. The runtime validator reflects the system's actual schema, while documentation may be outdated or incorrect

This finding did more than correct a spelling — it exposed an undefined layer in the governance framework. The meta-rule declared "native definitions prevail" but did not define what counts as a "native definition" or how to verify one. Official documentation was implicitly treated as the final authority, and runtime behavior revealed the fragility of that assumption.

### Phase 3: Establishing Verification Precedence

The follow-up question "what exactly is the IDE schema validator?" led to a layer analysis. The validator is a built-in YAML frontmatter schema validation feature in the external platform's VSCode extension, embedding the same schema as the runtime. This means it reflects not the behavior described by documentation, but the system's actual behavior.

Ranking verification sources by reliability produced a verification precedence for external authority:

1. **Runtime schema validator** (actual behavior — what the system accepts)
2. **Source code** (design intent — what the system was built to do)
3. **Official documentation** (descriptive — what the system is said to do)

Higher priority overrides lower. This precedence was recorded as an experience section in the terminology audit skill, serving as an operational baseline for future audits.

### Phase 4: Recognizing the Internal Parallel

After establishing verification precedence, a structural parallel surfaced. The project's own code-as-documentation rule had already defined a precedence for internal knowledge: code itself > co-located documentation > knowledge base. The logical structure was entirely isomorphic — "actual behavior prevails over descriptive documentation."

This recognition produced a generalizable principle: any governance framework that claims alignment with an external authority must simultaneously define that authority's verification levels. Otherwise, the alignment claim is a trust assumption, not a verifiable constraint. Documentation may lag behind runtime, source code may diverge from documentation, but only actual behavior is irrefutable.

### Phase 5: Stale Terms in Metadata

After the crystallize skill underwent substantial enhancement (readability guidelines, deploy kit specification, batch coordination, report indexing), a second terminology audit was executed, this time targeting the crystallize skill specifically.

The audit discovered a classic metadata staleness pattern: the skill's process sections and rules sections had been comprehensively updated from "portable derivatives" to "deploy kit," but the frontmatter description at the top of the file still read "optional portable derivatives." The root cause was that concept-renaming workflow covered the process sections (the content body) but missed the metadata header (a one-line description) — metadata is not in the natural work path of a renaming operation.

The fix was updating the frontmatter description to reflect current terminology. But the finding itself revealed a more general drift pattern: when a concept is renamed in a document's body, **non-body locations** (frontmatter, comments, example references) are the most likely to be missed, because they are not in the primary attention path of the modification operation.

### Phase 6: The Forward Specification Problem

The same audit also discovered another class of issue. The crystallize skill's report index section (Phase 8) stated: "Consumers: distill (checks topic overlap before recommending extraction)." But examining distill's skill definition file, it had no mechanism to read or reference the report index.

> **Decision Point**: Documentation claimed consumer behavior that consumers had not implemented
> — Alternatives: Remove the consumer reference (loses design intent) vs. retain but annotate as planned (distinguishes current state from intent)
> — Outcome: Retained with annotation "planned integration — not yet implemented in consumer skills." This distinguishes what the system does now (index exists, no consumer reads it) from what the system is ultimately designed to do (distill checks for overlap)

This is a specific type of documentation error: **forward specification** — describing planned behavior as if it were already implemented. The document reads like a factual statement, but is actually a forward-looking design intention. It shares the same structure as Phase 2's runtime contradiction — documentation describes not the system's actual state, but its desired state.

## Decisions Summary

| # | Decision | Rationale |
|---|----------|-----------|
| 1 | First audit's direct corrections (stale wording, verb drift) | Clear terminology inconsistency, authoritative definitions unambiguous |
| 2 | Trust runtime validator, revert documentation-guided changes | Runtime schema reflects actual behavior, documentation may be outdated |
| 3 | Establish three-level verification precedence (runtime > source > docs) | Generalize the lesson — any external authority alignment needs verification levels |
| 4 | Stale metadata correction | Concept renaming missed non-body locations |
| 5 | Forward specification annotated as "planned" | Distinguish current state from design intent, avoid misleading consumers |

## Supplementary Knowledge

Authoritative documents can be wrong in at least three ways, each requiring a different detection strategy:

| Error Type | Definition | Detection |
|------------|-----------|-----------|
| **Factually wrong** | Document says X, but runtime does Y | Runtime verification (schema validators, test execution) |
| **Stale** | Document once correctly said X, but the underlying system changed | Periodic re-verification; non-body locations (frontmatter, example references) are high-risk zones |
| **Forward-projecting** | Document describes planned behavior as if already implemented | Consumer-side verification — check whether claimed consumers actually possess the corresponding mechanisms |

All three share a root cause: documents describe **intent or historical state**, while code and runtime describe **current actuality**. Any governance framework that trusts documents without verification inherits this gap.

## Key Lessons

1. **Official documentation is not the final authority — runtime behavior is.** When official documentation and runtime behavior contradict, runtime wins. This is not because documentation is "deliberately" wrong, but because documentation describes intent or historical state while runtime reflects current fact. Verification precedence (runtime > source code > documentation) is the formalization of this reality.

2. **Internal precedence principles extend to external dependencies.** Code-as-documentation (code > docs > knowledge) and external authority verification (runtime > source > docs) are two instances of the same principle — actual behavior prevails over descriptive documentation. Recognizing this isomorphism helps avoid reinventing the same principle in different contexts.

3. **Metadata is a high-risk zone for terminology drift.** When a concept is renamed in a document's body, frontmatter, one-line descriptions, and example references are the most likely to be missed — they are not in the natural work path of the modification operation. Terminology audits must include non-body locations in their scan scope.

4. **Forward specifications need explicit marking.** Describing planned behavior as implemented fact misleads consumers. A "planned integration" annotation is clumsy but honest — it distinguishes the system's current state from design intent. This follows the same logic as "TODO" comments in code — marking incomplete parts is safer than pretending completeness.

5. **External alignment claims in governance frameworks need verification mechanisms.** Declaring "native definitions prevail" without defining verification levels builds alignment on trust assumptions. When trust assumptions break (as in this case's spelling contradiction), alignment claims lose operability. Verification precedence converts trust assumptions into verifiable constraints.
