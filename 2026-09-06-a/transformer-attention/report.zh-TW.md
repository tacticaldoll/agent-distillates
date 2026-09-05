# Transformer 如何建立直接關聯：注意力、位置與平行計算

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-09-06T02:36
**Model**: GPT-5.6 Sol
**Agent**: Codex VS Code extension 26.901.22334
**Source**: conversation

---

## 導言 (Introduction)

RNN 讓資訊依時間步逐次傳遞。Transformer 則使用自我注意力（self-attention），讓一個位置直接聚合其他位置的表示。這縮短了位置間的計算路徑，也讓訓練可沿序列位置平行進行。

直接關聯常被敘述成模型不再遺忘。注意力實際產生的是輸入相依的加權和；它不保證每個位置都被保存，也不等於可持續存取的外部記憶體。

## 分析 (Analysis)

縮放點積注意力（scaled dot-product attention）寫成：

\[
\operatorname{Attention}(Q,K,V)
=
\operatorname{softmax}\!\left(
\frac{QK^\top}{\sqrt{d_k}}
\right)V.
\]

查詢 \(Q\) 與鍵 \(K\) 的點積產生關聯分數，Softmax 將每列轉為權重，再對值 \(V\) 加權。除以 \(\sqrt{d_k}\) 是為了控制高維點積的尺度，避免 Softmax 過早飽和。

下列程式展示同一組值如何因查詢改變而被重新混合：

```python
import numpy as np

def softmax(x):
    e = np.exp(x - np.max(x, axis=-1, keepdims=True))
    return e / e.sum(axis=-1, keepdims=True)

Q = np.array([[1., 0.], [0., 1.]])
K = np.array([[1., 0.], [0., 1.], [1., 1.]])
V = np.array([[10., 0.], [0., 10.], [5., 5.]])

weights = softmax(Q @ K.T / np.sqrt(K.shape[1]))
print(np.round(weights, 3))
print(np.round(weights @ V, 3))
```

輸出是值向量的加權組合，不是把全部輸入無損複製到每個位置。多頭注意力讓模型在不同投影空間形成多組關聯，但各頭仍受維度與訓練目標限制。

因為注意力本身沒有序列順序，Transformer 還需要位置編碼（positional encoding）。原始架構加入正弦與餘弦位置向量，使相同 token 位於不同位置時得到不同表示。

標準全注意力需要形成長度 \(N\) 的 \(N\times N\) 分數矩陣。它縮短關聯路徑，卻以記憶體與計算成本交換；實際成本還包含前饋層、批次大小與硬體利用率，不能只由 \(O(N^2)\) 判斷速度。

## 反思 (Reflection)

Transformer 的關鍵優勢是關聯路徑與平行性，不是無限記憶。有限上下文會排除窗口外資訊，窗口內資訊也會因查詢、遮罩與層間轉換受到選擇。

注意力權重也不能直接等同因果解釋。某個位置權重較高，只表示當次前向計算中的混合比例較大；殘差連接、前饋層與後續層仍會改變最終輸出。

## 實務對比 (Practical Contrastive Examples)

錯誤說法是「任意兩個 token 距離為一，所以模型不會忘記」。常數層數的關聯路徑只描述計算圖，不保證訓練能學到正確關係，也不保證資訊通過多層後仍可辨識。

另一個錯誤是把資料污染直接推導成注意力均勻化。資料能改變所學參數，但 Softmax 是否平坦取決於查詢與鍵的分數；沒有實驗就不能把兩者寫成必然因果。

較完整的分析會同時測量任務表現、注意力分佈、表示秩與上下文位置效應。任何單一內部指標都不足以代表「理解」或「記憶」。

## 結論 (Conclusion)

Transformer 以內容相依的權重直接混合序列位置，減少遞迴造成的計算限制。位置編碼補入順序，多頭投影擴充關聯視角，殘差與前饋層共同形成完整模型。

注意力提供的是可學習的資訊路由，不是人類式記憶。它讓某些關係更容易表示，也引入新的成本與邊界；這兩面都來自同一個架構選擇。

架構定義與原始實驗見 [Attention Is All You Need](https://arxiv.org/abs/1706.03762)。
