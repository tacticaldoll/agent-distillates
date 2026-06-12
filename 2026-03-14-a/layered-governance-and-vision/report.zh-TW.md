---
title: "架構倒置：以雙層治理防禦權威漂移"
description: "探討外部規格 (Spec) 作為靜態文件，必須依賴動態的自動化治理機制 (Governance Agents) 來維護與守護的架構倒置原理。"
---

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-03-14T16:50
**Model**: Gemini 3 Flash
**Agent**: Antigravity IDE 1.19.6.0
**Source**: conversation

## 背景 (Background)

在追求系統強健性與一致性的實踐中，工程界長久以來將希望寄託於「規格驅動開發 (Spec-Driven Development, SDD)」。然而，當我們剝開這層外衣，審視其在實際複雜環境中的運作表現時，往往發現事與願違。如果我們不依賴完美的規格 (Spec) 來推動初期的開發，系統的秩序究竟依靠何種機制來維持？這引出了一個關鍵的架構反思：傳統治理模型將靜態的規格視為最高法律，卻忽略了其本身的脆弱性。本文將深入探討一項被忽略的架構倒置 (Architecture Inversion)，揭示規格與自動化治理機制（如 Agent Rules）之間的真正依賴關係，並證明「Spec-Driven 是終局願景，而非過渡期的解決方案」。

## 發現 (Findings)

### 迷思：靜態規格的絕對權威
在傳統的 SDD 思維中，結構化的規格（例如 OpenAPI 文件）被視為至高無上的單一事實來源 (Single Source of Truth, SSOT)（對應治理階層的 Level 0 或 Level 1）。系統內的所有行為、所有的介面與資料模型，都必須嚴格聽命於它。

但我們的分層治理框架給出了一個截然相反的結論：**真正的靜態規格，永遠只應處於 Level 2（規格 / 外部輸入）的位置。它自己無法保護自己，必須依賴位於 Level 1 的自動化治理機制 (Agent Governance) 來進行保護與代為維護。**

### 理論：為何規格只是 Level 2
1.  **本質上是被驗證的對象**：規格描述的是「這個介面的資料權限與屬性長什麼樣子 (Data Contract)」。它是一份業務層面的領域知識，其自身**完全沒有能力**去規範開發者提交程式碼的頻率、遇到命名衝突如何處理、或是專案核心的目錄架構。
2.  **靜態本質的脆弱性**：文件與規格模型都是靜態的。若沒有人去主動讀取它，若沒有檢查工具 (Linter) 在提交前進行靜態掃描阻擋，它不過是一組結構完美的字元。缺乏主動防護，權威文件漂移 (Authoritative Document Drift) 必然發生。

### 實踐：真正的權威在於 Agent Governance (Level 1)
維持專案免於混亂的骨幹力量，並不是那些供人仰望的完美規格書，而是位於 Level 1 的防護網。它是自動化的檢查腳本、強硬的 Linter 設定檔、以及 CI/CD 管線中的攔截器。它是定義了專案「必須如何運作」的實體執行機制。

這個架構的美妙之處，在於它形塑了一個安全且務實的「雙軌並行 (Dual-Track)」防護機制。

## 實務對比 (Practical Contrastive Examples)

以下展示單軌規格約束，與真正的雙層防護機制在效果上的深度差異。

**[錯誤/稀釋] 依賴純規格約束的單軌系統**

```yaml
# Level 1 (無) : 缺乏自動化防範機制
# Level 2 (靜態規則) :
UserSchema:
  type: object
  properties:
    phone:
      type: string
```

*分析*：遇到新的需求時，開發者為了省時，直接在程式碼中修改了 `phone` 為整數並上線。此時靜態規格成為了謊言。系統的真實行為已經改變，但由於缺乏強制巡邏的機制，規格與現實徹底脫鉤。長此以往，規格成為了歷史參考文物，無法約束開發。

**[正確/高解析度] 雙軌並行的分層架構防護 (Level 1 保護 Level 2)**

```python
# Level 1 (Agent Governance / Validation Gate Script) : 
# 必須驗證原始碼中的型別是否與規格文件嚴格相等。若不相等則阻斷編譯
# 或在觀察期 (Track B) 自動將未知欄位同步至觀察性 Schema，並打上警告標籤。
def validate_code_against_schema(schema_path, code_context):
    schema = load_level_2_schema(schema_path)
    if not match(schema, code_context.types):
        if is_observational_phase():
            # Track B：默默收集債務，寫入 x-conflict 供人工後續決斷
            inject_conflict_record(schema, code_context.diff)
            pass
        else:
            # Track A：嚴格阻斷
            raise SystemExit("Compilation blocked: Type mismatch with Level 2 Spec.")

# Level 2 (Spec): 被層層保護的靜態業務合約
```

*分析*：此案例中，真正支撐起單一事實來源的，是 Level 1 的巡邏與攔截腳本。在正規流程 (Track A) 中，任何試圖破壞合約的程式碼將被無情擊墜；而在觀察期 (Track B) 裡，防護機制則轉為「債務收集器」，為規格的最終收斂收集情報。

## 反思 (Reflection)

透過「讓 Level 1 保護 Level 2」的架構倒置，我們化解了政治性的規格爭議。它將人為判斷上的「誰對誰錯」，降溫為機器監控下的「待消解技術債」。開發團隊得以在一定的保護傘下平穩產出，同時確保系統的技術問題百分之百被顯性化並記錄在案。

我們必須扒下規格驅動開發的國王新衣，捫心自問：「我們嘔心瀝血撰寫的規格，真的具備阻擋劣質程式碼進入生產環境的物理能力嗎？」如果沒有執行期的強制力去守護它，它不過是對未來系統撒下的一張彌天大謊。

## 結論 (Conclusion)

Spec-Driven Development 從來都不是一種適配全生命週期的過程解決方案 (Process Solution)。它是在我們利用「準則驅動 (Guideline-Driven)」度過漫長黑夜、消解了海量的歷史包袱後，有幸抵達的「終局願景 (End-game Vision)」。

承認此點並不令人慚愧。在第一份具備編譯期絕對約束力的規格正式落地前，我們都只是依賴自動化準則與觀察性手段生存的倖存者。擁抱這種基於分層治理的現實主義，才是帶領團隊走向真正規格驅動彼岸的唯一路徑。
