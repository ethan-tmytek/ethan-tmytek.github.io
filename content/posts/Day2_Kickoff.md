---
title: "Day 2｜Kickoff 前的起手式：以 Project ARRAY-26 為例"
date: 2026-05-10T14:00:00+08:00
draft: false
tags: ["PDM", "Project", "Jira"]
categories: ["專案管理"]
description: "這是系列文章的第二篇，主要是給公司內部團隊看的，關於我們 TMYTEK 作為軟硬體整合商，在專案管理上該如何執行。"
showToc: true
TocOpen: true
---

> 系列：《TMYTEK 的硬體專案執行心法：從 Kickoff 到量產的工程治理》
> 適讀：PJM / EPM / MPM 團隊，以及與我們協作的 BD / SCM / R&D 同仁

Day 1 我們建立了共同語言。今天我們用一個具體案例，示範資深 PJM 在 **Kickoff 之前**應該把 Jira 結構建立到什麼程度。這篇是 PJM 的「起手式」標準作業。

---

## 案例設定：Project ARRAY-26

> **客戶要求：** 2026 Q3 交付 5 套客製化陣列天線 Antenna Card，並通過客戶規範的 POD（Prove of Design）測試。

這個專案橫跨 7 個工程職能：

- 天線（ANT / Tony）
- 射頻（RF / Vic）
- 電子工程（EE / Ken）
- 機構與熱流（ME / Andy）
- 韌體（FW / Silas）
- GUI 軟體（GUI / Ivy）
- FPGA（FPGA / Keith）

這個規模的專案，PJM 在 Kickoff 前該完成什麼？我們一步一步走。

---

## 一、三個關鍵設計判斷

進入細節前，先講三個會貫穿整個結構的設計判斷：

**判斷一：這個專案是 Initiative，不是 Epic。**

雖然只有 5 套 Antenna Card 看起來像「一個產品」，但它跨越完整的 NPI 流程、橫跨 7 個職能、時程跨多個季度——符合 Initiative 的判準。在我們跟客戶或董事會的單頁 roadmap 上，這就是一條獨立的線。

**判斷二：Epic 的切分原則是「職能 × NPI 階段」的矩陣。**

這是硬體專案最自然的切法。同一個 NPI 階段（例如 EVT），不同職能要做的事完全不同（ANT 在量測天線、EE 在驗證電源、FPGA 在驗證時序）；同一個職能（例如 ANT），不同階段的工作也完全不同（Concept 在做拓撲、PVT 在做量產一致性）。把這兩個維度交叉，就得到 Epic 結構。

**判斷三：Review Meeting 不是「行政事項」，是 Epic 級別的工作項。**

很多 PJM 把 DR (Design Review) 當作會議室預訂，這是錯的。Review 的準備工作（document package、test report、risk log）是真實的工程工時，必須當 Epic 處理，否則它會在最後一週吃掉所有人的時間。

---

## 二、Initiative 設定

| 欄位 | 值 |
|------|---|
| Issue Type | Initiative |
| Status | Concept |
| Owner | PJM（指派） |
| Customer | (客戶名稱) |
| 目標完成季度 | 2026 Q3 |
| 預估人月 | 約 42 人月 |
| 策略類別 | 客製化新硬體產品 |
| 主要里程碑 | DR0 (2026-01-15) · DR1 (2026-03-13) · DR2 (2026-05-22) · DR3 (2026-07-17) · POD (2026-08-21) · 交付 (2026-09-25) |

預估人月：7 個職能 × 平均每人 6 個月參與 × 平均 0.6 投入比例 ≈ 25 人月直接投入，加上 PJM、QA、生產協助、Review 籌備等間接約 17 人月，合計 42 人月。

---

## 三、NPI 階段時程主軸

我們用 9 個月的標準節奏：

| 階段 | 起訖 | 週數 | 主要產出 | Phase Gate |
|------|------|------|---------|-----------|
| Concept & Planning | 2026-01-05 ~ 01-15 | 2 | 系統架構、技術可行性、BOM 雛形 | DR0 |
| Design | 2026-01-16 ~ 03-13 | 8 | Schematic、Layout、機構、FW/GUI/FPGA 架構 | DR1 |
| EVT | 2026-03-16 ~ 05-22 | 10 | EVT 樣品、各職能驗證報告、Risk Log | DR2 |
| DVT | 2026-05-25 ~ 07-17 | 8 | DVT 樣品、整合測試、可靠度測試 | DR3 |
| PVT | 2026-07-20 ~ 08-21 | 5 | 5 套量產樣品、產線驗證、客戶 POD | POD Pass |
| Delivery | 2026-08-24 ~ 09-25 | 5 | 5 套交付、文件交付、初期保固 | Initiative Close |

