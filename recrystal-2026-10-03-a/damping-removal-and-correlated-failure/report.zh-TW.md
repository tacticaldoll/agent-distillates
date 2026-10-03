# 把人和時間從決策迴路拿掉之後：選擇偏差、同步失效與不可逆的損失

**Structure**: Analytical Essay
**Date**: 2026-10-03T05:40
**Source**: notes
**Model**: Claude Opus 5.5
**Agent**: GitHub Copilot Chat v0.68.0

## 導言 (Introduction)

自動化的承諾之一，是把「訊號出現」到「採取行動」之間的距離縮到最短。估價模型算出房價，系統就直接向屋主發出收購要約；安全廠商發現新威脅，更新就在幾分鐘內推送到全球數百萬台電腦。中間原本存在的人工複核、分批上線與等待，被看成拖慢速度的摩擦力（Friction）。

本文要回答的問題是：當人與時間從「訊號到不可逆行動」的路徑上被移除時，系統失去的到底是什麼？在什麼情況下，那些被當成摩擦的步驟其實承擔著穩定或隔離的功能，在什麼情況下它們真的只是延遲？

這個問題對兩類讀者特別重要。一類是設計自動化決策系統的工程師與產品負責人，他們需要知道拿掉哪一步是安全的、拿掉哪一步會讓系統暴露在無法收回的損失下。另一類是核准這些設計的主管，他們需要分辨，一個主張「全面自動化」的提案，是在移除浪費，還是在移除保護。讀完本文，讀者應能對任何一個被提議移除的步驟問三個問題：它是否提供了決策模型以外的獨立資訊？它是否限制了同一個錯誤一次能影響的範圍？它擋下的損失是否可逆？

本文的論證分五步。第一步先修正一個常見的誤解：在回饋系統裡，延遲本身通常讓系統更不穩定，所以人工步驟的價值不在於「慢」。第二步說明人工步驟最重要的功能之一，是提供獨立資訊，以抵消選擇機制把無偏差的估計誤差變成有偏差的損失。第三步處理另一種功能：限制同一個錯誤同時到達的範圍。第四步說明為什麼對不可逆的損失，平均值會誤導決策。第五步區分兩種移除保護步驟的人，因為他們需要不同的對策。

證據分兩類。Zillow 的購屋業務、CrowdStrike 的全球當機、蘇伊士運河的阻塞與紅海海纜受損，取自公司申報文件、官方事故分析與可查證的報導；供應鏈與決策理論的機制取自學術文獻。文中三個數學模型都是本文設定，用來讓讀者檢查機制，不是對任何個案的重建。

## 分析 (Analysis)

### 一、先修正一個誤解：延遲通常不是保護

把人工步驟稱為「阻尼」，是一個直覺但容易誤導的比喻。在控制理論中，一個回饋迴路（Feedback Loop）的穩定性同時取決於增益（每次修正的幅度）與延遲（從觀察到修正之間的時間）。在其他條件不變時，增益越大、延遲越長，系統通常越容易來回震盪。延遲在這裡通常是壞事，雖然它的實際影響仍取決於系統與控制器的形式。

供應鏈研究提供了最直接的證據。牛鞭效應（Bullwhip Effect）指的是：零售端需求的小幅波動，沿著批發商、製造商、原料商往上游傳遞時，訂單的波動被逐級放大。Lee、Padmanabhan 與 Whang（1997）在〈供應鏈中的資訊扭曲：牛鞭效應〉中歸納出四個來源：需求訊號處理、短缺時的搶貨博弈、批量訂購與價格波動。Sterman（1989）在一個模擬啤酒供應鏈的實驗中發現，參與者對自己的決策如何回饋到環境「不敏感」，訂貨因而反覆偏離所需。Chen 等人（2000）則證明，即使把終端需求資訊集中分享給各層，波動放大也只能被減少，不能被完全消除。

這些研究共同指向一件事：讓資訊更快、更完整地傳遞，通常會減輕牛鞭效應，而不是加劇它。所以，一個常見的說法，認為自動化把傳遞延遲壓到接近零、因而造成超音速牛鞭效應（Supersonic Bullwhip Effect），把兩種不同的機制混在一起。快速傳遞本身不會放大波動；放大來自預測方法、前置時間與決策者對回饋的誤判。下文第三節會說明，CrowdStrike 事件裡真正起作用的是另一種機制：所有節點同時收到同一個錯誤。

那麼人工步驟的價值在哪裡？本文的回答是，被拿掉的步驟可能承擔三種彼此獨立的功能，而「慢」不是其中任何一種。這是本文的分類，不是完整的清單：

1. **獨立資訊**：複核者看到決策模型沒有看到的東西，因此能擋下模型的錯誤。
2. **範圍限制**：分批或分區處理，讓同一個錯誤一次只能影響一部分對象。
3. **緩衝**：庫存、備援與保留的現金，讓錯誤在造成不可逆損失前被吸收。

