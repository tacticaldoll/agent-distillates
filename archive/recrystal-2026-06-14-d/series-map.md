# 結構與邊界的執行：批次系列地圖

**Date**: 2026-06-14
**性質**: 批次系列地圖（涵蓋本桶 2 個系列的邊界、角色與成群理由）
**閱讀導覽**: 見本桶 [guide.zh-TW.md](./guide.zh-TW.md)

## 群邊界與脊椎位置

本群處理「授權判斷與治理原則如何落成可檢查、可回放、可隔離的約束，否則只是文件」。在全集脊椎上，這是執行層：把前面三群的「應該由誰當家」收束成 deterministic pipeline、SSOT 與 sandbox 邊界——讓統計生成接到穩定、可審計、可隔離的工程與物理邊界。

## 收錄系列與角色

| 報告 | 核心問題 | 不可替代角色 | 邊界 |
| :--- | :--- | :--- | :--- |
| [架構約束與決定性管線](./architecture-constraints-deterministic-pipeline/report.zh-TW.md) | 如何把 agent 統計生成接到可驗證、可追蹤、可恢復的工程系統？ | 分類集中、絕對歸屬、決定性管線、多維 SSOT（code/spec/archive/production）與抵抗重構重力井。 | 不寫框架導入手冊，不把所有架構問題化約成文件或 prompt 問題。 |
| [Linux 權限與 Sandbox 模型](./linux-permission-sandbox-boundary/report.zh-TW.md) | 權限檢查如何從 process credentials、kernel object 與 daemon 授權，推導到最小權限與 sandbox 邊界？ | 主體/客體/能力/邊界自含詞彙，串起 permission denied 排查、Unix socket 授權、container root 與 Agent 執行安全。 | 不提供完整 hardening checklist，不取代發行版或 runtime 文件。 |

## 為何成群

架構約束從軟體層把權威邊界變成 SSOT、schema gate 與單向資料流；Linux/Sandbox 從作業系統層把它變成 credentials、capabilities、socket 授權與隔離邊界。兩者是同一個「把權威邊界落成強制執行」問題的兩個層面——一個在程式碼結構，一個在 OS 與 runtime。合讀才看出 sandbox 不是權限開關，而是可見世界、可用能力、可呼叫 syscall 與代執行責任的組合邊界，正如多維 SSOT 不是單一真相，而是多個真理維度的互制。

## 與相鄰群的分界

- **知識與意圖治理群**：治理入口與權威分層是上游聲明；本群把聲明落成可執行的結構與物理約束。
- **獨立件（人機協作）**：結構與邊界終究靠人設計、驗證與裁決；弧線在此交回給正交的人之底座。

## Metadata

```toml
[[reports]]
slug = "architecture-constraints-deterministic-pipeline"
title = "架構約束與決定性管線：從 SSOT 到多維治理迴路"
article_type = "Analytical Essay"
tags = ["AI 治理", "單一事實來源", "決定性管線", "領域驅動設計", "技術債"]

[[reports]]
slug = "linux-permission-sandbox-boundary"
title = "Linux 權限與 Sandbox 模型：從主體憑證到邊界組合"
article_type = "Analytical Essay"
tags = ["Linux 權限", "Sandbox", "最小權限", "主體", "客體"]
```