**重要紀律：** DR0 放在 Concept 結束後、Design 啟動前——這代表 Design 階段只能在 DR0 通過後才啟動。如果 Concept 階段發現某個技術風險過大（例如客戶要求的天線增益實作上極困難），DR0 就是停損點。**這個紀律會省掉 80% 的後期返工。**

---

## 四、Epic 矩陣：42 個 Epic 的全貌

把 7 個職能 × 5 個階段交叉，加上 5 個 Phase Gate Epic + 2 個橫向整合 Epic，總共 **42 個 Epic**。下表呈現職能 × 階段的矩陣：

| 職能 | Concept (W1–2) | Design (W3–10) | EVT (W11–20) | DVT (W21–28) | PVT (W29–33) |
|------|:---:|:---:|:---:|:---:|:---:|
| **ANT** Tony | 陣列拓撲 / 增益分析 | 天線元件設計 / EM 模擬 | EVT 天線量測 / S 參數 | DVT 陣列校準 | PVT 量產一致性 |
| **RF** Vic | RF 鏈路預算 / 元件選型 | RF 電路設計 / 模擬 | EVT RF 性能驗證 | DVT RF 校準與穩定性 | PVT RF 量產測試程式 |
| **EE** Ken | 系統電源與訊號架構 | Schematic / PCB Layout | EVT 板級驗證 | DVT 整合與 SI/PI | PVT 板級量產驗證 |
| **ME** Andy | 機構架構 / 熱流預估 | 機構詳細設計 / 熱模擬 | EVT 機構與熱驗證 | DVT 環境與可靠度 | PVT 量產治具 |
| **FW** Silas | 韌體架構與介面定義 | 韌體核心開發 | EVT 韌體驗證與 bring-up | DVT 韌體穩定化 | PVT 韌體 release |
| **GUI** Ivy | GUI 需求與架構 | GUI 開發 / API 串接 | EVT GUI 整合測試 | DVT GUI 穩定化 | PVT GUI 交付版 |
| **FPGA** Keith | FPGA 架構與 IP 評估 | FPGA RTL 開發 / 模擬 | EVT FPGA bring-up | DVT FPGA 時序與穩定性 | PVT FPGA bitstream release |

**外加 5 個跨職能 Phase Gate Epic：**
- DR0 · Concept Review · 2026-01-15
- DR1 · Design Review · 2026-03-13
- DR2 · EVT Exit Review · 2026-05-22
- DR3 · DVT Exit Review · 2026-07-17
- POD · 客戶 Prove of Design · 2026-08-21

**外加 2 個橫向整合 Epic：**
- 系統整合測試（跨 EVT / DVT，PJM 主導，所有職能協作）
- 文件交付與客戶簽收（PVT 後段，PJM + 文管）

---

## 五、Investment Category 的分配

注意一個關鍵設計：**同一個 Initiative 底下的 Epic 會分屬不同 Investment Category**。具體分配：

| 階段 | Investment Category | Epic 數量 |
|------|---------------------|----------|
| Concept + Design (W1–10) | 1. 新硬體產品 | 14 |
| EVT + DVT (W11–28) | 4. 驗證 / 認證 / 合規 | 14 |
| PVT (W29–33) | 5. 製造 / NPI | 7 |
| Phase Gate (DR0–DR3、POD) | 1. 新硬體產品 | 5 |
| 系統整合 + 文件交付 | 1 + 5（各一） | 2 |

當 VP 看儀表板時，這個專案的工時會自然分布在三個類別——這正反映了硬體專案的真實樣貌：「真正在做新東西」的時間佔約 40%，「驗證」佔 40%，「量產導入」佔 20%。**如果某個硬體專案 90% 都歸在類別 1，那一定是分類錯誤。**

---

## 六、Story 與 Task 範例：以 EE-EVT 為例

完整 42 個 Epic 全展開到 Story 會超過 300 個 Story、上千個 Task。我們選一個代表性 Epic 完整展開，其他套用相同模式。

### Epic：EE-EVT · EVT 板級驗證（Ken 主責）

| 欄位 | 值 |
|------|---|
| Investment Category | 4. 驗證 / 認證 / 合規 |
| 主責職能 | EE |
| 貢獻職能 | FW, FPGA, ANT, RF |
| 當前階段 | EVT |
| 規模估算 | 硬體日曆 6 週 + 工程人日 22 |
| 起訖 | 2026-03-30 ~ 2026-05-08 |
| Definition of Done | EVT 板通過所有電源 / 訊號完整性 / 介面驗證、輸出 EVT Test Report、Risk Log 更新、進入 DR2 候選 |