一個步驟若三者都不提供，就這三種功能而言它只是延遲；但它仍可能有本文未列的功能，例如讓人累積接手經驗，移除前仍要問它擋下過什麼。一個步驟只要提供其中一種，拿掉它就要同時說明那個功能改由什麼承擔。

### 二、獨立資訊：選擇如何把無偏差的錯誤變成有偏差的損失

設想一個估價模型平均而言完全準確：它對每間房子的估價，有時偏高、有時偏低，誤差的平均是零。公司依這個估價向屋主發出全現金收購要約，屋主可以接受或拒絕。

問題在於，接受與否是由屋主決定的，而屋主比模型更清楚自家房子的狀況：屋頂漏水、地基問題、鄰居的噪音。模型估高了，屋主傾向接受；模型估低了，屋主轉向公開市場。公司最後買到的，是模型高估的那些房子。這是逆向選擇（Adverse Selection）：資訊不對稱的一方，會挑選對自己有利的交易。Akerlof（1970）在〈檸檬市場〉中用二手車說明了這個機制如何讓市場上只剩品質差的商品，形成檸檬市場（Market for Lemons）。石油業稱它的近親為贏家詛咒：在競標中得標的，往往是最高估儲量的那一方（Capen, Clapp, & Campbell, 1971）。

這個機制有一個違反直覺的結論：模型的平均誤差為零，不保證成交的平均誤差為零。下面的模型把它寫成可以手算的形式。本文設定：每間房子的真實價值都是 100；模型誤差等機率地取 −20、−10、0、10、20 之一，平均為零；屋主知道真實價值，只有在要約高於 100 時才賣。再加入一位勘驗者，他對真實價值也有一個估計，誤差取自同一組數值，所以他和模型一樣準，不比模型更準。勘驗規則是：要約高於勘驗者的估計，就否決。

為了分辨勘驗的價值來自哪裡，模型比較三種勘驗者。獨立的勘驗者有自己的誤差，與模型的誤差無關；附和的勘驗者只看模型的數字，所以他的估計就是模型的估計；盲目的勘驗者否決的比例相近，但否決與否和要約高低無關。比較的指標是「成交時平均多付多少」，這樣可以排除「交易變少，損失自然變少」的效果。

```python
# 本文設定：數值皆為設想，用來檢查選擇機制，不重建任何公司的資料
from itertools import product

TRUE_VALUE = 100.0
ERRORS = (-20, -10, 0, 10, 20)   # 模型與勘驗者的誤差取自同一組：兩者一樣準，平均都為零

def executed_overpay(markup=0.0, veto=None):
    """每個 (模型誤差, 勘驗誤差) 組合等機率；回傳成交交易的多付金額。"""
    paid = []
    for model_err, inspect_err in product(ERRORS, ERRORS):
        offer = TRUE_VALUE + model_err + markup
        if veto is not None and veto(offer, model_err, inspect_err):
            continue
        if offer > TRUE_VALUE:   # 屋主只在要約高於真值時賣
            paid.append(offer - TRUE_VALUE)
    return paid

def mean(xs):
    return sum(xs) / len(xs)

DECISIONS = len(ERRORS) ** 2
independent = lambda offer, m, h: offer > TRUE_VALUE + h   # 用自己的估計
echoing     = lambda offer, m, h: offer > TRUE_VALUE + m   # 只看模型的數字
blind       = lambda offer, m, h: h <= 0                   # 否決與要約無關

base = executed_overpay()
assert sum(ERRORS) == 0 and mean(base) > 0                         # 無偏模型，成交時卻多付
assert mean(executed_overpay(veto=independent)) < mean(base)       # 獨立資訊降低成交時的多付
assert mean(executed_overpay(veto=echoing)) == mean(base)          # 附和者不提供資訊
assert mean(executed_overpay(veto=blind)) == mean(base)            # 只是少做交易，不改善成交品質
assert sum(executed_overpay(markup=5)) / DECISIONS > sum(base) / DECISIONS   # 調高出價，每次決策的多付增加
```

依所列設定可以手算。沒有勘驗時，只有模型誤差為 10 與 20 的情境成交，成交時平均多付 $(10 + 20) / 2 = 15$。獨立的勘驗者會否決大部分高估的要約：誤差 10 的要約只在勘驗者自己也高估 10 以上時通過（5 次中 2 次），誤差 20 的要約只在勘驗者也高估 20 時通過（5 次中 1 次），成交時平均多付降為 $(2 \times 10 + 1 \times 20) / 3 \approx 13.3$。附和的勘驗者永遠不否決，平均仍是 15；盲目的勘驗者少做了一些交易，但被留下的交易與原來一樣糟，平均也是 15。把所有要約一律調高 5，成交時的平均多付不變，但成交的情境變多，每次決策平均多付從 $150 / 25 = 6$ 升為 $225 / 25 = 9$。

