# GAN 如何透過對抗學習生成：生成器、判別器與動態平衡

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-09-06T02:36
**Model**: GPT-5.6 Sol
**Agent**: Codex VS Code extension 26.901.22334
**Source**: conversation

---

## 導言 (Introduction)

生成對抗網路（generative adversarial network）不直接為每筆資料指定重建目標。它讓生成器產生樣本，再讓判別器分辨真實資料與生成資料，透過雙方競爭逼近資料分佈。

這種方法能在沒有顯式似然函數的情況下訓練生成器。代價是訓練目標不再是單一固定曲面，而會隨另一個網路的更新持續改變。

## 分析 (Analysis)

原始 GAN 的極小極大目標為：

$$
\min_G\max_D V(D,G)
=
\mathbb E_{x\sim p_{\mathrm{data}}}[\log D(x)]
+
\mathbb E_{z\sim p(z)}[\log(1-D(G(z)))].
$$

判別器 $D$ 希望提高真實樣本分數並降低生成樣本分數。生成器 $G$ 則改變生成分佈，使判別器更難區分。理想平衡下，生成分佈與資料分佈一致，判別器輸出二分之一。

實際訓練交替更新兩組參數。當判別器太強時，生成器可能收到缺乏辨識度的梯度；當生成器只找到少數能欺騙判別器的輸出時，它可能忽略其他資料模式。

下列玩具程式展示「只覆蓋一個模式」為何不能由平均值檢查發現：

```python
import numpy as np

rng = np.random.default_rng(4)
real = np.concatenate([
    rng.normal(-3, 0.3, 500),
    rng.normal(3, 0.3, 500),
])
collapsed = rng.normal(3, 0.3, 1000)

print("mean:", round(real.mean(), 3), round(collapsed.mean(), 3))
print("left-mode coverage:",
      round(np.mean(real < 0), 3),
      round(np.mean(collapsed < 0), 3))
```

這裡生成資料只保留右側模式。它展示模式坍縮（mode collapse）的輸出形狀，但沒有模擬 GAN 的梯度，因此不能用來證明任何特定訓練原因。

## 反思 (Reflection)

GAN 的生成器通常定義一個從簡單潛在分佈到資料空間的映射。若多個 $z$ 被送到相近輸出，生成器仍可能在判別器目前可見的弱點上得分，卻失去分佈覆蓋。

「對抗」也不表示雙方具有意圖。它只是兩個相依目標的最佳化結構。訓練不穩、模式坍縮與震盪是可觀察現象，但成因會隨損失、架構與更新比例改變。

## 實務對比 (Practical Contrastive Examples)

錯誤做法是挑選少量視覺效果最好的樣本，據此判定生成器已學會資料分佈。這只能顯示精度，不能顯示生成結果是否涵蓋所有模式。

較完整的評估會分開觀察品質與覆蓋率。對玩具分佈可以直接計算各模式比例；對高維資料則需要多種指標與人為檢查，且任何代理指標都有盲點。

另一個錯誤是把 GAN 的模式坍縮與其他模型中的 posterior collapse 或 model collapse 視為同一件事。它們共享「坍縮」名稱，卻涉及不同變數與訓練程序。

## 結論 (Conclusion)

GAN 透過判別器提供可學習的比較訊號，使生成器在沒有顯式資料似然的情況下逼近資料分佈。能力來自雙方相互調整，訓練困難也來自這個非定態目標。

理解 GAN 不能停在「兩個網路互相競爭」。真正需要追蹤的是生成分佈覆蓋了哪些資料模式，以及判別器梯度如何改變生成器的映射。

模式坍縮與展開最佳化的實驗可參考 [Unrolled Generative Adversarial Networks](https://arxiv.org/abs/1611.02163)。
