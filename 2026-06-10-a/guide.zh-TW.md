# 批次導讀：Linux 權限模型的完整閱讀弧

## 背景

這批結晶報告把 Linux 權限模型整理成一條由淺入深的閱讀路線：先理解 process 是誰，再理解它碰到什麼，接著學會排查錯誤，最後進入 daemon 與 container 的權限邊界。

## 閱讀順序

1. **一個 Process 到底是誰：Linux Credentials 的主體模型**  
   文體：分析論文。摘要：建立 process credentials 作為權限主體狀態的基礎模型。

2. **檔案、Socket 與 Kernel Object：權限檢查如何把主體接到客體**  
   文體：分析論文。摘要：把視角從 process 移到 file descriptor、filesystem path、Unix socket 與 daemon 入口。

3. **Permission Denied 不是一種錯：Linux 權限排查地圖**  
   文體：技術隨筆。摘要：把前兩篇模型轉成 `EACCES`、`EPERM`、socket 與 container 權限問題的排查流程。

4. **最小權限的現代形狀：從降權到 Sandbox 邊界**  
   文體：分析論文。摘要：把 credentials 與 object 權限提升為 daemon lifecycle、capabilities、namespace 與 seccomp 的設計原則。

5. **Unix Socket Daemon 的授權邊界：從入口權限到 Protocol 權限**  
   文體：分析論文。摘要：深挖本機 socket daemon 的四層授權：入口、peer credentials、protocol、daemon privilege。

6. **Container 權限模型：Root、Namespace 與 Host Object 的邊界**  
   文體：分析論文。摘要：說明 container root、namespace、capabilities、host mount、seccomp 與 LSM 如何共同構成部署邊界。

## 跨報告連結

- [一個 Process 到底是誰：Linux Credentials 的主體模型 分析] → [檔案、Socket 與 Kernel Object：權限檢查如何把主體接到客體 分析] (causal: process credentials 是主體，filesystem/socket 是主體要操作的客體。)
- [檔案、Socket 與 Kernel Object：權限檢查如何把主體接到客體 結論] → [Permission Denied 不是一種錯：Linux 權限排查地圖 調查] (refinement: 主體與客體模型被轉成可執行的排查順序。)
- [Permission Denied 不是一種錯：Linux 權限排查地圖 發現] → [最小權限的現代形狀：從降權到 Sandbox 邊界 分析] (causal: 能定位拒絕層之後，才能設計更窄的權限邊界。)
- [檔案、Socket 與 Kernel Object：權限檢查如何把主體接到客體 分析] → [Unix Socket Daemon 的授權邊界：從入口權限到 Protocol 權限 分析] (refinement: socket file 的入口模型被細化為 daemon 授權模型。)
- [最小權限的現代形狀：從降權到 Sandbox 邊界 反思] ←→ [Container 權限模型：Root、Namespace 與 Host Object 的邊界 反思] (facet: 兩者都處理最小權限，但一者偏 daemon lifecycle，一者偏部署隔離組合。)
