# 知識與意圖的治理：批次系列地圖

**Date**: 2026-06-14
**性質**: 批次系列地圖（涵蓋本桶 3 個系列的邊界、角色與成群理由）
**閱讀導覽**: 見本桶 [guide.zh-TW.md](./guide.zh-TW.md)

## 群邊界與脊椎位置

本群處理「承載權威的東西——知識、規格、詞彙——如何被治理到可信，並維持在能被正確消費、驗證與退場的位置」。在全集脊椎上，這是把「授權判斷」落到具體載體的治理層：權威要可驗證、可委派、可路由、可回滾，而不是靠集中或宣告。

## 收錄系列與角色

| 報告 | 核心問題 | 不可替代角色 | 邊界 |
| :--- | :--- | :--- | :--- |
| [AI 協作治理與知識架構](./ai-collaboration-governance-knowledge-architecture/report.zh-TW.md) | 治理文件如何避免變成權威漂移、入口膨脹與知識複製？ | 薄入口、可驗證權威、預設委派、知識路由與消解循環。 | 不替工具定義設定檔，不處理 CI / lint / spec 實作。 |
| [規格驅動開發與棕地治理](./brownfield-spec-driven-governance/report.zh-TW.md) | 棕地如何讓規格逐步成為可信契約，而非把觀察、願景或 LLM 輸出誤升格為權威？ | 規格債 → 觀察/契約雙層結構 → 差量流程 → 確定性工具與回饋迴路。 | 不寫導入手冊，不要求規格一次完成。 |
| [術語治理與可逆投影](./terminology-governance-reversible-projection/report.zh-TW.md) | 生成式術語如何避免從候選變成不可逆污染，並能事後安全策展、合併、降級與回滾？ | 術語生命週期、可逆投影、對稱剝除、角色/歸屬/時機治理。 | 不設計特定術語庫 schema，不提供完整 migration 工具。 |

## 為何成群

三者治理的是不同層級的「權威載體」：知識架構治理知識住哪層與如何路由；規格治理意圖如何成為可信契約；術語治理詞彙如何從候選成熟為共享真相。它們共用同一個因果：載體一旦被放錯層級或過早升格，就造成權威漂移與污染；解法都是讓載體回到能被驗證、委派、回滾的位置。合讀才看出它們是同一個「權威路由與成熟」問題的三個切面，而非三種獨立技巧。

## 與相鄰群的分界

- **信任與權威群**：建立授權判斷原則（可信由誰授權）；本群處理承載權威的載體如何被治理。
- **結構與邊界執行群**：把治理聲明落成 schema validation、CI gate、pipeline；本群只到治理入口與權威分層。

## Metadata

```toml
[[reports]]
slug = "ai-collaboration-governance-knowledge-architecture"
title = "AI 協作治理與知識架構：薄入口、可驗證權威與知識路由"
article_type = "Analytical Essay"
tags = ["AI 治理", "知識管理", "權威漂移", "知識萃取", "單一事實來源"]

[[reports]]
slug = "brownfield-spec-driven-governance"
title = "規格驅動開發與棕地治理：從觀察性 schema 到確定性契約"
article_type = "Analytical Essay"
tags = ["規格驅動開發", "棕地專案", "確定性邊界", "技術債", "AI 治理"]

[[reports]]
slug = "terminology-governance-reversible-projection"
title = "術語治理與可逆投影：讓延後命名不變成語意污染"
article_type = "Analytical Essay"
tags = ["術語管理", "可逆投影", "語意污染", "知識管理", "結構約束"]
```
