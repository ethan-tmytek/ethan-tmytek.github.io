---
title: "Day 3｜Kickoff 後的執行治理：五大類事件與四層節奏"
date: 2026-05-10T14:00:00+08:00
draft: false
tags: ["PDM", "Project", "Jira"]
categories: ["專案管理"]
description: "這是系列文章的第三篇，主要是給公司內部團隊看的，關於我們 TMYTEK 作為軟硬體整合商，在專案管理
上該如何執行。"
showToc: true
TocOpen: true
---

> 系列：《TMYTEK 的硬體專案執行心法：從 Kickoff 到量產的工程治理》
> 適讀：PJM / EPM / MPM 團隊，以及與我們協作的 BD / SCM / R&D 同仁

Day 1 建立共同語言、Day 2 完成 Kickoff 起手式。今天進入專案執行階段。

---

## 一、起手式 vs. 執行中的本質差異

Kickoff 前做的是**確定性規劃**——已知的工作、已知的依賴、已知的時程。Kickoff 後進入**不確定性管理**——未知的 bug、未預期的 issue、客戶變更、外部依賴失準、技術風險浮現。

兩者用的工具完全不同。Kickoff 前需要的是**結構**（Initiative / Epic / Story 的層級），Kickoff 後需要的是**流程**（事情發生時誰決定什麼、什麼時候升級、什麼時候動 baseline）。

我們看過的最大專案失敗模式，從來不是「規劃做得不好」，而是「規劃做得很好，但執行中沒有應變治理，新狀況一個一個累積，三個月後 baseline 已經跟現實脫節，PJM 還在維護一個假的計畫表」。**起手式做扎實只是 30 分，執行治理才是另外 70 分。**

執行中要處理的事情可以分成五大類：

1. **Issue**（執行中浮現的問題）
2. **Bug**（軟韌體缺陷）
3. **Risk**（識別到但尚未發生的風險）
4. **Change Request**（範圍變更請求）
5. **Dependency Slip**（依賴鏈延遲）

但先講一個貫穿所有類別的核心紀律：

---

## 二、核心紀律：不要污染原始 Story

新手 PJM 最常犯的錯誤是：執行中發現問題，就直接修改原本的 Story 描述、加 Task、改估算。**這會污染歷史資料、破壞 Predictability 計算、讓回顧分析失真。**

正確做法是：**原始 Story 凍結，新狀況用新 issue 表達，並用 link 關聯**。

例如 EE-EVT 的 ARR-303（電源 rail 紋波量測）發現紋波超出規範，正確處理是新建一張 Bug `ARR-3501`，描述「ARR-303 量測發現電源 rail 1 紋波超出規範 12mV」，並用 `is caused by` 連結到 ARR-303。**原始 Story 該標完成就標完成（量測這件事有做），缺陷另外追蹤**。

這個紀律會穿透下面所有五類的處理流程。

---

## 三、第一類：Issue（執行中浮現的問題）

**定義：** 執行某個 Story 過程中發現的非預期狀況。可能是設計缺陷、量測異常、規格不清、跨職能介面理解不一致。

**Jira 表達：** 不用 Bug type（Bug 是專指軟韌體缺陷）。Issue 用 Task issue type 加上自定義欄位區分。建議建立一個叫 `Execution Issue` 的子類型。

**必填欄位：**

| 欄位 | 說明 |
|------|------|
| Issue Type | Task（標記為 Execution Issue） |
| Severity | P0 / P1 / P2 / P3（4 級嚴重度） |
| Linked to | 關聯到原始 Story（用 "is caused by" 或 "blocks"） |
| Owner | 主責解 issue 的人 |
| Reporter | 發現的人 |
| Discovery Phase | Concept / Design / EVT / DVT / PVT |
| Target Resolution | 目標解決日期 |

### Severity 分級（PJM 必須在 Kickoff 前定義清楚）

**P0 — 阻斷性：** 不解這個 issue，下個 Phase Gate 鐵定過不了。例如 EVT 發現天線增益實測比規範差 5dB——這是 P0，因為 DR2 Exit Criteria 直接卡住。**P0 issue 24 小時內必須有 owner 和初步 mitigation plan。**

