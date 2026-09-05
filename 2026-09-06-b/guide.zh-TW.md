# 模型能力如何失效：批次導讀

本批報告研究模型表現為何下降，以及這種下降是否真的來自模型本身。共同原則是先固定比較條件，再區分能力未形成、學到錯誤規律、更新干涉、近似損失與環境失配。每篇均可獨立閱讀；順序只呈現診斷問題由定義到驗證的展開。

## 閱讀順序

1. [表現下降不等於模型退化](capability-failure-framework/report.zh-TW.md)：建立參數、資料、推論設定與評測指標的診斷座標。
2. [能力為何沒有形成](optimization-failures/report.zh-TW.md)：區分梯度障礙與不同生成模型中的坍縮。
3. [模型為何學錯](spurious-learning/report.zh-TW.md)：分析資料雜訊、偽相關與捷徑學習。
4. [新知識為何覆蓋舊能力](catastrophic-forgetting/report.zh-TW.md)：以梯度干涉理解災難性遺忘。
5. [壓縮保留了分數，是否保留了能力](compression-loss/report.zh-TW.md)：比較剪枝、量化與知識蒸餾的近似損失。
6. [模型沒有改變，世界卻改變了](distribution-shift/report.zh-TW.md)：拆解分佈漂移、回饋迴圈與遞迴生成資料。
7. [如何證明能力真的退化](degradation-diagnosis/report.zh-TW.md)：把控制變因、行為切片與反事實比較組成診斷程序。

## 概念關係

診斷時可以把觀察到的表現寫成 (S(\theta,P,c,m))。四個輸入分別代表模型參數、資料分佈、推論設定與測量方法。其餘報告各自深入其中一種改變，但不假設所有失效都共享同一病因。

- [能力為何沒有形成](optimization-failures/report.zh-TW.md)與[模型為何學錯](spurious-learning/report.zh-TW.md)都可能在首次訓練後出現，前者關注可訓練性，後者關注被選中的規律。
- [新知識為何覆蓋舊能力](catastrophic-forgetting/report.zh-TW.md)與[壓縮保留了分數，是否保留了能力](compression-loss/report.zh-TW.md)都會改變模型，卻分別來自新目標與部署近似。
- [模型沒有改變，世界卻改變了](distribution-shift/report.zh-TW.md)提醒讀者，模型外部變化也能製造相同症狀。

因此，本批報告不採用「參數生命週期」作為統一理論。參數只是一個座標；資料、推論程序與評測契約同樣可能改變觀察結果。