這個模型能檢查的規則是：在賣方掌握更多資訊時，買方的成交樣本被選擇過，模型的平均準確度不能代表成交的平均結果；一個與模型誤差無關的估計，即使不比模型更準，也能抵消一部分選擇偏差，而只看模型數字的複核做不到這件事。讀者若把勘驗者的誤差改成與模型誤差相同，第二個斷言就會失敗。模型不能說明真實市場的誤差大小，也忽略了轉售價差、交易成本與屋主並非完全知情等因素，所以它的「多付」不等於真實的虧損。

#### 個案：Zillow 的購屋業務

Zillow 原本是房地產資訊平台，它的自動估價「Zestimate」是供買賣雙方參考的資訊產品。2018 年它開始直接收購房屋、整修後轉售。2021 年 2 月，Zillow 宣布在 23 個市場中，Zestimate 將成為符合資格房屋的「初始現金要約」（Zillow, 2021）。這是把估價模型的輸出更直接地接到要約流程上的一步：它是初始要約，公告也說明要約以房屋資訊正確為前提，正式成交前仍有後續程序。

2021 年 11 月 2 日，Zillow 宣布結束這項業務。依其向 SEC 提交的股東信，第三季它購入 9,680 間房屋，季末庫存 9,790 間，另有 8,172 間已簽約待交屋；第三季提列 3.044 億美元的存貨減損，並預期第四季再認列 2.4 億至 2.65 億美元的相關損失；公司將裁減約 25% 的人力（Barton & Parker, 2021）。執行長 Rich Barton 的說明是：「我們判定，預測房價的不可預測性遠超過我們的預期」（Zillow Group, 2021）。消息公布次日，股價下跌約 25%（Levy, 2021）。

業務收尾前的內部做法，有兩種程度不同的報導。《商業內幕》依內部人士說法報導，一項名為「番茄醬計畫」（Project Ketchup）的內部方案為了提高競爭力，減少要約中扣除的整修費用（Nicoll & Geiger, 2021）。一份 2024 年發表在資訊系統教育期刊的教學個案進一步寫道，該方案「阻止定價專家修改演算法的房屋估值」（Gudigantala & Mehrotra, 2024）。後一個說法目前只見於該教學個案，因此只當作教學個案的陳述引用。

這個個案能支持什麼，要說清楚。Zillow 自己把失敗歸因於未來售價預測的不確定性，並在財報中另列整修與轉售產能的限制（Zillow Group, 2021）；本文第二節的選擇機制是另一種可能的貢獻因素，它與公開事實一致（模型輸出直接成為要約、人工調整的空間據報被縮小、成交房屋大量積壓），但 Zillow 的揭露並沒有證明逆向選擇是主因。股東在 2021 年 11 月提起證券集體訴訟，法院在 2022 年 12 月部分駁回、部分准許被告的撤銷請求（Stanford Securities Class Action Clearinghouse, n.d.）；這是程序上的進展，不是對事實或法律責任的最終認定。

#### 預測改變它要預測的對象

獨立資訊還有另一個用處。選擇偏差處理的是「誰會接受要約」，但當一個模型的輸出被用來做大量決策時，這些決策本身也會改變市場：一個大量收購的買家抬高或壓低某個地區的成交價，其他買賣雙方也會調整行為，而這些改變後的成交又會成為模型下一輪的訓練資料。Perdomo 等人（2020）在〈執行性預測〉中把這類情形形式化：「當預測被用來支持決策，它們可能影響自己要預測的結果。」在這種情況下，模型在歷史資料上表現良好，不代表它在自己參與塑造的市場上仍然準確。把這個機制套到 Zillow 是本文推論，所引資料沒有量測它的購屋對當地房價的影響。它對人工步驟的含義是：一個不依賴模型資料的現場判斷，可以在模型開始「學到自己的影響」時提早發出訊號。

### 三、範圍限制：同一個錯誤同時到達每一個節點

第二種功能與資訊無關，與範圍有關。設想一個錯誤不論被誰發現都會造成同樣的損害，那麼保護系統的方法就是讓它一次只能碰到一部分對象，在擴散前被看見。

2024 年 7 月 19 日協調世界時 04:09，資安廠商 CrowdStrike 向其 Windows 端點感測器推送了一份內容設定檔（Channel File 291），並在 05:27 撤回。依該公司的事後分析，這份設定檔所用的範本定義了 21 個輸入欄位，但感測器程式碼只提供了 20 個值；當設定檔首次用到第 21 個欄位時，感測器發生越界讀取而當機。用來驗證內容的工具本身也有邏輯錯誤，沒有攔下這個不一致（CrowdStrike, 2024a）。微軟估計受影響的 Windows 裝置約 850 萬台，不到全部 Windows 電腦的 1%（Weston, 2024）。