**P1 — 嚴重影響：** 不解會大幅增加風險，但有解法繞過。例如電源紋波超規範但已有候選對策（換電容、改 layout）。**P1 issue 一週內必須有 mitigation plan。**

**P2 — 可控影響：** 影響有限，可以排入下個 sprint 處理。

**P3 — Nice to have：** 應該修但不影響交付。

### 紀律：P0 / P1 數量是 PJM 的健康指標

**P0 / P1 的數量是 PJM 的健康指標，不是工程師的指責對象。** 如果某週新增 5 個 P0，是專案系統性出問題，不是哪個工程師沒做好。PJM 要做的是分析 root cause（為什麼這麼多 P0 沒在 Concept / Design 階段就抓到？），而不是檢討發現 issue 的人。

**獎勵發現 issue 的人，懲罰隱瞞 issue 的人**——這是 TMYTEK 專案文化最重要的設定。

### Issue 工作流

**Open** → **Triaged**（PJM + 主責職能 Lead 評過，確認 severity 與 owner）→ **In Progress** → **In Review** → **Resolved** → **Closed**

**紀律：** Issue 的 status 變更要由 owner 主動操作，不要由 PJM 代勞。這個動作是 owner 對團隊的「信號發送」——「我接手了」、「我做完了」。

---

## 四、第二類：Bug（軟韌體缺陷）

**定義：** 韌體、FPGA、GUI 的功能性缺陷。**硬體類缺陷（電路、機構、天線）歸 Issue，不歸 Bug**——這是業界慣例，因為 Bug 工作流是針對軟韌體生命週期設計的。

Bug 跟 Issue 處理流程相似，但有兩個關鍵差異：

**差異一：Bug 必須附「重現步驟」與「環境」。**

每個 Bug 在 Open 狀態時，必填欄位包含：Reproduce Steps、Environment（哪個 build、哪個硬體版本、哪個 GUI 版本）、Expected Behavior、Actual Behavior。**沒有這四個欄位的 Bug 一律打回**——不是 PJM 的紀律，是 QA 的責任。**讓 QA 來把這道關**，PJM 別介入細節。

**差異二：Bug 要綁 build version。**

用 Jira 的 Affects Version / Fix Version 欄位。Affects Version = 哪個 build 發現的、Fix Version = 哪個 build 計畫修。這個資訊讓 release notes 自動可以生成「這個 build 修了哪些 Bug」。

### 特殊處理：Won't Fix

PJM 必須有勇氣對某些 Bug 說「這次不修」。例如 PVT 階段發現一個 P3 GUI 文字錯誤，修它要重做韌體 release flow 再花一週。**這時應該 Won't Fix，並把 Bug 移到下一個 release。**

但 Won't Fix 必須**有書面理由**並通知客戶（如果是客戶看得到的東西）。在 Jira 上要有 Resolution = "Won't Fix" + Comment 說明原因 + 在 Risk Log 記錄這個決定。**未來客戶問「為什麼這個沒修？」我們才有東西可以回。**

---

## 五、第三類：Risk（識別到但尚未發生的風險）

這是大部分 PJM 處理得最差的一類，但其實是最有價值的一類。

**核心觀念：** Issue 是「已發生的問題」，Risk 是「可能發生的問題」。Issue 反應現在，Risk 預測未來。**好的 PJM 80% 的時間在處理 Risk，20% 在處理 Issue；差的 PJM 反過來。**

### Jira 表達：把 Risk 當作獨立 Issue Type

我們把 Risk 當作 Story-level 的特殊 issue type，而不是 Task。原因是 Risk 有自己的生命週期（識別 → 評估 → 緩解 → 監控 → 關閉）。

**必填欄位：**

| 欄位 | 說明 |
|------|------|
| Risk Type | Technical / Schedule / Resource / External / Customer |
| Probability | High / Medium / Low |
| Impact | High / Medium / Low |
| Risk Score | Probability × Impact 自動計算 |
| Mitigation Plan | 怎麼降低發生機率 |
| Contingency Plan | 萬一發生了怎麼辦 |
| Trigger | 什麼狀況代表這個 Risk 已經要發生了 |
| Owner | 主責監控與緩解的人 |
| Phase | 這個 Risk 主要影響哪個階段 |

