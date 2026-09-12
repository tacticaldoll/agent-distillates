# 賣的不是模型：AI 商品由能力、服務與責任共同構成

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-09-06T03:14
**Model**: GPT-5.6 Sol
**Agent**: Codex VS Code extension 26.901.22334
**Source**: conversation

---

## 導言 (Introduction)

企業簽約購買「AI 助理」時，交付物看似是一個模型。實際付款換來的通常還包括 API、權限、監測、整合、支援，以及出錯後的處理程序。

只研究模型會高估演示能力與產品能力的等價程度。本文把 AI 商品視為一個社會技術系統（sociotechnical system）：數值模型只是其中一項元件，組織與基礎設施共同決定它能否可靠產生價值。

## 分析 (Analysis)

可以先用加總式列出供應側元件：

$$
\text{AI Product}=
\text{Model}+
\text{Serving}+
\text{Integration}+
\text{Human Work}+
\text{Governance}.
$$

這不是會計恆等式，而是盤點工具。模型產生輸出；服務層管理容量與延遲；整合層連接企業資料；人類處理例外；治理層則指定誰能部署、覆核與停止系統。

商品價值也不能由模型分數直接推出。對特定流程，可把每期實現價值簡化成：

$$
V=Delta Q+Delta L-C_{\mathrm{serve}}
-C_{\mathrm{integrate}}-C_{\mathrm{review}}-mathbb E[H].
$$

$\Delta Q$ 是品質改善，$\Delta L$ 是節省的勞動，$H$ 是錯誤造成的損害。各項必須換成一致單位才可計算；在尚未估價時，這條式子只能提醒分析者哪些成本不應消失。

責任流與資料流並不相同。下面的關係圖顯示模型輸出需要經過授權，才會成為外部行動。

```mermaid
flowchart LR
    D[企業資料] --> M[模型服務]
    M --> O[建議或內容]
    O --> H[人工或規則覆核]
    H --> A[組織行動]
    A --> E[外部效果]
    G[治理與責任] -.約束.-> M
    G -.指定.-> H
    G -.承擔.-> E
```

模型可以生成文字，卻不會自行取得付款、解僱或醫療處置權。行動權來自產品與組織設計；責任若在圖中沒有明確落點，不會因介面使用「助理」一詞而消失。

## 反思 (Reflection)

「統計黑盒」不是 AI 獨有。保險定價、信用評分與需求預測同樣處理不確定性。生成式 AI 的特殊張力在於輸出像完整的人類工作成果，模型元件因而容易代表整個產品。

反過來，也不能因產品需要大量人工便否定模型價值。許多成熟服務都依賴操作、支援與風險控制。問題不是有沒有人，而是這些人力是否被計入成本、品質與責任設計。

## 實務對比 (Practical Contrastive Examples)

錯誤做法是用離線模型準確率估算可替代職位數。這跳過工作分解、例外比例、審核時間與錯誤損害，也假定每個正確輸出都能直接轉成組織行動。

較好的做法先選定一條工作流程，記錄輸入、模型輸出、人工接手與最終結果。只有端到端時間、品質與風險改善，才構成產品效用證據。

另一個錯誤是把「human in the loop」當成責任答案。若覆核者沒有時間、權限或足夠資訊，人類只是在介面上替自動化背書，而非有效控制。

## 結論 (Conclusion)

AI 商品不是模型權重的別名。它是模型、服務、整合、人工與治理共同運作的系統；任一層失效，都可能讓漂亮的模型輸出無法成為可靠結果。

因此，採購與評估應從交付責任出發，而非從模型人格出發。真正可購買的不是抽象智慧，而是一套在特定條件下產生效用、處理例外並承擔後果的服務安排。

AI 系統的角色與責任配置可參考 [NIST AI Risk Management Framework 1.0](https://www.nist.gov/publications/artificial-intelligence-risk-management-framework-ai-rmf-10)；模型只占生產系統一部分的工程分析見 [Hidden Technical Debt in Machine Learning Systems](https://proceedings.neurips.cc/paper_files/paper/2015/hash/86df7dcfd896fcaf2674f757a2463eba-Abstract.html)。