**Story 列表（11 個）：**

| Story Key | 名稱 | SP | 職能 | 狀態類型 | 天數 |
|-----------|------|----|------|---------|------|
| ARR-301 | EVT 板開箱檢查與目視 / X-ray | 2 | EE | Active | 1 |
| ARR-302 | Power-on 測試 | 3 | EE | Active | 2 |
| ARR-303 | 各電源 rail 量測與紋波分析 | 5 | EE | Active | 3 |
| ARR-304 | 系統 clock 訊號完整性量測 | 3 | EE | Active | 2 |
| ARR-305 | High-speed 介面眼圖量測 | 5 | EE | Active | 3 |
| ARR-306 | 等待 FW bring-up 完成 | 0 | EE | **External Wait** | 5 |
| ARR-307 | 與 FW 協作驗證控制介面 | 3 | EE+FW | Active | 2 |
| ARR-308 | 與 FPGA 協作驗證 SerDes 連通 | 5 | EE+FPGA | Active | 3 |
| ARR-309 | 熱量測（搭配 ME） | 3 | EE+ME | Active | 2 |
| ARR-310 | 撰寫 EVT 板級 Test Report | 3 | EE | Active | 2 |
| ARR-311 | 更新 Risk Log 給 DR2 | 2 | EE | Active | 1 |

**注意 ARR-306 這張 External Wait 狀態的 Story**——這是硬體專案的關鍵紀律。等待韌體 bring-up 完成是真實發生的時間，**必須建票而非隱藏**。否則 DR2 倒數時會發現「為什麼 EE 的工作好像都被推遲」，但其實是依賴鏈問題。把它顯性化，PJM 才能在週會上看到「EE 卡住了不是因為慢，是因為 FW」，並去推 FW。

---

## 七、Phase Gate Epic：以 DR2 為例

DR2（EVT Exit Review）是這個專案最關鍵的 Phase Gate，決定能不能進 DVT。

**Story 列表（10 個，由 PJM 拆分給各職能）：**

| Story Key | 名稱 | 職能 | SP | 天數 |
|-----------|------|------|----|------|
| ARR-401 | ANT 提交 EVT 天線量測報告 | ANT | 3 | 2 |
| ARR-402 | RF 提交 EVT RF 性能驗證報告 | RF | 3 | 2 |
| ARR-403 | EE 提交 EVT 板級驗證報告 | EE | 3 | 2 |
| ARR-404 | ME 提交 EVT 機構與熱驗證報告 | ME | 3 | 2 |
| ARR-405 | FW 提交 EVT 韌體驗證報告 | FW | 3 | 2 |
| ARR-406 | GUI 提交 EVT GUI 整合測試報告 | GUI | 2 | 1 |
| ARR-407 | FPGA 提交 EVT FPGA bring-up 報告 | FPGA | 3 | 2 |
| ARR-408 | PJM 整合 EVT 跨職能 Risk Log | PJM | 3 | 2 |
| ARR-409 | PJM 撰寫 DR2 簡報 | PJM | 5 | 3 |
| ARR-410 | DR2 會議召開 + Exit Criteria 簽核 | All | 2 | 1 |

### DR2 的 Exit Criteria（PJM 必須在 Kickoff 前就明確定義）

- 天線陣列實測增益達到客戶規範 ≥ 95%
- RF 鏈路 EVM 在規範範圍內
- 所有電源 rail 紋波 < 規範閾值
- 高速介面眼圖通過 mask 規範
- 機構通過初步熱穩態測試（穩態溫度 < 規範 -5°C 安全餘裕）
- 韌體核心功能 100% 通過
- FPGA bring-up 完成且時序通過 STA
- Risk Log 中 P0/P1 風險全數有 mitigation plan

**PJM 紀律：Exit Criteria 必須在 Kickoff 前寫死、不可在會議上即興放寬。** 如果某項沒過，要嘛延 DR2、要嘛開「Conditional Pass」並建立 follow-up Epic 進入 DVT 階段監控——但 Conditional Pass 一個專案內最多 2 次，第 3 次就要升級到 VP 介入。

---

## 八、整合 Epic：最容易被遺漏的關鍵

「系統整合測試」這個 Epic 存在的理由：**單一職能的 Epic 永遠只測試自己負責的部分**。ANT 量測天線、RF 驗證鏈路、EE 驗證電源——但「ANT + RF + EE + FW + FPGA 從頭到尾跑完一次客戶 use case」這件事，沒有任何單一職能會主動做。

把它變成獨立 Epic，由 PJM + QA 主責。Story 範例：