### Risk Log 維護紀律

**每週的 PJM 站立會，第一個議程永遠是 Risk Log 巡檢。** 不是 Issue、不是進度，是 Risk。

理由：Issue 自己會吵，Risk 不吵但會在最壞的時刻爆炸。

每週要做的事：新識別的 Risk 加入、已緩解的 Risk 關閉、Trigger 已觸發的 Risk 升級為 Issue（注意：這時要新建一個 Issue 並 link 到原 Risk，不要直接改 Risk 的 type）。

### ARRAY-26 的初始 Risk Log

PJM 在 Kickoff 時應該已經識別的 Risk（基於專案類型可預測，不是事後諸葛）：

| Risk | Type | P | I | Mitigation | Contingency |
|------|------|---|---|-----------|-------------|
| 客戶 POD 規範解讀分歧 | Customer | M | H | DR0 前與客戶開規範 walkthrough、簽 understanding 文件 | 升級到客戶決策層 |
| 天線陣列實測達不到客戶增益規範 | Technical | M | H | Concept 階段做 link budget 預留 3dB 餘裕 | 啟用 plan B 拓撲、申請客戶規範鬆綁 |
| FPGA 與 RF 整合 timing 失準 | Technical | M | M | EVT 早期就跑跨職能 bring-up | 增加 EE + FPGA 整合 sprint |
| ODM 打樣排程延遲 | External | H | M | Design 階段就鎖兩家 PCB 廠 | 啟用備援廠商 |
| 客戶端口對接窗口變動 | Customer | M | M | 雙窗口（主 + 副）並建立月度同步會 | 升級到 PJM 對 PJM |
| 韌體與 GUI 介面定義改動 | Technical | H | M | Design 階段定義 API contract 並 freeze | 開 sprint 衝刺對接 |
| 環境測試一次未過要重做 | External | M | H | EVT 階段先做粗測、不要拖到 DVT 才一次 | 預留 2 週環境測試 buffer |
| 量產治具開發落後 | Schedule | M | H | NPI Epic 在 DVT 階段就啟動而非 PVT | 用試產治具替代正式治具交付 |
| **關鍵料件交期信心度低** | External | M | H | SCM 已追蹤兩家備援代理商 | 啟用替代料（EE Lead 已預先驗證） |

8–10 個初始 Risk 是合理的數量。少於 5 個代表 PJM 沒認真想，多於 15 個代表 Risk 與 Issue 沒分清楚。

---

## 六、第四類：Change Request（範圍變更請求）

這是專案治理裡最容易失控的一類。

**核心紀律：所有範圍變更必須走正式流程，不接受口頭 / Email / Slack 直接生效。**

### Jira 表達

CR 是跟 Initiative 同層級的 Issue Type（不掛在 Epic 下）。原因：CR 可能影響多個 Epic、多個職能。

**必填欄位：**

| 欄位 | 說明 |
|------|------|
| Source | Customer / Internal / External Regulation |
| Requestor | 提出變更的人 |
| Description | 要改什麼 |
| Reason | 為什麼要改 |
| Affected Epics | 受影響的 Epic（多選） |
| Schedule Impact | 預估影響時程多久 |
| Resource Impact | 預估增加多少人月 |
| Cost Impact | 預估增加多少成本 |
| Recommendation | PJM 的建議：Approve / Reject / Defer |
| Decision | 最終決議 + 簽核者 |

### CR 工作流

**Submitted** → **Under Analysis**（PJM + 相關 Lead 評估，1 週內完成）→ **Decision Pending** → **Approved / Rejected / Deferred** → **Implemented** → **Closed**

### 決策權限階梯（Kickoff 前必須與 VP 對齊）

