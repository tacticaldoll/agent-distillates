# 模型不是被編寫，而是被擬合：資料、假設空間與損失函數

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-09-06T02:36
**Model**: GPT-5.6 Sol
**Agent**: Codex VS Code extension 26.901.22334
**Source**: conversation

---

## 導言 (Introduction)

傳統程式通常由開發者直接寫出規則。機器學習（machine learning）則先定義可選函數的範圍，再以資料決定其中的參數。這個差異讓「模型學會了什麼」不能只靠閱讀程式碼回答。

模型也不是把資料原封不動存入參數。訓練是在有限的假設空間（hypothesis space）中尋找一個函數，使它在指定評分方式下表現較好。資料、函數形式與評分方式共同限定了所得能力。

## 分析 (Analysis)

給定訓練樣本 $D=\{(x_i,y_i)\}_{i=1}^{n}$，監督式學習常被寫成經驗風險最小化：

$$
\hat\theta=\arg\min_\theta
\frac{1}{n}\sum_{i=1}^{n}
\ell\!\left(f_\theta(x_i),y_i\right)
+\lambda R(\theta).
$$

其中 $f_\theta$ 是候選模型，$\ell$ 是損失函數（loss function），$R$ 是正則化項。這條式子沒有要求模型理解世界；它只要求參數在有限樣本與指定目標下得到較低數值。

真正關心的是母體風險：

$$
\mathcal R(\theta)=
\mathbb E_{(x,y)\sim P_{\mathrm{target}}}
[\ell(f_\theta(x),y)].
$$

訓練集只提供這個期望值的有限估計。若訓練分佈與目標分佈不同，降低經驗風險也未必改善部署表現。這就是泛化（generalization）問題的核心，而不是模型是否記住資料的心理描述。

下列最小實驗以一次多項式擬合展示同一批資料如何因假設空間不同而產生不同模型：

```python
import numpy as np

rng = np.random.default_rng(7)
x = np.linspace(-1, 1, 12)
y = x**2 + rng.normal(0, 0.08, size=x.shape)

for degree in (1, 2, 10):
    coef = np.polyfit(x, y, degree)
    train_mse = np.mean((np.polyval(coef, x) - y) ** 2)
    grid = np.linspace(-1.3, 1.3, 200)
    true_mse = np.mean((np.polyval(coef, grid) - grid**2) ** 2)
    print(degree, round(train_mse, 4), round(true_mse, 4))
```

十次多項式可能把訓練誤差壓得很低，卻在樣本外快速偏離 $x^2$。資料沒有單獨決定答案；模型容量與損失也參與了決定。

## 反思 (Reflection)

「模型由資料驅動」容易被誤解為資料包含答案。更準確的說法是，資料對候選函數施加選擇壓力，而架構先決定哪些函數有機會被選到。正則化與最佳化程序還會進一步改變實際結果。

這也表示參數不能被當成可逐條閱讀的規則表。即使訓練程式完全公開，特定參數為何形成仍取決於樣本次序、初始化與數值運算。可解釋性的困難來自整個估計過程，而不只是參數數量龐大。

## 實務對比 (Practical Contrastive Examples)

錯誤做法是只比較訓練誤差，然後宣稱誤差最低的模型最聰明。這忽略了資料外推、模型容量與目標分佈，也把評測結果錯當成固定能力。

較完整的做法會分離訓練、驗證與測試資料，並先說明部署分佈。若任務重視低頻案例，還要分群檢查誤差，而不是讓大量常見樣本主宰單一平均值。

這個對比說明：模型能力不是單一參數，也不是一個平均分數。它是模型在特定資料分佈、任務定義與度量方式下呈現的行為。

## 結論 (Conclusion)

機器學習的基本操作不是把智慧寫進程式，而是在假設空間中以有限資料估計參數。資料決定可觀察證據，架構限定可表示函數，損失函數定義何謂改善。

因此，任何模型能力主張都應附帶三個問題：它對哪個分佈成立、由哪個目標學得，以及用什麼指標驗證。離開這些條件，「模型學會了」只是一句缺少技術內容的概括。

主要理論脈絡可參考 Ian Goodfellow、Yoshua Bengio 與 Aaron Courville 的 [Machine Learning Basics](https://www.deeplearningbook.org/contents/ml.html)。
