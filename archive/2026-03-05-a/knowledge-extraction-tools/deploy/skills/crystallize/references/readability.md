# Readability Guidelines

Reference material for crystallize report generation. Apply these guidelines to all report content regardless of structure type.

**Language note**: Rule text is English (AI-readable convention). Examples use 正體中文 because they demonstrate readability patterns for target-language report output — style control requires target-language examples. Adapt the principles to your project's preferred output language.

## Terminology Introduction Protocol

When the report uses domain-specific terms not expected in the reader's general vocabulary:

### 1. First-Use Rule

First occurrence of a domain term must include either:

**Parenthetical gloss** — term followed by inline explanation:

> 管線拓撲（pipeline topology — 上游工具的輸出如何流向下游工具）

**Example-first introduction** — concrete scenario before the abstract term:

> 兩個工具共享同一個上游來源，但產出完全不同的下游成品——這種**管線拓撲**意味著它們不是重疊的，而是分歧的。

### 2. Density Limit

No more than **2 new domain terms per paragraph**. If a passage requires 3+, split into smaller paragraphs or add a scene-setting sentence first that uses only previously-established vocabulary.

### 3. Jargon Acceleration Check

When consecutive paragraphs each introduce new terms, insert a **bridging paragraph** that:
- Uses only previously-established vocabulary
- Connects the prior concept to the upcoming one
- Gives the reader a moment to absorb before the next new term

### 4. Sentence Complexity Limit

A single sentence MUST NOT pack **3 or more independent concepts** joined by subordinate clauses, nested parentheticals, or em-dash interruptions. When a sentence requires ≥ 2 levels of parenthetical nesting (e.g., `A（B（C））`), split into multiple sentences.

**Bad** (3 concepts + nested parentheticals in one sentence):

> 管線的初始設計完成了三件事：用「是否產出成品？」測試區分工具與約束、辨識三階段管線形態（評估→結構化→持久化）、以及為結構化工具建立核心機制——文體選擇（Experience Report / Analytical Essay / Technical Note）、品質閘門、可攜帶衍生物萃取。

**Good** (one concept per sentence, single-level parentheticals):

> 管線的初始設計解決了三個問題。第一，用「是否產出成品？」這個測試區分工具與約束。第二，辨識出三階段管線形態（評估→結構化→持久化）。第三，為結構化工具建立核心機制：文體選擇、品質閘門、可攜帶衍生物萃取。

## Narrative Continuity Rules

### 1. Phase Transitions

Each Discovery phase (or equivalent section in other structures) must open with a **transition sentence** connecting to the previous phase's outcome — what was resolved, and what new question or tension it raised.

**Bad** — abrupt topic shift:
> ### 階段三：內部一致性
> 框架精煉到一定程度後，一個矛盾浮現...

**Good** — connected transition:
> ### 階段三：內部一致性
> 術語問題解決後，框架的對外對齊已經站穩。但向內看，一個更深的矛盾浮現...

### 2. Background as Scene-Setting

Background establishes **starting conditions**, not a complete history. Include only what the reader needs to follow the Discovery section. If prior work spans multiple phases, summarize the **outcome state** ("the pipeline was complete but three boundaries emerged"), not the **process** ("first X was built, then Y was designed, then Z was added").

**Bad** — compressed process recap:
> 框架建立了四層文件階層、三類機制、知識路由表、Skeleton Coherence 規則和驗證程序。

**Good** — outcome state as starting condition:
> 框架經過數個階段的建立，從文件階層到機制分類，看似完整。然而，開始新增和修改治理文件後，一致性問題隨之浮現。

### 3. No Orphan Structures

Tables, callout blocks, and lists must be preceded by **at least one sentence of prose** that explains what the reader is about to see and why it matters. A structural element appearing without narrative introduction is an **orphan**.

**Bad** — orphan table:
> | 面向 | 平台機制規格 | 應用層治理 |
> |------|------------|-----------|
> | 檔案格式 | ✅ | — |

**Good** — introduced table:
> 這個分層讓框架的定位變得清晰。下表對照平台已提供的機制規格與專案自建的治理關注點：
>
> | 面向 | 平台機制規格 | 應用層治理 |
> |------|------------|-----------|
> | 檔案格式 | ✅ | — |

## Visualization Guidance

### 1. Complexity Threshold

When describing relationships between **3 or more entities**, a flow with branching paths, or a decision tree, prefer a Mermaid diagram over prose-only description. The diagram shows structure (what); surrounding prose explains causation and trade-offs (why).

### 2. Diagram Is Not Self-Sufficient

A diagram without surrounding prose is an orphan (see Narrative Continuity Rule 3). Every diagram must be:
- **Preceded** by a sentence explaining what the reader is about to see
- **Followed** by a sentence interpreting the key insight the diagram reveals

### 3. Diagram Language

Use English labels in diagrams for technical convention. Surrounding prose follows the report's primary language.

## Examples

### Decision Point: Full Callout vs Narrative-Woven

**Full callout format** (for Standard density tier — 2–4 Decision Points in report):

> **Decision Point**: 目的導向分類取代觸發導向
> — Alternatives: 「被 X 觸發，產出 Y」（描述何時啟用）vs.「延伸能力——可執行任務、參考知識或領域專業」（描述本質是什麼）
> — Outcome: 4 個始終啟用的單元被重新分類為 Rule 而非 Skill。分類系統與原生規格定義對齊

**Narrative-woven format** (for Selective/Narrative-first density tiers — 5+ Decision Points):

> 分類系統的核心問題浮現：初始分類使用「被什麼情境觸發」的思維，結果把始終啟用的編碼原則也歸為「工具」。面對這個分類錯誤，選擇了**以目的取代觸發作為分類基準**——相較於繼續用觸發時機描述本質，直接問「它是什麼」更穩定，因為本質不隨調用場景改變。結果是 4 個單元被正確重新分類為 Rule。

The narrative-woven format preserves all three elements (decision, alternatives, outcome) but embeds them in flowing prose instead of a rigid callout structure. The bold statement marks the decision for scanning; parenthetical or dash-separated clauses carry the alternatives and outcome.

### Terminology: Good vs Bad Introduction

**Bad** (jargon acceleration — 3 new terms in one sentence, no scaffolding):

> Skeleton Coherence 規則定義了三種骨架的耦合映射，用驗證程序防止耦合區段漂移。

**Good** (first-use explanations, one concept at a time):

> 框架的模板定義了耦合區段——同一份文件中必須同步的成對段落（例如 Rules 和 Checklist）。為了防止這些成對段落各自漂移，在模板定義層建立了一套同步規則（Skeleton Coherence）。

The good version introduces "耦合區段" with an inline definition, then uses that established term to introduce "Skeleton Coherence" — building vocabulary incrementally rather than front-loading.