| 簽核層級 | 適用 |
|---------|------|
| **PJM 可決定** | Schedule Impact ≤ 1 週、Resource Impact ≤ 5 人日、Cost ≤ 5 萬台幣，且不影響客戶可見規格 |
| **VP 產品需簽核** | Schedule Impact 1–4 週、Resource Impact 5–20 人日、不改變客戶交付承諾的內部變更 |
| **客戶 + VP 雙方簽核** | Schedule Impact > 4 週、影響客戶可見規格、影響交付日 |

**這個階梯必須在 Kickoff 時對全團隊宣告。** 因為現實中 80% 的 CR 是工程師覺得「順便改一下沒關係」的小事，沒走流程直接做了，三個月後累積成 baseline 偏離。階梯講清楚，工程師才知道什麼算「順便」、什麼必須走流程。

### CR 對 baseline 的處理

CR 通過後，**不要直接改原始 Epic / Story 的估算和時程**。正確做法：

1. 原始 Story 的 baseline（規劃時的估算與時程）凍結保留，當作歷史記錄
2. CR 通過後，建立新的 Story（如果是新增功能）或新的 sub-task（如果是修改現有功能），清楚標記為「來自 CR-XXX」
3. 更新 Epic 的「Latest Estimate」欄位，反映 CR 後的最新預估

這樣做的好處：年底回顧時，可以清楚看到「原本規劃 100 人月，CR 加了 15 人月，實際做了 110 人月」——CR 的影響被顯性化。

---

## 七、第五類：Dependency Slip（依賴鏈延遲）

**定義：** 外部依賴（供應商、ODM、實驗室、客戶）或跨職能依賴沒在預定時間到位。

這一類在純軟體公司不太重要，**硬體專案最常爆的就是這類**。

### 偵測

不用建新的 issue type。**Dependency Slip 的訊號是 Story 在 External Wait 狀態超過原始預估時間 50%**。例如某個 Story 規劃 External Wait 5 天（等打樣回來），第 8 天還沒回——觸發 Dependency Slip。

可以用 Jira automation 自動偵測：當 status = External Wait 且超過某天數，自動 escalate 給 PJM。

### 處理

PJM 收到 escalation 後 **24 小時內**要做三件事：

第一，**確認真實狀態**。打給供應商 / 實驗室 / 客戶聯絡窗口，問清楚到底什麼時候會到。**不要用郵件問，用電話**——電話會逼對方給出明確答案，郵件會收到「我們儘快處理」這種無用的回覆。

第二，**評估對下游 Epic 的影響**。如果延 3 天，可能下游吸收得了；如果延 2 週，必須觸發 baseline 調整。

第三，**決定是否升級為 Risk 或 Issue**。如果延遲已成定局，建立一個 Issue；如果還有可能補救，更新原本的 Risk Log。

### 預防：依賴日曆

PJM 應該在 Kickoff 前建立 **Dependency Calendar**，把所有外部依賴的時間點列出來：第一輪 PCB 打樣回來日、第二輪打樣回來日、機殼模具打樣日、認證實驗室排期、客戶樣品評估會議、ODM 試產日、海運交期……每一項都標出 owner 和 trigger date。

每週 PJM 站立會要過一遍——**比起追工程師進度，追外部依賴是 PJM 真正創造價值的地方**。工程師會自動推進工作，外部依賴不會自動推進它自己。

**這也是 Day 2 講過的 PJM ↔ SCM 月度 Material Sync 的延伸**——SCM 是 PJM 在物料依賴上最重要的合作夥伴，依賴日曆上凡涉及料件的項目，都應該與 SCM 共同維護。

---

## 八、四層治理節奏

工具講完了，最後講節奏——這些工具要透過什麼樣的會議與檢視時點來發揮作用。

我們採用**四層循環**：

### 每日（Daily）：非同步 Slack standup

不開站立會。45 人 + 矩陣式組織，硬要每天開站立會會崩潰。改用 **Slack 非同步 standup**——每個職能在自己的 channel 早上 10 點前 post 三件事：今天做什麼、有沒有 blocker、有沒有需要 PJM 知道的事。

PJM 每天上午看這些，發現異常才打電話介入。

### 每週（Weekly）：PJM Sync · 30–45 分鐘 · 職能 Lead + PJM

議程固定四項：

