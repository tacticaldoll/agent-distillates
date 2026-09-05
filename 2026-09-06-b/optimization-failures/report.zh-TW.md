# 能力為何沒有形成：梯度障礙、坍縮與最佳化失敗

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-09-06T02:58
**Model**: GPT-5.6 Sol
**Agent**: Codex VS Code extension 26.901.22334
**Source**: conversation

## 導言 (Introduction)

模型理論上能表示某種函數，不代表訓練一定能找到它。當損失停滯或輸出失去多樣性時，人們常說模型退化；但若能力從未形成，更準確的分類是取得失敗（acquisition failure）。

「坍縮」尤其容易製造錯誤統一。RNN 的梯度消失、GAN 的模式坍縮與 VAE 的後驗坍縮都可能降低能力，三者失去的對象卻不同。

## 分析 (Analysis)

遞迴式神經網路（recurrent neural network）的早期訊號需要穿過 Jacobian 連乘：

\[
\frac{\partial h_T}{\partial h_k}
=
\prod_{t=k+1}^{T}
\frac{\partial h_t}{\partial h_{t-1}}.
\]

若多數方向的有效尺度小於 1，早期時間步收到的梯度會指數衰減。這妨礙信用分配（credit assignment）：模型可能具備表示長程依賴的參數空間，最佳化卻難以取得那組參數。

下列標準 Python 實驗隔離連乘效果：

```python
for scale in (0.8, 0.95, 1.0, 1.05, 1.2):
    influence = scale ** 50
    print(scale, f"{influence:.6g}")
```

這個標量例子只證明重複相乘可以消失或爆炸。真實網路包含矩陣方向、非線性函數與門控路徑，因此不能用單一底數預測所有 RNN。

生成對抗網路（generative adversarial network）的模式坍縮失去的是分佈覆蓋。不同潛在輸入被映射到少數輸出模式；問題位於生成器與判別器的動態目標，不是時間梯度必然縮小。

變分自動編碼器（variational autoencoder）的後驗坍縮則失去潛在變數資訊。當近似後驗接近先驗，

\[
D_{\mathrm{KL}}(q_\phi(z\mid x)\|p(z))\approx 0,
\]

解碼器可能忽略 \(z\)。這個結果可能與解碼器能力、目標權重及推論網路落後有關，不能只由「KL 太強」一語定案。

三個現象可以共享上位分類「訓練未取得預期能力」，卻不共享低階病因。上位分類幫助整理症狀，修復時仍必須回到各自的狀態變數與目標函數。

## 反思 (Reflection)

最佳化失敗與能力退化的時間方向不同。前者描述從初始化出發沒有到達預期目標，後者通常暗示已通過基準的模型後來變差。若沒有保存訓練中間點，兩者在最終輸出上可能看起來一樣。

名稱也可能隱藏觀察尺度。GAN 產生幾張漂亮圖片，仍可能缺少整體覆蓋；VAE 重建良好，仍可能沒有使用潛在變數。單一品質樣本無法證明整個機制正常。

## 實務對比 (Practical Contrastive Examples)

錯誤做法是建立通用「collapse 修復包」，同時對 RNN、GAN 與 VAE 調低學習率。梯度爆炸可能受益於裁剪，但 GAN 的模式覆蓋與 VAE 的潛在資訊需要不同觀察量。

較好的診斷會針對失去的對象設計量測：RNN 看跨時間梯度範數，GAN 看分佈覆蓋，VAE 看潛在變數與輸入的資訊及 KL 使用量。共同的名稱只負責導航，不負責下藥。

另一個錯誤是只看最終損失。對抗訓練的兩方損失未必直接對應樣本品質；VAE 的總下界也可能掩蓋重建項與 KL 項之間的重分配。

## 結論 (Conclusion)

能力未形成首先是訓練路徑問題，而不是已取得能力的自然老化。架構定義可表示範圍，最佳化動態決定實際抵達的位置。

不同算法可以共享失效分類，但機制必須逐一證成。診斷時應問「哪個資訊通道、分佈範圍或潛在變數失去作用」，而不是把所有坍縮壓成一條定律。

RNN 梯度分析見 [On the Difficulty of Training Recurrent Neural Networks](https://arxiv.org/abs/1211.5063)；GAN 模式覆蓋見 [Unrolled Generative Adversarial Networks](https://arxiv.org/abs/1611.02163)；VAE 訓練動態見 [Lagging Inference Networks and Posterior Collapse in Variational Autoencoders](https://arxiv.org/abs/1901.05534)。