這不到 1% 的裝置集中在航空、醫療與金融。達美航空估計損失約 5 億美元（Associated Press, 2024），五天內取消約 7,000 個航班（Yousif, 2024）；英國多家家庭醫師診所使用的 EMIS 病歷系統受到影響（Edser, Armstrong, & Phillips, 2024），倫敦證券交易所集團的新聞發布服務中斷，交易所本身照常運作（Atkinson, 2024）。由於感測器在開機早期就當機，許多機器需要依廠商的處置指引人工逐台處理（CrowdStrike, 2024b）。

這起事件中被拿掉的不是人工複核，而是範圍限制。CrowdStrike 的客戶可以設定感測器程式版本落後最新版一到兩版，但這個設定不適用於這類快速回應的內容更新；事後該公司承諾，此類內容將先做金絲雀式的小範圍部署，再分階段擴大，並讓客戶能決定在何時、何處部署（CrowdStrike, 2024a, 2024b）。

這類失效的名稱是共模故障（Common-Mode Failure）：多個原本被視為彼此獨立的元件，因為共用同一個原因而同時失效。冗餘設計在這裡失去作用，因為每一份備援都裝了同一個感測器、收到同一份設定檔。描述這種損失範圍的工程術語是爆炸半徑（Blast Radius）：一次錯誤最多能影響多少對象。分階段部署做的事情很簡單。設想把全部裝置分成互不重疊的批次，比例依序為 $c_1, c_2, \dots$（$c_i \ge 0$，總和為 1），錯誤在第 $k$ 批之後被偵測並停止後續部署，那麼收到錯誤內容的比例就是 $c_1 + \dots + c_k$，而不是 1。實際失效的比例不會超過這個數；若錯誤只在部分機器上觸發，實際失效會更少。只要第一批的錯誤能在第二批開始前被看見，爆炸半徑就被限制在 $c_1$。這個式子的前提是錯誤在第一批就會顯現，而且不會在批次之間傳播；若錯誤只在特定條件下觸發，分批的保護會打折扣。

共模故障也可能藏在看似獨立的備援裡。依本文推論，分在不同機房或不同區域的系統，若共用同一套身分驗證、同一套網域解析或同一套管理控制面，那個共用元件就是它們共同的失效原因；自動擴充機制若在一個區域故障時同時向其他區域要資源，也可能把局部故障擴散出去。檢查備援是否獨立，要看的是它們共用什麼，而不是它們分在幾個地方。要看出共用什麼，前提是依賴關係有紀錄；軟體物料清單（SBOM）就是為這個目的發展的做法，美國電信資訊管理局在 2021 年提出它的最低要素，用來提高軟體供應鏈的透明度（National Telecommunications and Information Administration, 2021）。一份依賴清單不會告訴你哪個依賴會失效。依本文推論，若各系統的清單能彙整在一起，「哪些系統共用同一個元件、因而可能同時失效」就成為可以查詢的問題。

同樣的結構出現在實體供應鏈。2021 年 3 月 23 日至 29 日，貨櫃輪長賜號在蘇伊士運河擱淺六天，每天約有 96 億美元的貨物受阻（Russon, 2021）。2024 年 2 月，紅海多條海底電纜受損，香港環球全域電訊表示其中四條被切斷，估計影響約 25% 的相關流量（HGC Global Communications, 2024）。半導體先進製程更集中：依 2021 年的產業報告，全球 10 奈米以下的晶圓產能，92% 在台灣、8% 在南韓（Semiconductor Industry Association & Boston Consulting Group, 2021）；極紫外光微影設備只有 ASML 一家供應（ASML, n.d.）。社會學家 Perrow（1999）把這類系統描述為「緊耦合」：一個部分的失效沒有時間或空間被隔離，就傳到下一個部分。

監管開始回應這個問題，但方式與常見的想像不同。歐盟《數位營運韌性法案》（Digital Operational Resilience Act）自 2025 年 1 月 17 日起適用於金融業，要求金融機構評估對資通訊第三方服務的集中度風險（Concentration Risk），對關鍵第三方服務商建立監督架構，並要求特定機構定期進行威脅導向的滲透測試（European Parliament & Council, 2022）。它要求的是評估替代可能性與依賴關係，並沒有規定任何單一供應商的市占上限。

### 四、不可逆：為什麼平均值會誤導

第三種功能是緩衝，它與損失能否收回有關。決策理論常用期望值比較選項，但期望值是對許多平行情境的平均。對一家只能活一次的公司，重要的是它沿著時間走過的那一條路徑。

物理學家 Ole Peters（2019）用一個擲硬幣的賭局說明兩者的差別。每一回合，正面財富增加 50%，反面減少 40%。對許多人同時下注的平均（系綜平均）而言，每回合的期望倍數是 $0.5 \times 1.5 + 0.5 \times 0.6 = 1.05$，看起來每回合賺 5%。但對單一個人連續下注而言，長期的每回合倍數是 $\sqrt{1.5 \times 0.6} \approx 0.949$，每回合約虧 5.1%。系綜平均與時間平均不相等的系統，稱為不具備各態歷經性（Ergodicity）。