1. **Risk Log 巡檢**（10 分鐘）：新識別 / 已關閉 / Trigger 接近的 Risk
2. **P0 / P1 Issue 與 Bug 進度**（10 分鐘）
3. **下週的依賴日曆**（5 分鐘）：含 SCM 料況週報重點摘要
4. **下週的職能負載**（5 分鐘）

**這 30 分鐘是整個專案最關鍵的會議**，因為這時做的調整成本最低。

### 雙週（Bi-weekly）：Sprint Retro + Demo · 60 分鐘 · 全團隊

工程紀律的會議——demo 已完成的 Story、Retro 上個 Sprint 的流動指標、討論工程流程改善。注意這個會議不討論 schedule 或 risk，那是 PJM Sync 的事。

### 每月（Monthly）：VP Review · 45 分鐘 · PJM + VP

給 VP 看的——當月儀表板狀態、需要 VP 簽核的 CR、未來一個月的 Risk forecast。

### Phase Gate（不定期）：Design Review · 120 分鐘 · 全 stakeholder + VP

Phase Gate 不是「會議」，是「決策」——有結構化的 Exit Criteria 對照、明確的 Pass / Conditional Pass / Fail / Reject 結果。

---

## 九、Initiative 健康度標準（給 VP 看的判斷工具）

什麼時候 VP 該介入一個專案，什麼時候放手讓 PJM 做？我們用三個指標：

| 指標 | 健康 | 警戒 | 紅燈 |
|------|------|------|------|
| 未解決 P0 / P1 數量 | ≤ 3 | 4–6 | ≥ 7 |
| Risk Score 加權合計趨勢 | 平穩或下降 | 連 2 週上升 | 連 4 週上升 |
| Predictability（承諾 vs 完成） | 80–110% | 60–80% | < 60% 連 2 期 |

如果某專案連續兩週進入紅燈區，**VP 應該介入**——這不是責備 PJM，是支援 PJM 解開組織層級的瓶頸。

---

## 十、收斂：完整的 PJM 工具箱

到這裡，從 Kickoff 前的 Jira 框架，到 Kickoff 後的執行治理，PJM 在硬體專案上需要的工具都齊了。

**Kickoff 前準備：** Initiative + Epic 結構、Phase Gate Exit Criteria、初始 Risk Log、Dependency Calendar、決策權限階梯、SCM MRL 與 ETA 確認。

**Kickoff 後維護：** Issue / Bug 的 Triage、Risk Log 的週度巡檢、CR 的正式流程、依賴鏈的主動追蹤、四層治理節奏。

**VP 真正要看的：** 月度的儀表板、Risk Score 加權合計、未解決 P0/P1 數量、Predictability 趨勢。

---

## 三天系列總結

這個系列的目的不是講「Jira 怎麼用」——任何 PJM 都可以查 Atlassian 文件學會 Jira 操作。**這個系列要建立的是 TMYTEK 內部的工程治理共同語言**：

- **Day 1** 建立資源語言（Investment Category + 層級結構）
- **Day 2** 示範 Kickoff 前的起手式（用 ARRAY-26 為例 + PJM ↔ SCM 採購流程）
- **Day 3** 講 Kickoff 後的執行治理（五大類事件 + 四層節奏）

這套方法不是 TMYTEK 獨創——它是 FAANG 工程治理 + 硬體 NPI 流程 + 矩陣式組織管理的綜合，並針對我們公司的實況（45 人、軟硬整合、ODM/EMS 量產、Jira Plans）做了細部調整。

PJM 的核心價值不是「催進度」，是**讓正確的決策在正確的時間被做出來**。把結構建好、把流程跑好，催進度的需求自然會減少 80%。

---

**接下來：** 這套方法已開始在 ARRAY-26 與其他進行中的專案套用。如果你的專案也準備啟動，請聯絡 PJM 團隊取得 Jira Project Template 與初始 Risk Log 範本，避免從零開始。

如果你（特別是 BD / SCM / R&D 的同仁）對這套方法的某個面向有疑問，或者想把這套方法如何套用在你協作的專案上，歡迎在這篇文章下留言或直接找 PJM 團隊討論。
