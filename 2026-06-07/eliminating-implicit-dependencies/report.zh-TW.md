# 消滅隱式依賴的架構課：從狀態追蹤到領域自洽

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-06-07T23:42
**Model**: Gemini 3.1 Pro
**Agent**: Antigravity IDE 2.0.4
**Source**: conversation

## 導言 (Introduction)

在自動化知識處理與發布管線（Publishing Pipeline）的演進歷程中，為了解決任務進度追蹤與動態元數據（Metadata）注入的迫切需求，系統架構往往會不知不覺地積累隱式依賴（Implicit Dependencies）。這些依賴最初看似無害的便利捷徑，但隨著時間推移與管線複雜度的陡增，它們會逐漸演變成全域變數的幽靈，成為系統擴展、自動化測試與除錯的嚴重阻礙。

本次架構反思旨在探討並解決管線設計中的兩大核心反模式（Anti-patterns）：第一，是過度依賴全域中介檔案（Global Index Files）所帶來的脆弱狀態同步問題；第二，是發佈排程腳本（Orchestrator Script）因承載過多業務邏輯而膨脹為難以測試的神物件（God Object）。這不僅是一次語法層面的簡單重構，更是重新確立系統單一真相來源（Single Source of Truth，SSOT）與落實領域驅動設計（Domain-Driven Design，DDD）嚴格邊界的關鍵重塑。

## 分析 (Analysis)

在傳統的管線實作中，我們觀察到兩個存在根本缺陷的設計決策，這兩個缺陷共同造就了高耦合的脆弱系統。

**缺陷一：雙重狀態同步（Dual State Synchronization）**
過去，系統為了掌握每一份生成任務的進度，選擇依賴獨立的狀態追蹤檔案（如 `session_records` 或 JSON 索引清單）。這不可避免地導致了真實檔案系統狀態與 JSON 紀錄之間的脫鉤（Desynchronization）。當實體檔案產生失敗、暫存目錄被開發者手動清理，或是跨越不同環境部署時，這種中介檔案便會成為錯誤狀態的溫床。它從根本上違反了 SSOT 原則，迫使開發者在排除故障時必須同時核對實體檔案與索引紀錄這兩個潛在衝突的真相來源。

**缺陷二：管線腳本的隱式狀態突變（Implicit State Mutation）**
早期的排程器承攬了壓倒性的領域職責。它不僅負責橫向的流程調度，還親自下場進行縱向的標籤萃取、設定檔解析、作者錨定以及遙測數據的過濾。更致命的是，排程器頻繁透過直接修改傳入的文件實體（Document Entity）來完成任務，產生了大量難以追蹤的副作用。物件被丟進一個巨大的黑箱中，出來時已被塞滿各式屬性，這使得單元測試變得幾乎不可能，且嚴重違反了單一職責原則（SRP）。

為了解決上述問題，我們透過架構圖來視覺化新舊設計的根本差異：

```mermaid
graph TD
    subgraph 舊架構：隱式突變與雙重狀態
        O[Orchestrator Script] -->|讀寫| I[(index.json 中介狀態)]
        O -->|隱式修改| D1[Document Entity]
        O -->|隱式修改| D1
        I -.->|狀態容易脫鉤| FS[(實體檔案系統)]
    end

    subgraph 新架構：顯式組裝與無狀態
        OS[Orchestrator Script] -->|鏈式傳遞依賴| B[Document Assembler]
        B -->|純函數構建| D2[Document Entity]
        D2 -->|產出| FS2[(實體檔案系統 / SSOT)]
    end
```

如上圖所示，架構的修正分為雙軌進行：首先，**果斷廢除所有中介追蹤檔案**，確立「實體目錄結構即資料庫」的無狀態（Stateless）設計。任務的完成與否，完全取決於輸出目錄中實體檔案的存在與否。其次，**導入建造者模式（Builder Pattern）**，建立專屬的文件組裝器（Document Assembler）。讓排程腳本退回它應有的本分——單純負責排程與依賴傳遞，而將「如何組裝一份合法文件」的領域知識，完全委託給自洽的領域模型。

## 反思 (Reflection)

廢除全域狀態追蹤，雖然在初期會讓開發者產生失去「全域掌控感」的錯覺，但這種短期的犧牲卻換來了系統韌性的大幅提升。當我們不再需要費心維護兩套平行的狀態時，許多極端的邊界狀況（Edge Cases）便迎刃而解。舉例來說，如果一個節點在處理過程中意外崩潰，無狀態的設計賦予了系統完美的冪等性（Idempotence）——我們只需重新執行指令，系統便能依據實體檔案的現狀無縫接續，而不會因為中介檔案殘留的髒數據（Dirty Data）而卡在無效狀態。

此外，引入領域組裝器後，排程腳本的程式碼行數通常能縮減 70% 以上，不僅可測試性與可讀性有了質的飛躍，更在模組間畫下了清晰的界線。然而，這種設計也伴隨著嚴格的紀律要求：未來任何元數據的新增或修改需求，都必須嚴格遵守領域邊界，不能圖一時方便直接在排程器中進行屬性賦值。這份「刻意的約束（Intentional Constraint）」，正是維持軟體架構純淨的必要代價。

## 實務對弄 (Practical Contrastive Examples)

為了更清晰地界定語意邊界並對抗抽象漂移，以下對比了兩種不同的架構實作方式。請注意排程器角色的轉變與屬性變更的清晰度。

**反面模式：隱式狀態突變（Implicit Mutation within Orchestrator）**
在這種設計中，排程腳本越俎代庖，直接對傳入的物件進行大量隱式修改。這使得排程器與文件結構產生了極高的強耦合，任何領域邏輯的變更都會直接波及排程層。

```python
# 錯誤示範：全能排程器中的元數據注入
def _inject_metadata(self, document_entity, processing_context, vocabulary_db):
    # 排程器親自執行數百行的字串解析、比對與陣列操作
    author_name = self._parse_global_config()
    normalized_tags = self._resolve_tags(processing_context, vocabulary_db)
    
    # 隱式的狀態突變，導致物件難以追蹤修改來源
    document_entity.metadata["author"] = author_name
    document_entity.metadata["tags"] = normalized_tags
```

**正確模式：顯式領域組裝（Explicit Domain Assembly with Builder）**
在這種設計中，排程器僅透過 Fluent API 傳遞所需的依賴與上下文。這種寫法不只是語法糖，而是一種「編譯期與執行期的防禦機制」，它強制要求所有屬性必須透過組裝器所開放的介面（Interface）進行顯式構建，意圖明確且毫無隱藏副作用。

```python
# 正確示範：排程器僅負責依賴傳遞與鏈式調用
document_entity = (DocumentAssembler(document_entity)
                   .with_base_meta(processing_context)
                   .with_author(global_config_path)
                   .with_vocabulary_tags(processing_context, vocabulary_db)
                   .with_telemetry(processing_context)
                   .build())
```

## 結論 (Conclusion)

本次的架構重塑揭示了一個通用的軟體工程原理：任何需要「暗中同步（Covert Synchronization）」或「越俎代庖（Overstepping Boundaries）」的設計，最終都會演變成難以償還的技術債。依賴倒置與單一真相來源法則（SSOT）並不僅僅是理論上的教條，而是對抗系統熵增的具體武器。

當我們讓實體目錄結構原原本本地反映任務真實狀態，並讓領域物件完全掌握自身的組裝邏輯時，系統便能獲得面對未來規模化擴展所需的絕對韌性與清晰度。讓排程歸排程，讓實體歸實體，堅守物件的領域自洽性，這正是領域驅動設計最堅實的防線。