```python
# Peters (2019) 的賭局：獨立、公平的擲幣；正面 ×1.5，反面 ×0.6
UP, DOWN = 1.5, 0.6
ensemble_multiplier = 0.5 * UP + 0.5 * DOWN
time_multiplier = (UP * DOWN) ** 0.5

assert abs(ensemble_multiplier - 1.05) < 1e-12
assert abs(time_multiplier - 0.9 ** 0.5) < 1e-12 and time_multiplier < 1.0

wealth = 1.0
for _ in range(50):          # 100 回合，正反各 50 次；順序不影響結果
    wealth *= UP * DOWN
assert abs(wealth / 0.9 ** 50 - 1) < 1e-9   # 終值就是 0.9 的 50 次方
assert 1.05 ** 100 > 100                     # 同樣 100 回合的系綜期望值則超過起始的 100 倍
```

依公式可以手算：100 回合正反各半之後，財富是起始的 $0.9^{50}$，約為 0.5%；同樣 100 回合的系綜期望值則是 $1.05^{100}$，超過起始的 100 倍。兩者的差距來自少數極幸運的路徑拉高了平均，而典型的路徑幾乎歸零。正反各半是大量回合後的典型情形，不是唯一可能的路徑。若再加上一條規則，財富低於某個門檻就出局（例如公司破產、無法再融資），那麼這個門檻就是吸收壁（Absorbing Barrier）：一旦觸及，後面再好的期望值都與你無關。

這個模型與自動化決策的關係在於規模。依本文推論，一個人工逐筆核准的流程，每天能承擔的部位有上限；把模型輸出直接接到資產負債表上，部位可以在幾個月內累積到公司承受不起的規模。Zillow 第三季購入近萬間房屋，是一個部位迅速擴大的例子；擴大有多少來自自動化，多少來自資金、成長目標或出價政策，所引資料無法區分。依本節的邏輯，評估這種系統時要問的不只是「平均每筆交易是否賺錢」，還有「最壞的一段路徑會不會先碰到吸收壁」。

### 五、兩種拆除者，兩種對策

前面三節說明了被拿掉的步驟承擔什麼功能。還有一個問題：為什麼有人會拿掉它們？本文設定兩種理想型來區分，因為它們需要不同的對策；現實中的決策者可能兩者兼有，也可能出於其他動機。

第一種是清醒的提取者：他知道這些步驟有保護作用，但保護的價值不由他承擔，拿掉它們能讓他在後果浮現前兌現收益。對付他的是誘因設計：延長報酬兌現期、讓追回機制涵蓋這類損失。

第二種是真誠的信任者：他真心相信模型比人更準確，把人工步驟看成可以清除的雜訊，而且他不會在出事前離開，會與系統一起承受後果。對付他的不是誘因，因為他的誘因已經與公司一致；需要的是讓他的信念接受檢驗。

討論這類決策者時，常有人引用鄧寧與克魯格的研究，用達克效應（Dunning-Kruger Effect）解釋高層的過度自信。這個引用要謹慎。Kruger 與 Dunning（1999）的四項研究測試的是幽默感、文法與邏輯推理，發現表現最差的四分之一受試者大幅高估自己的相對排名；後續研究指出，這個圖形有一部分來自測量雜訊與作圖方式造成的統計假象（Nuhfer et al., 2016; Gignac & Zajenkowski, 2020）。它不能用來診斷任何一位經理人。

比較有用的概念是校準（Calibration）：一個人或模型宣稱的信心，與它實際的準確率是否相符。校準可以被檢查。對信任模型的決策者，組織能要求的是：模型在它沒見過的時期（例如利率轉向、需求反轉）的表現如何？它的誤差在成交樣本與全部樣本上是否一樣？它說有 90% 把握的預測，事後有幾成是對的？這些問題不需要判斷任何人的心理狀態，只需要資料。

至於保留人工步驟本身，Bainbridge（1983）在〈自動化的反諷〉中指出一個兩難：自動化越先進，人在少數接手時刻的貢獻就越關鍵，但平時被自動化接管，人也失去了累積接手能力的機會。一個名義上保留、但複核時間只夠點擊核准的人工步驟，不提供第二節所說的獨立資訊；它就是那個模型裡「附和的勘驗者」。本文根據這個兩難與第二節的模型提出一個設計要求：要讓人工步驟承擔功能，至少要有三個條件，複核者看得到模型看不到的資訊，有否決的權限，而且否決與核准在事後被追究的方式是對稱的。這是本文建議，Bainbridge 的論文並未列出這三個條件。

## 反思 (Reflection)

**人也會錯，而且未必比模型少錯。** 本文沒有主張人工判斷比模型準確。在第二節的模型裡，勘驗的價值來自它的誤差與模型的誤差無關，而不是勘驗者比模型更準。如果人的判斷與模型高度相關（例如複核者主要參考模型的分數），或人的誤差本身就很大，保留人工步驟的效果會大打折扣。贏家詛咒最早是在人類競標者身上被記錄的。