| Story 名稱 | 負責 |
|-----------|------|
| 端到端 use case 1：客戶情境 A 全鏈路測試 | PJM + QA + All |
| 端到端 use case 2：客戶情境 B 全鏈路測試 | PJM + QA + All |
| 跨職能介面壓力測試 | PJM + EE + FW + FPGA |
| **客戶 POD 測試 dry run（POD 前 2 週）** | PJM + QA + All |

最後那個 Story 是 PJM 的關鍵風險控管動作——**在客戶來看 POD 之前，自己先把 POD 跑一遍**。任何 PJM 不做這個 dry run 就讓客戶來測的，幾乎都會在 POD 當天出包。

---

## 九、PJM 與 SCM 的互動：採購物料的關鍵環節

> 這一節特別寫給 SCM 團隊與 PJM 團隊閱讀。我們的合作介面在這裡。

硬體專案的成功有一半在工程，另一半在物料。**PJM 把 Epic 規劃好了，但如果零件三週後才到，所有的 Epic 計畫都是空的。** 這就是 PJM 與 SCM 必須緊密協作的原因。

### 採購觸發時機

在我們的 NPI 流程中，主要的採購觸發點有 **3 個**：

| 觸發點 | 時機 | 採購規模 | 用途 |
|--------|------|---------|------|
| **POC 物料** | Concept 階段啟動後 | 少量（1–2 套） | 技術可行性驗證、實驗用 |
| **EVT 物料** | DR0 通過、Design 階段中段 | 小量（3–5 套） | EVT 樣品製作 |
| **DVT 物料** | DR1 通過、Design 完成後 | 中量（5–10 套） | DVT 樣品製作 + 認證測試件 |

PVT 階段的量產物料因為跟產線排程綁在一起，由 SCM 主導、PJM 配合，不在這份 PJM 起手式的範圍。

### 標準採購流程

每一個觸發點，PJM 與 SCM 之間走相同的標準流程：

```
Step 1：PJM 產出 Material Request List (MRL)
        ↓
Step 2：MRL 提交給 SCM 生/物管團隊
        ↓
Step 3：SCM 回報每個料件的交期承諾 (ETA)
        ↓
Step 4：PJM 對照 Epic 時程，確認交期是否支援
        ↓
Step 5：若有衝突 → 觸發 Design 端 alternative 評估
        若無衝突 → SCM 正式下單
        ↓
Step 6：SCM 提供下單後追蹤窗口（料況週報）
```

### Material Request List (MRL) 必填欄位

PJM 提交給 SCM 的 MRL 必須包含以下欄位。**欄位不齊全的 MRL，SCM 應該打回，不要勉強處理**——這是雙方共同的紀律：

| 欄位 | 說明 | 範例 |
|------|------|------|
| Project Code | 對應 Initiative 代號 | ARRAY-26 |
| MRL Phase | POC / EVT / DVT | EVT |
| Required Date | 物料需到齊的最晚日期 | 2026-03-12 |
| Linked Epic | 哪個 Epic 會用到這批料 | EE-EVT 板級驗證 |
| Part Number | 廠商料號或內部料號 | (依料件) |
| Description | 料件描述 | LDO 3.3V, 2A, SOT-223 |
| Quantity | 需求數量（含 spare） | 30 pcs（5 套 × 6 顆 + 安全庫存） |
| Spec Reference | 規格書 / Datasheet 連結 | (Confluence link) |
| Alternative Acceptable? | 可否接受替代料 | Yes / No / 需 Design 評估 |
| PJM Contact | 主責 PJM 與職能 Lead | (簽核窗口) |

### SCM 交期承諾 (ETA Confirmation) 應回報內容

SCM 收到 MRL 後，必須在 **3 個工作天內**回報每一個料件的：

| 欄位 | 說明 |
|------|------|
| Source | 供應商 / 代理商 / 現貨商 |
| Lead Time | 標準交期 |
| ETA | 預計到貨日（含船期 / 通關時間） |
| Confidence | 高 / 中 / 低（信心度） |
| Risk Note | 潛在風險（缺料、價格大幅波動、停產風險等） |
| Cost Note | 單價與整批金額 |

**信心度低的料件必須特別標記**，PJM 就會知道「這個料雖然 ETA 是 3 月 5 日，但 SCM 沒把握」，從而觸發備援評估。

### 衝突的處理：當交期撐不到 Required Date

當 SCM 回報的 ETA 晚於 PJM 的 Required Date，**這是雙方共同的問題**，不是 SCM 的「壞消息」。處理流程：

第一步是**評估彈性**。PJM 先檢視 Required Date 是不是可以晚一週——很多時候硬體 Epic 排程有 buffer，挪一週不影響 DR Gate。

