# agent-distillates — agent 蒸餾物儲存庫

這個 repository 用來存放 agent 從對話、工作流程或思考過程中蒸餾出的自足產物。
目前主要內容是個人內化報告(crystallize 產物),也就是把一段成熟的理解整理成可回看、
可分享、但不直接參與執行的知識文件。

這裡的內容可以作為閱讀、學習、回顧與後續寫作的材料;但它們不是專案規則、不是執行
時依賴,也不應被任何專案的程式、設定或自動化流程當作穩定介面引用。

本 README 的目的很輕:提醒蒸餾物如何擺放,讓同一天、多語版本、以及有序系列都能維持
可預期的目錄結構。

## 結構總覽

```
agent-distillates/
├── README.md                         ← 本檔(目錄提醒)
├── <date>/                           ← 獨立報告的日期桶
│   └── <slug>/                       ← 每篇報告以 slug 隔離
│       ├── guide.<lang>.md           ← 各語言版本
│       └── guide.<lang>.md
├── <date>-a/                         ← 系列:第 1 篇(依 a→b→c 為閱讀順序)
│   └── <slug>/
│       └── guide.<lang>.md
├── <date>-b/                         ← 系列:第 2 篇
│   └── <slug>/ …
└── <date>-c/                         ← 系列:第 3 篇
    └── <slug>/ …
```

## 命名規則

| 層級 | 規則 |
|---|---|
| **日期桶 `<date>`** | ISO 格式 `YYYY-MM-DD`。**獨立**報告用裸日期。 |
| **系列後綴 `-a/-b/-c`** | 僅當多篇報告構成**有序系列**時使用,小寫字母依閱讀順序遞增接在日期後(`2026-06-12-a`、`-b`、`-c`)。 |
| **報告 `<slug>/`** | 一律以 slug 子目錄**隔離每一篇**報告。slug 為小寫 kebab-case、純 ASCII,由標題衍生,是報告的穩定識別;同篇的各語言版本**共用同一個 slug**。 |
| **檔名 `guide.<lang>.md`** | 固定 base name `guide` + `.<lang>` + `.md`。`<lang>` 用 BCP-47 標籤:`en`、`zh-TW`、`zh-CN`、`ja`…。**一律標註語言,不使用無標籤的 `guide.md`。** 同篇的不同語言並列在同一個 `<slug>/` 內。 |

## 獨立 vs 系列

- **獨立報告**:`<date>/<slug>/`。同一天若產出**多篇彼此獨立**的報告,共用一個
  `<date>/` 桶、各自一個 `<slug>/` 即可(slug 已足以隔離,不需後綴)。
- **系列**:當多篇是**刻意排序、需依序閱讀**的一組時,才用 `<date>-a/b/c` 為各
  桶定序,每桶內仍以 `<slug>/` 放該篇。

## Commit 風格

commit message 以簡短、可掃描為主,把重點放在蒸餾物的新增、修訂或歸檔調整。

- `docs: add <slug>`:新增一份蒸餾物。
- `docs: update <slug>`:修訂既有蒸餾物。
- `docs: add README intro`:更新儲存庫說明或目錄規範。
- `chore: reorganize structure`:只調整目錄或歸檔結構。

此 repository 以文件歸檔為主,通常不需要細分 `feat`、`fix`、`test` 等程式專案常見
類型。

## 實例

今日的獨立報告(雙語):

```
agent-distillates/2026-06-12/separating-by-role-owner-and-time/
├── guide.zh-TW.md
└── guide.en.md
```

假想的三篇有序系列:

```
agent-distillates/2026-06-12-a/<slug>/guide.en.md
agent-distillates/2026-06-12-b/<slug>/guide.en.md
agent-distillates/2026-06-12-c/<slug>/guide.en.md
```

## 備註

- 報告**內容格式**(標題、`Structure`/`Date`/`Source`/`Status` 表頭、章節)由
  crystallize 規範定義,不在本檔範圍。
- 報告語言以檔名 `<lang>` 標示;原始撰寫語言可在報告本文的來源/狀態欄另記。
