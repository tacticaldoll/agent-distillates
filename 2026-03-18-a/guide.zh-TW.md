# 導讀

**Session**: 2026-03-18-a
**Reports**: 3

## 背景

一個多人維護的 Rust 改寫專案在導入 OpenSpec 工作流後，重新審視知識治理架構。從治理真空的診斷到三層模型的建立，過程中反覆在「理想設計」與「多作者現實」之間妥協，衍生出三個可獨立內化的主題。

## 閱讀順序

1. **Runtime 優先，共存為先** `Experience Report` — 治理根的發現過程：從工具中心化的誤判到 Claude Code first 三層模型，以及與多位開發者工作習慣的妥協
   ↓ 治理設計中的「不攔截」決策，引出知識熵管理問題
2. **排水口而非水壩** `Analytical Essay` — 非對抗式知識熵管理：為什麼不阻擋寫入而改用循環消解，以及收斂的數學條件
   ↓ 排水口模式中的「不提及」原則，引出更廣泛的注意力工程問題
3. **提及即引導，沉默即邊界** `Analytical Essay` — AI agent 的 context window 特性如何決定資訊架構設計：正面範圍、session 隔離、隱式指令

## 跨報告連結

- Report A §Discovery 第五階段 → Report B §Introduction（causal: 「不攔截 context/ 寫入」的決策直接催生了排水口模式的設計需求）
- Report A §Supplementary Knowledge 不對稱治理 ←→ Report B §Analysis 妥協的經濟學（facet: 同一個妥協事件的兩個觀察角度——治理力度的不對稱 vs 成本效益的計算）
- Report B §Analysis 收斂條件 → Report C §Analysis Session 作為硬邊界（refinement: 排水口模式依賴「觸發機制寫入永遠載入的指引」，Report C 深化解釋了為什麼永遠載入的指引具有更強的行為約束力）
- Report A §Discovery 第四階段 正面範圍 ←→ Report C §Analysis 提及即注入（facet: 同一個設計決策從治理視角和認知視角的分析）
