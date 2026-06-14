# 結構與邊界的執行

**Date**: 2026-06-14
**性質**: 批次導讀（本桶報告閱讀順序與跨報告關係）
**本群地圖**: [series-map.md](./series-map.md)

## 這一群的共同問題

授權判斷與治理原則最終要落成**可檢查、可回放、可隔離**的約束，否則只是文件。兩篇分別從軟體架構與作業系統兩個層面，把權威邊界變成結構與物理上的強制執行。

## 在脊椎上的角色

這是弧的執行層：把上游的「應該由誰當家」收束成 deterministic pipeline、SSOT 與 sandbox 邊界。（本群在全集中的定位見 [series-map.md](./series-map.md)。）

## 收錄報告與閱讀順序

1. [架構約束與決定性管線：從 SSOT 到多維治理迴路](./architecture-constraints-deterministic-pipeline/report.zh-TW.md)
   — 分類集中、絕對歸屬、決定性管線、多維 SSOT 與抵抗重構重力井。
   ↓ 軟體結構之外，執行最終落到作業系統的權限與隔離——
2. [Linux 權限與 Sandbox 模型：從主體憑證到邊界組合](./linux-permission-sandbox-boundary/report.zh-TW.md)
   — process credentials、kernel object、入口權限 vs 完整授權、最小權限與 sandbox 多層邊界組合。
