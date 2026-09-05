# 壓縮保留了分數，是否保留了能力：剪枝、量化與蒸餾

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-09-06T02:58
**Model**: GPT-5.6 Sol
**Agent**: Codex VS Code extension 26.901.22334
**Source**: conversation

---

## 導言 (Introduction)

模型從訓練環境移到手機或邊緣裝置時，常需要減少記憶體與計算。剪枝、量化與知識蒸餾都可稱為壓縮，卻以不同方式建立近似模型。

若壓縮後平均準確率不變，人們容易宣稱能力完整保留。然而，平均值可能看不見決策邊界附近、罕見類別或校準程度的改變。

## 分析 (Analysis)

量化（quantization）把連續權重映射到離散格點。最簡單的均勻量化可寫成：

\[
Q_\Delta(w)=\Delta\operatorname{round}(w/\Delta).
\]

若沒有截斷且採最近格點，單一權重誤差滿足 \(|Q_\Delta(w)-w|\leq\Delta/2\)。輸出誤差仍會受輸入尺度、層數與非線性影響，不能把單一權重界直接當成整體模型界。

下列實驗比較原始線性分類器與粗量化版本。大多數樣本維持不變，靠近邊界的樣本卻可能翻轉。

```python
def quantize(value, step):
    return step * round(value / step)

weight, bias = 0.26, -0.13
q_weight = quantize(weight, 0.2)
q_bias = quantize(bias, 0.2)
samples = [0.1, 0.49, 0.51, 0.8, 2.0]

for x in samples:
    original = int(weight * x + bias >= 0)
    compressed = int(q_weight * x + q_bias >= 0)
    print(x, original, compressed,
          round(weight * x + bias, 3),
          round(q_weight * x + q_bias, 3))
```

這個翻轉不是權重自然退化，而是部署表示改變。若測試集很少包含低邊際樣本，整體準確率可能完全不動。

剪枝（pruning）將部分連接設為零，常以權重大小或重要性近似選擇。重新訓練可以讓剩餘參數補償，但被剪除方向是否影響罕見行為，仍取決於評測覆蓋。

知識蒸餾（knowledge distillation）則重新訓練較小的學生模型，使其匹配教師輸出。常見軟目標為：

\[
p_i^{(T)}=
\frac{\exp(z_i/T)}{\sum_j\exp(z_j/T)}.
\]

溫度 \(T\) 展開類別間的相對分數。學生學到的是教師行為的受限近似，不是把每個參數直接壓小；其失真來源與量化不同。

## 反思 (Reflection)

壓縮本身不是退化的同義詞。經過量化感知訓練或重新訓練，壓縮模型可能在指定基準上維持甚至改善表現。應被檢查的是能力分佈是否改變，而不是檔案大小是否變小。

同樣地，浮點模型也不是唯一真實模型。不同硬體核心、算子融合與數值精度都可能帶來差異；部署契約應包含可接受的輸出容差，而非假定逐位一致。

## 實務對比 (Practical Contrastive Examples)

錯誤做法是只在平衡測試集上比較 top-1 accuracy。壓縮可能集中傷害罕見類別、低對比影像或接近安全閾值的案例，平均值仍幾乎不動。

較好的做法會比較輸出差、決策翻轉率、校準與行為切片。還應在真正部署硬體上測量延遲和記憶體，因為理論位元數下降不保證系統端收益。

另一個錯誤是把蒸餾當成無損複製。學生容量、蒸餾資料與溫度共同限制可轉移行為；教師在資料外的反應通常沒有被完整指定。

## 結論 (Conclusion)

模型壓縮是一組受資源約束的近似程序。量化改變數值格點，剪枝移除連接，蒸餾重新估計較小模型；三者不能只因目的相似就視為同一機制。

壓縮是否削弱能力，必須以固定資料與推論契約比較行為。平均分數是起點，不是無損證明；越靠近決策邊界或越少見的案例，越需要獨立切片。

剪枝與訓練後量化的早期整合見 [Deep Compression](https://arxiv.org/abs/1510.00149)；整數推論見 [Quantization and Training of Neural Networks for Efficient Integer-Arithmetic-Only Inference](https://arxiv.org/abs/1712.05877)；蒸餾目標見 [Distilling the Knowledge in a Neural Network](https://arxiv.org/abs/1503.02531)。