**人工步驟也可能正是延遲來源。** 第一節指出延遲會讓回饋系統更不穩定。如果一個人工核准步驟讓補貨決策晚了兩週，它可能加劇牛鞭效應。判斷的依據是功能清單：這一步提供的是獨立資訊、範圍限制還是緩衝？三者都沒有，它就是自動化或移除的候選；但在動手前，仍要確認它沒有承擔本文未列的功能。

**緩衝有成本，冗餘不是越多越好。** 備援供應商、安全庫存與保留現金都要花錢，而它們保護的事件可能很多年都不發生。本文沒有給出一個適當的緩衝水準，因為那取決於損失的大小與可逆程度。第四節的模型只說明：當損失不可逆時，若只看逐筆的期望收益、不計入破產與路徑依賴，就會低估緩衝的價值。

**分批不是萬靈丹。** 分階段部署的保護，前提是錯誤在早期批次中就會顯現。如果錯誤只在某個日期、某種設定或某種負載下觸發，它可能安然通過所有批次，然後同時爆發。範圍限制要搭配多樣性：不同批次在不同條件下運行，才更可能提早暴露錯誤。

**個案的歸因有限。** Zillow 與 CrowdStrike 被用來說明機制，但兩起事件都有多重原因。Zillow 面對的是一個罕見的房價波動時期；CrowdStrike 的錯誤是一個具體的程式缺陷。本文的機制解釋了為什麼這類錯誤會被放大，不解釋錯誤本身為什麼發生。

## 實務對比 (Practical Contrastive Examples)

下表列出五個常被提議「全面自動化」的步驟。對每一個步驟，表格依本文的三種功能，指出拿掉它會失去什麼，以及哪一種替代做法可以保留功能而不保留延遲。替代做法一欄是本文建議。

| 被提議移除的步驟 | 它承擔的功能 | 直接移除的後果 | 保留功能、減少延遲的替代（本文建議） |
| :--- | :--- | :--- | :--- |
| 收購前的現場勘驗 | 獨立資訊 | 成交樣本被賣方挑選，平均多付 | 依模型不確定度分流：低不確定度直接成交，高不確定度才勘驗；勘驗者看不到模型分數 |
| 設定檔的分批推送 | 範圍限制 | 一個錯誤同時到達全部節點 | 自動化的金絲雀部署與自動回滾；讓客戶決定部署時間 |
| 第二供應商與安全庫存 | 緩衝與範圍限制 | 單點中斷即停線 | 依替代所需時間決定庫存水位；定期演練切換 |
| 交易金額的人工核准上限 | 緩衝 | 部位隨模型擴張，觸及吸收壁 | 自動化的部位上限，以最壞路徑而非期望值設定；交易頻率或集中度異常時自動暫停，等待人工覆核 |
| 跨機房、跨區域的備援 | 範圍限制 | 共用的身分驗證、網域解析或控制面一旦失效，所有備援同時失效 | 列出備援之間共用的元件與依賴清單；保留不經主要控制面的復原路徑；關鍵功能在與外部服務斷線時仍能有限度運作 |

這張表的限制在於，每一列的替代做法都假設功能可以被拆開來保留。實務上，一個人工步驟常常同時承擔多種功能，也會有表上沒列的功能，例如讓第一線人員累積判斷經驗。移除前，最可靠的做法仍是逐一問：這一步擋下過什麼？

## 結論 (Conclusion)

把人與時間從決策迴路中拿掉，失去的不是「慢」。延遲本身在回饋系統中通常是壞事，加速資訊傳遞往往讓系統更穩定。被拿掉的步驟真正可能承擔的，是三種與速度無關的功能：提供決策模型以外的獨立資訊，限制同一個錯誤一次能到達的範圍，以及在損失變成不可逆之前吸收它。

這三種功能各自對應一種失效。缺少獨立資訊時，選擇機制會把模型無偏差的誤差變成有偏差的損失，在賣方比買方更清楚商品品質的市場尤其如此。缺少範圍限制時，冗餘失去作用，因為所有備援同時收到同一個錯誤。缺少緩衝時，期望值為正的策略仍可能沿著單一路徑走向無法回頭的損失。

判斷一個步驟能不能拿掉，問的是它承擔哪一種功能，以及拿掉之後那個功能由什麼接手。對推動移除的人，對策也分兩種：清醒的提取者要用誘因約束，真誠的信任者要用校準的證據檢驗。兩者都不需要猜測任何人的心理，只需要追問系統在它沒見過的情境下會怎樣。

## 參考文獻 (References)

文中以（作者, 年份）標示出處，條目依作者字母排序。讀取日期為 2026-10-03。三個程式模型與表中替代做法為本文設定或建議，不是實測。標示「引用範圍」者，表示本文只引用該來源的指定部分。

