# 統計模型如何學習：批次導讀

本批報告從機器學習的共同骨架出發，逐步比較不同模型如何把資料轉換成參數與表示；每篇均可獨立閱讀，排列順序只反映概念由一般到具體的展開方式。

## 閱讀順序

1. [模型不是被編寫，而是被擬合](statistical-learning-foundations/report.zh-TW.md)（分析論文）：建立資料、假設空間、損失函數與泛化的共同語言。
2. [梯度如何塑造參數](gradient-descent-backprop/report.zh-TW.md)（分析論文）：說明反向傳播如何把局部誤差轉成各層參數更新。
3. [CNN 如何利用局部結構](cnn-local-structure/report.zh-TW.md)（分析論文）：觀察卷積如何把平移結構寫進模型。
4. [RNN 如何壓縮序列](rnn-gated-state/report.zh-TW.md)（分析論文）：追蹤共享參數、隱藏狀態與門控資訊流。
5. [Transformer 如何建立直接關聯](transformer-attention/report.zh-TW.md)（分析論文）：拆解縮放點積注意力與位置資訊。
6. [VAE 如何學習潛在空間](vae-latent-space/report.zh-TW.md)（分析論文）：以變分下界理解機率編碼與生成。
7. [GAN 如何透過對抗學習生成](gan-adversarial-learning/report.zh-TW.md)（分析論文）：以雙方目標理解隱式分佈學習。
8. [Diffusion 如何從噪聲生成](diffusion-denoising/report.zh-TW.md)（分析論文）：以逐步加噪與反向去噪理解生成程序。

## 跨報告連結

- [模型不是被編寫，而是被擬合](statistical-learning-foundations/report.zh-TW.md) → [梯度如何塑造參數](gradient-descent-backprop/report.zh-TW.md)（refinement：從統計目標深入參數更新）
- [梯度如何塑造參數](gradient-descent-backprop/report.zh-TW.md) → [CNN 如何利用局部結構](cnn-local-structure/report.zh-TW.md)（refinement：共同最佳化方法套用到局部結構）
- [梯度如何塑造參數](gradient-descent-backprop/report.zh-TW.md) → [RNN 如何壓縮序列](rnn-gated-state/report.zh-TW.md)（refinement：共同最佳化方法套用到時間共享參數）
- [RNN 如何壓縮序列](rnn-gated-state/report.zh-TW.md) ↔ [Transformer 如何建立直接關聯](transformer-attention/report.zh-TW.md)（facet：兩種序列關係建模方式）
- [VAE 如何學習潛在空間](vae-latent-space/report.zh-TW.md) ↔ [GAN 如何透過對抗學習生成](gan-adversarial-learning/report.zh-TW.md)（facet：顯式潛在推論與隱式生成分佈）
- [VAE 如何學習潛在空間](vae-latent-space/report.zh-TW.md) ↔ [Diffusion 如何從噪聲生成](diffusion-denoising/report.zh-TW.md)（facet：兩種以隨機變數建立生成程序的方法）
