# 企業如何與 AI 共同生產價值：流程改造、人工補償與資料回流

<!-- front matter -->
**Structure**: Analytical Essay
**Date**: 2026-09-06T03:14
**Model**: GPT-5.6 Sol
**Agent**: Codex VS Code extension 26.901.22334
**Source**: conversation

---

## 導言 (Introduction)

同一套 AI 工具進入兩家公司，結果可能完全不同。差異不一定來自模型版本，而可能來自資料權限、流程拆分、教育訓練與例外處理。

因此，生產力不是模型單方面交付的固定屬性。它是技術與組織互補（organizational complementarity）的結果：模型提供某種能力，企業重新安排工作，兩者才形成可觀察產出。

## 分析 (Analysis)

最簡單的互補模型可寫成：

\[
Y=A\,q^\alpha o^{1-\alpha},
\qquad 0<\alpha<1,
\]

其中 \(q\) 表示模型在任務上的品質，\(o\) 表示流程適配程度。這是分析用生產函數，不是經驗上已確立的普遍定律；它只表達任一投入接近零時，另一項很難單獨實現全部價值。

下列實驗比較只改善模型與同時改善流程的結果：

```python
def output(model_quality, process_fit, alpha=0.5):
    return model_quality ** alpha * process_fit ** (1 - alpha)

cases = {
    "weak model, strong process": (0.4, 0.9),
    "strong model, weak process": (0.9, 0.4),
    "both aligned": (0.9, 0.9),
}

for name, values in cases.items():
    print(name, round(output(*values), 3))
```

乘法形式使短板可見。真實組織未必符合平方根關係，但任何生產力主張都應說明模型之外的互補投入。

人工補償（human compensation）是這些投入的一部分。員工會改寫提示、補找資料、核對答案、處理例外，甚至替模型輸出維持禮貌與責任。若研究只量測完成速度，這些新增工作可能被藏在「使用工具」之中。

自動化還可能留下最難的案例給人類。當日常案例被系統處理，操作員較少練習，卻要在異常時立即接手。這種自動化反諷不是生成式 AI 新發明，而是長期存在的人因工程問題。

資料回流讓共同生產更加明顯。人類接受、修改與拒絕輸出的紀錄可以改善產品；但這些紀錄也反映既有介面與管理政策，不是中立的「人類真實偏好」。

## 反思 (Reflection)

把流程改造全部算成 AI 效益會高估模型，把所有人工補償算成 AI 失敗則會低估系統。合理的分析單位是端到端工作，而不是只看模型或只看員工。

效果也可能在人群間高度異質。經驗較少的工作者可能更容易從建議中受益，專家則可能花更多時間核查低品質輸出。平均生產力不能直接代表每種技能與任務。

## 實務對比 (Practical Contrastive Examples)

錯誤做法是購買工具後要求所有員工立即使用，再以登入率當成功指標。登入只能證明接觸，不能證明工作品質或風險改善。

較好的做法先找出可逆、可覆核且錯誤成本較低的子任務，建立基線後分階段導入。除了速度，也量測重工、升級處理、員工學習與客戶結果。

另一個錯誤是把人工覆核標成臨時成本，假設模型升級後必然歸零。某些覆核來自責任制度與高風險例外，即使模型更準確也可能永久存在。

## 結論 (Conclusion)

AI 的實現價值由模型與組織共同生產。流程、技能、責任與資料回流不是部署後的雜項，而是產品能力得以轉成結果的必要條件。

因此，AI 是否有效不能只問模型進步多少。還要問企業改造了什麼、增加了哪些隱形工作，以及收益與風險由誰承擔。

生成式 AI 在特定客服場景中的異質生產力效果與研究邊界，見已發表於 QJE 的 [Generative AI at Work](https://academic.oup.com/qje/article/140/2/889/7990658)。自動化可能增加人類異常處理負擔的經典分析見 Bainbridge 的 [Ironies of Automation](https://www.sciencedirect.com/science/article/pii/0005109883900468)。
