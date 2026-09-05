# VAE 如何學習潛在空間：機率編碼與變分推論

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-09-06T02:36
**Model**: GPT-5.6 Sol
**Agent**: Codex VS Code extension 26.901.22334
**Source**: conversation

## 導言 (Introduction)

自動編碼器可以把輸入壓縮成較小表示，再嘗試重建原資料。變分自動編碼器（variational autoencoder）不只產生一個編碼向量，而是學習給定輸入時潛在變數的機率分佈。

這使模型能從潛在空間取樣並生成資料，但也帶來兩個同時存在的要求：潛在變數要保留輸入資訊，所有輸入的編碼又必須接近可取樣的共同先驗。

## 分析 (Analysis)

VAE 假設生成過程為 \(p_\theta(x\mid z)p(z)\)。真實後驗 \(p_\theta(z\mid x)\) 通常難以直接計算，因此使用編碼器 \(q_\phi(z\mid x)\) 近似，並最大化證據下界（evidence lower bound）：

\[
\log p_\theta(x)
\ge
\mathbb E_{q_\phi(z\mid x)}
[\log p_\theta(x\mid z)]
-D_{\mathrm{KL}}\!\left(q_\phi(z\mid x)\|p(z)\right).
\]

第一項鼓勵重建，第二項限制編碼分佈不要離先驗太遠。兩者不是「正確與錯誤」的對立，而是可重建性與可取樣性的取捨。

若編碼器輸出 \(\mu(x)\) 與 \(\sigma(x)\)，直接抽樣會阻斷一般的梯度路徑。重參數技巧（reparameterization trick）改寫為：

\[
z=\mu(x)+\sigma(x)\odot\epsilon,
\qquad \epsilon\sim\mathcal N(0,I).
\]

隨機性被移到與參數無關的 \(\epsilon\)，使梯度可穿過 \(\mu\) 與 \(\sigma\)。

下列程式計算一維高斯後驗相對標準常態先驗的 KL 項：

```python
import numpy as np

def kl_standard_normal(mu, log_variance):
    return -0.5 * (1 + log_variance - mu**2 - np.exp(log_variance))

cases = [
    (0.0, 0.0),
    (1.0, 0.0),
    (0.0, np.log(0.25)),
]

for mu, log_var in cases:
    print(mu, round(np.exp(log_var), 3),
          round(kl_standard_normal(mu, log_var), 4))
```

當 \(\mu=0\) 且變異數為 1，近似後驗等於先驗，KL 為零。這是最低正則化成本，但若所有輸入都得到相同分佈，潛在變數便不再攜帶輸入差異。

## 反思 (Reflection)

VAE 的「潛在空間有意義」不是由連續性自動保證。意義取決於資料、解碼器能力、目標權重與評估方式；不同潛在方向未必對應人類可命名概念。

後驗坍縮（posterior collapse）也不應只怪罪 KL 項。研究指出，強解碼器、最佳化路徑、局部極小值與編碼表示缺乏差異都可能參與形成。名稱描述結果，不是完整病因。

## 實務對比 (Practical Contrastive Examples)

錯誤做法是只降低重建誤差，然後宣稱模型學到良好生成空間。普通自動編碼器可能把訓練樣本映射到彼此隔離的位置，使任意取樣落在沒有資料支持的區域。

另一個極端是過度強迫所有後驗貼近先驗。模型可能得到容易取樣的表面形式，解碼器卻忽略 \(z\)，生成結果主要依賴自身能力。

合理評估需要同時查看重建、取樣品質，以及潛在變數與輸入之間的資訊。單一 ELBO 數值未必能顯示哪一部分正在失效。

## 結論 (Conclusion)

VAE 把編碼改寫成近似機率推論，並以證據下界共同訓練編碼器與解碼器。它的生成能力來自重建項與分佈正則化之間的結構性張力。

重參數技巧解決的是隨機取樣下的梯度估計，不是語意保證。只有把目標函數、潛在資訊與生成結果一起觀察，才能說明模型實際學到什麼。

基本推導見 Kingma 與 Welling 的 [Auto-Encoding Variational Bayes](https://arxiv.org/abs/1312.6114)。