第二步是**評估替代料**。如果交期硬卡死，PJM + SCM + EE Lead 三方開短會（不超過 30 分鐘），決定：

- 用替代料（PJM 的 EE Lead 評估技術風險）
- 加價找現貨（SCM 評估成本影響、PJM 評估能否吸收）
- 改變 Epic 順序（PJM 重排，先做不需要該料件的 Epic）
- 動 Initiative 時程（升級到 VP 決策）

第三步是**書面化決策**。任何衝突的解決必須在 Jira 對應 Epic 留下 comment，記錄「2026-02-XX 因 LDO 3.3V 交期延遲 4 週，改用 替代料 PN-12345，EE Lead 已驗證電氣特性可接受」這類紀錄。**這是未來客戶查問或 RMA 追溯時的關鍵證據鏈**。

### Material Risk 進入 Risk Log

**所有信心度為「中」或「低」的料件，PJM 應在 Risk Log 建立對應的 Risk 項目**。這讓物料風險變成可監控的物件，而不是 SCM 一個人扛。Risk Log 範例：

> **Risk-005**: ARRAY-26 的 LDO 3.3V (PN-XXX) 交期信心度低
> - **Probability**: Medium
> - **Impact**: High（影響 EE-EVT 進度，連帶 DR2）
> - **Mitigation**: SCM 已在 1 月底開始追蹤兩家備援代理商
> - **Contingency**: 改用替代料 PN-12345（EE Lead 已預先評估通過）
> - **Owner**: SCM 窗口 + PJM
> - **Trigger**: ETA 確認延後超過 1 週 → 啟用備援

### PJM ↔ SCM 的固定節奏

除了專案啟動時的 MRL 提交，PJM 與 SCM 在執行中保持兩個固定節奏：

**每週料況週報（SCM 推送）：** 每週五，SCM 給 PJM 一份所有在途料件的狀態更新（已到 / 在途 / 延遲 / 風險）。PJM 把延遲訊號吸收進 Risk Log。

**月度 Material Sync（雙方共同）：** 每月一次 30 分鐘會議，PJM、SCM 生管、SCM 物管三方檢視未來 60 天的物料需求 vs. 供應預測，提早識別未來的衝突點。

**這個節奏是雙向的。** PJM 不只是「下單者」，要主動告訴 SCM 未來規劃；SCM 不只是「執行者」，要主動回報市場訊號（料況變化、價格波動、停產警告）。**Kickoff 前 PJM 與 SCM 要對齊這個節奏**，否則執行階段會變成單向施壓。

---

## 十、Kickoff 前的最後檢查清單

PJM 在 Kickoff 前應該對照這份清單確認：

- [ ] Initiative 已建立並填寫所有欄位
- [ ] 42 個 Epic 已建立並標好 Investment Category 與起訖日期
- [ ] 5 個 Phase Gate Epic 的 Exit Criteria 已寫定
- [ ] 至少 3 個範例 Epic 已展開到 Story 層級作為團隊參考
- [ ] Jira Project 權限與自動化規則已設定
- [ ] **所有職能 Lead（Tony / Vic / Ken / Andy / Silas / Ivy / Keith）已 review 過自己職能的 Epic 範圍並無異議**
- [ ] 客戶 POD 規範已附在 Initiative 描述中或連結到 Confluence
- [ ] 整合測試 Epic 與 dry run 計畫已被各 Lead 知道
- [ ] **POC 階段 MRL 已提交 SCM 並取得 ETA 確認**
- [ ] EVT / DVT 階段 MRL 的草稿已建立（即使尚未提交，先讓 SCM 預知）
- [ ] **Material Risk 已進入 Risk Log**
- [ ] PJM ↔ SCM 月度 Material Sync 排入雙方行事曆
- [ ] Initial Risk Log 已建立（至少 5–10 項初始 Risk）

倒數第 5 點最容易被跳過——Kickoff 變成 PJM 單向宣告。這是大忌。**Kickoff 前 1 週必須跟每個 Lead 1-on-1 過一次他們職能的 Epic 列表**，他們點頭你才能寫進 Jira。這個動作做扎實，後面 9 個月可以省下無數的「我以為不是我負責」的爭執。

---

## 收斂

到這裡，Project ARRAY-26 在 Kickoff 前的 Jira 結構完整建立。明天的 Day 3 將進入 Kickoff 之後——**起手式做扎實只是 30 分，執行治理才是另外 70 分**。我們會講五大類執行事件（Issue / Bug / Risk / CR / Dependency Slip）的處理流程，以及四層治理節奏。