- Akerlof, G. A. (1970). The market for "lemons": Quality uncertainty and the market mechanism. *The Quarterly Journal of Economics, 84*(3), 488–500. [https://doi.org/10.2307/1879431](https://doi.org/10.2307/1879431)
- ASML. (n.d.). *EUV lithography systems*. [官方網頁](https://www.asml.com/en/products/euv-lithography-systems)。引用範圍：極紫外光微影技術為 ASML 獨有。
- Associated Press. (2024, July 31). Delta faces $500 million in lost revenue as result of tech outage last week, CEO says. *CBS News*. [官方網頁](https://www.cbsnews.com/news/delta-global-tech-outage-500-million-in-costs/)
- Atkinson, E. (Ed.). (2024, July 19). Global IT outage [Live coverage]. *BBC News*. [官方網頁](https://www.bbc.com/news/live/cnk4jdwp49et?page=6)。引用範圍：倫敦證券交易所集團關於新聞發布服務與交易所照常運作的聲明。
- Bainbridge, L. (1983). Ironies of automation. *Automatica, 19*(6), 775–779. [https://doi.org/10.1016/0005-1098(83)90046-8](https://doi.org/10.1016/0005-1098(83)90046-8)
- Barton, R., & Parker, A. (2021, November 2). *Q3 2021 shareholder letter* (Exhibit 99.3 to Form 8-K). Zillow Group, Inc. [SEC EDGAR](https://www.sec.gov/Archives/edgar/data/1617640/000161764021000085/exhibit993.htm)。引用範圍：購入、庫存與簽約房屋數、存貨減損、第四季預期損失、裁員比例。
- Capen, E. C., Clapp, R. V., & Campbell, W. M. (1971). Competitive bidding in high-risk situations. *Journal of Petroleum Technology, 23*(6), 641–653. [https://doi.org/10.2118/2993-PA](https://doi.org/10.2118/2993-PA)
- Chen, F., Drezner, Z., Ryan, J. K., & Simchi-Levi, D. (2000). Quantifying the bullwhip effect in a simple supply chain: The impact of forecasting, lead times, and information. *Management Science, 46*(3), 436–443. [https://doi.org/10.1287/mnsc.46.3.436.12069](https://doi.org/10.1287/mnsc.46.3.436.12069)。本次只讀到摘要；引用範圍限於集中需求資訊能減少但不能消除波動放大。
- CrowdStrike. (2024a, August 6). *External technical root cause analysis: Channel File 291*. [官方 PDF](https://www.crowdstrike.com/wp-content/uploads/2024/08/Channel-File-291-Incident-Root-Cause-Analysis-08.06.2024.pdf)。文中簡稱 CrowdStrike, 2024a。
- CrowdStrike. (2024b, July 24). *Preliminary post incident review*. [官方網頁](https://www.crowdstrike.com/falcon-content-update-remediation-and-guidance-hub/)。文中簡稱 CrowdStrike, 2024b。
- Edser, N., Armstrong, K., & Phillips, A. (2024, July 19). GPs, airports and banks in UK hit by IT outage. *BBC News*. [官方網頁](https://www.bbc.com/news/articles/cp0823lz4j7o)。倫敦證券交易所集團新聞發布服務的聲明另見同日 BBC 即時報導。
- European Parliament & Council. (2022). Regulation (EU) 2022/2554 on digital operational resilience for the financial sector. *Official Journal of the European Union, L 333*, 1–79. [EUR-Lex](https://eur-lex.europa.eu/eli/reg/2022/2554/oj/eng)。EUR-Lex 文本本次未能擷取；條文內容依歐洲銀行管理局單一規則手冊與條文轉載核對，引用範圍：適用日期、第 26、28、29 條與第 31 條以下。
- Gignac, G. E., & Zajenkowski, M. (2020). The Dunning-Kruger effect is (mostly) a statistical artefact: Valid approaches to testing the hypothesis with individual differences data. *Intelligence, 80*, 101449. [https://doi.org/10.1016/j.intell.2020.101449](https://doi.org/10.1016/j.intell.2020.101449)。本次只核對書目。
- Gudigantala, N., & Mehrotra, V. (2024). Teaching case: When strength turns into weakness: Exploring the role of AI in the closure of Zillow Offers. *Journal of Information Systems Education, 35*(1), 67–72. [https://doi.org/10.62273/TRCF3655](https://doi.org/10.62273/TRCF3655)
- HGC Global Communications. (2024, March 4). *Statement: Supplementary information regarding submarine cable damage in the Red Sea*. [官方網頁](https://www.hgc.com.hk/press-releases/statement-supplementary-information-of-hgc-global-communications-regarding-submarine-cable-damage-in-the-red-sea-to-demonstrate-hong-kong-as-international-telecommunication-hub-to-demonstrate-hong-kong-as-international-telecommunication-hub)
- Kruger, J., & Dunning, D. (1999). Unskilled and unaware of it: How difficulties in recognizing one's own incompetence lead to inflated self-assessments. *Journal of Personality and Social Psychology, 77*(6), 1121–1134. [https://doi.org/10.1037/0022-3514.77.6.1121](https://doi.org/10.1037/0022-3514.77.6.1121)
- Lee, H. L., Padmanabhan, V., & Whang, S. (1997). Information distortion in a supply chain: The bullwhip effect. *Management Science, 43*(4), 546–558. [https://doi.org/10.1287/mnsc.43.4.546](https://doi.org/10.1287/mnsc.43.4.546)。本次讀到摘要；引用範圍：四個來源。
- Levy, A. (2021, November 3). Zillow plunges 25% to lowest since July 2020, after company exits home-buying business. *CNBC*. [官方網頁](https://www.cnbc.com/2021/11/03/zillow-stock-plunges-24percent-after-company-exits-home-buying-business.html)
- National Telecommunications and Information Administration. (2021, July 12). *The minimum elements for a software bill of materials (SBOM)*. U.S. Department of Commerce. [官方網頁](https://www.ntia.gov/report/2021/minimum-elements-software-bill-materials-sbom)
- Nicoll, A., & Geiger, D. (2021, November 9). Zillow insiders are blaming an internal initiative called Project Ketchup for the company's home-flipping failures. *Business Insider*. [官方網頁](https://www.businessinsider.com/zillow-insiders-blame-project-ketchup-for-firms-home-buying-failures-2021-11)
- Nuhfer, E., Cogan, C., Fleisher, S., Gaze, E., & Wirth, K. (2016). Random number simulations reveal how random noise affects the measurements and graphical portrayals of self-assessed competency. *Numeracy, 9*(1), Article 4. [https://doi.org/10.5038/1936-4660.9.1.4](https://doi.org/10.5038/1936-4660.9.1.4)
- Perdomo, J., Zrnic, T., Mendler-Dünner, C., & Hardt, M. (2020). Performative prediction. In *Proceedings of the 37th International Conference on Machine Learning* (PMLR 119, pp. 7599–7609). [官方網頁](https://proceedings.mlr.press/v119/perdomo20a.html)
- Perrow, C. (1999). *Normal accidents: Living with high-risk technologies* (Updated ed.). Princeton University Press. ISBN 9780691004129。初版 1984 年。引用範圍：交互複雜性與緊耦合的概念。
- Peters, O. (2019). The ergodicity problem in economics. *Nature Physics, 15*(12), 1216–1221. [https://doi.org/10.1038/s41567-019-0732-0](https://doi.org/10.1038/s41567-019-0732-0)。引用範圍：+50%／−40% 賭局；文中的倍數為依其設定計算。
- Russon, M.-A. (2021, March 29). The cost of the Suez Canal blockage. *BBC News*. [官方網頁](https://www.bbc.com/news/business-56559073)。每日受阻貨值引自 Lloyd's List 的估計，指受阻的貿易額，不是每日損失。
- Semiconductor Industry Association, & Boston Consulting Group. (2021, April). *Strengthening the global semiconductor supply chain in an uncertain era*. [官方網頁](https://www.semiconductors.org/strengthening-the-global-semiconductor-supply-chain-in-an-uncertain-era/)。2021 年的產能分布，不代表現況。
- Stanford Securities Class Action Clearinghouse. (n.d.). *Zillow Group, Inc. securities litigation*. [官方網頁](https://securities.stanford.edu/filings-case.html?id=107828)。案號 2:21-cv-01551-TSZ（W.D. Wash.）；本次未逐一查閱 2026 年的訴訟紀錄。
- Sterman, J. D. (1989). Modeling managerial behavior: Misperceptions of feedback in a dynamic decision making experiment. *Management Science, 35*(3), 321–339. [https://doi.org/10.1287/mnsc.35.3.321](https://doi.org/10.1287/mnsc.35.3.321)。本次讀到摘要。
- Weston, D. (2024, July 20). *Helping our customers through the CrowdStrike outage*. Official Microsoft Blog. [官方網頁](https://blogs.microsoft.com/blog/2024/07/20/helping-our-customers-through-the-crowdstrike-outage/)
- Yousif, N. (2024, August 9). Delta Airlines hits out at CrowdStrike, alleging $500m loss. *BBC News*. [官方網頁](https://www.bbc.com/news/articles/c6284e7r7d7o)
- Zillow. (2021, February 25). *Zillow starts making cash offers for the Zestimate*. [官方網頁](https://zillow.mediaroom.com/2021-02-25-Zillow-Starts-Making-Cash-Offers-For-the-Zestimate)
- Zillow Group, Inc. (2021, November 2). *Zillow Group reports third-quarter 2021 financial results* (Exhibit 99.1 to Form 8-K). [SEC EDGAR](https://www.sec.gov/Archives/edgar/data/1617640/000161764021000085/q32021991.htm)。引用範圍：執行長說明、售價預測不確定性與產能限制。
