# Jack Wu 的開發經歷與職涯概覽

---

## 自我介紹

我是一名專業的 Java 軟體工程師，擁有多年後端系統開發與維護經驗，熟悉 Spring Boot、JSP、MySQL、MS SQL 及 Tomcat 等技術，並且具備跨領域的專案管理與客戶溝通能力。曾參與多個大型企業專案與醫療系統開發，具備良好的系統設計、程式優化與團隊合作經驗。

---

# 💼 軟體開發經歷總覽

本頁面彙整本人 4～5 年軟體開發經驗，涵蓋醫療資訊系統、政府與企業專案開發、SAP 整合與雲端服務應用等範疇。  
主要技術專長為 Java、Shell Script、自動化工具與跨系統整合，所有技術範例均已整理於本頁面與子頁面中（以 Markdown 形式記錄，無附程式碼檔案）。

## 🧩 技術專長

- **後端開發**：Java (Spring, JSP), SAP ABAP, Azure Function
- **資料庫設計與調校**：MS SQL、MySQL、Stored Procedure 優化
- **系統整合與自動化**：Batch Script、Shell Script、Tomcat、CI/CD 操作流程
- **API串接與外部服務應用**：Google Cloud Speech API、SAP Integration API
- **前端技術**：HTML, JavaScript, jQuery, CSS
- **文件輸出與檔案處理**：iText PDF 產生、SMB 檔案傳輸、檔案備份與檢查
- **環境維運與伺服器管理**：Linux、Windows Server、排程與日誌分析

---

## 📌 展示說明

> 💡 所有展示內容皆已經去識別化，為實務經驗中之功能模組與技術處理邏輯摘錄，**並無實際商業資料或病患資訊**。

由於部分內容涉及醫療系統、客戶平台架構與上傳申報機制，僅擇選具代表性且可公開的技術內容進行整理。

---

# 🧠 專案案例（Selected Technical Cases）

以下案例皆來自實務專案經驗，已進行去識別化與簡化，僅保留技術設計與問題解決邏輯。

---

## 🔧 Case 1：醫療影像批次上傳優化

### 📌 問題背景
- 舊系統批次上傳成功率僅約 65%～75%
- 常見問題：
  - 資料缺漏導致失敗
  - DB connection 未釋放造成資源耗盡
  - 無 retry 機制，失敗需人工補送

### 🛠 解決方案
- 重構批次處理流程
- 設計錯誤分類機制：
  - 可重試（Transient Error）
  - 不可重試（Data Error）
- 導入 retry 機制，提高自動修復能力
- 優化 DB connection 使用與釋放
- 增加 logging 與錯誤追蹤

### ⚙ 技術重點
- Java Batch Processing
- Transaction 控制
- Connection Pool 管理
- Logging / Error Handling 設計

### 🚀 成果
- 成功率提升至穩定 90% 並維持一年以上
- 大幅降低人工補送作業
- 提升系統穩定性與可維運性

---

## ⚙️ Case 2：Azure Functions + SAP Integration

### 📌 問題背景
- 傳統批次與 API 架構擴展性不足
- 系統間整合複雜，維運成本高

### 🛠 解決方案
- 導入 Azure Functions 建立 Serverless 架構
- 使用事件驅動（Event-driven）處理資料流
- 串接 SAP Integration API 進行資料交換
- 拆分模組，提升系統彈性

### ⚙ 技術重點
- Azure Functions（Serverless）
- RESTful API Integration
- SAP API 串接
- 非同步處理設計

### 🚀 成果
- 系統可用性提升至 99.9%
- 降低維運負擔與人工作業
- 提升擴展性與部署彈性

---

## 🛡 Case 3：檔案完整性驗證與自動還原（MD5）

### 📌 問題背景
- 系統檔案可能被異動但無法即時發現
- 缺乏完整性驗證機制

### 🛠 解決方案
- 設計 MD5 hash 機制，定期產生檔案指紋
- 自動比對異常檔案
- 發現異常時執行自動還原
- 搭配排程（cron / scheduler）定期執行

### ⚙ 技術重點
- Shell Script / Batch Script
- MD5 Hash 計算
- 排程與自動化處理
- 檔案系統操作

### 🚀 成果
- 可即時偵測檔案異常
- 降低系統被竄改風險
- 提升系統安全性與可靠性

---

## 📊 Case 4：Log 分析與效能監控工具

### 📌 問題背景
- 無法有效掌握系統回應時間與效能瓶頸
- Tomcat log 難以人工分析

### 🛠 解決方案
- 開發 Java 工具解析 Tomcat access log
- 計算平均回應時間與 SLA 指標（3 秒內比例）
- 輸出統計報表供維運與優化參考

### ⚙ 技術重點
- Java Log Parsing
- 檔案處理（File I/O）
- 效能指標計算（Latency / SLA）
- 數據分析邏輯設計

### 🚀 成果
- 快速掌握系統效能狀況
- 提供優化依據，提升使用者體驗
- 降低問題排查時間

---

## 🧩 工作經歷摘要

### 1. 中山大學 (2025/1 ~ 2025/12)
> 主導開發校內運動中心系統，完成前後端與權限分級，提升系統管理效率30%。
- 透過 JSON properties 自建 i18n 多語系，有效擺脫外部套件依賴，達成無刷新頁面語系切換的流暢體驗，大幅降低依賴耦合與提升未來設計的靈活性。
- 將原本人工流程系統化，提升整體營運效率與系統穩定性。
  
### 2. 資旅軟體開發有限公司（2023/4 ~ 2024/3）

> 負責 SAP 與 Azure 平台相關開發與整合，處理商業邏輯與系統串接需求。

- SAP 程式更新與修正（Stored Procedure 優化）
- 開發 Azure Function 與 SAP Integration API 串接
- Hybris 系統功能增修與維護 
🛠 `#Java #Spring #SAP #AzureFunction #SQL`

### 3. 碩網（2021/9 ~ 2023/2）

> 參與政府、企業與日本客戶專案，負責前後端整合、資料串接、自動化流程設計與維運。

- Robot 對話平台功能開發（TTS、SST、搜尋、稽催、滿意度填寫）
- 自動化工具開發（log 分析、MD5 驗證、Shell 腳本）
- Server 壓力測試與問題追蹤
- 批次資料處理與格式匯出（ODS/XLS/PDF）
🛠 `#Java #Batch #GoogleCloudSpeech #LogAnalysis`

### 4. 高雄榮民總醫院（2018/10 ~ 2021/4）

> 擔任醫療單位核心系統開發與維運角色，跨科室合作實作臨床報告、病歷整合與上傳批次作業。

- 精神科病歷、病程系統設計與調閱功能
- 心理、職能、社工報告電子化，整合電子病歷
- 中醫藥囑查詢系統、藥師排班模組
- 醫療影像批次上傳程式重構（合格率由 65~75% 提升至穩定 90%）
- 研究計畫系統移轉與伺服器管理
🛠 `#Java #醫療資訊 #PDF匯出 #SMB儲存`

---
  
## 📘 功能與模組展示（已寫於 `.md` 文件中）

📦 功能模組（Supporting Modules）

以下為實務專案中實作之功能模組：

- 病患資料查詢模組  
  使用 AJAX 與後端 API 整合，實現動態資料填入與表單同步

- SMB 檔案存取模組  
  根據業務邏輯組合路徑，實現跨伺服器檔案讀取（PDF / Excel）

- PDF 輸出模組  
  使用 iText 將 HTML 表單轉換為 PDF，並處理檔案儲存與命名規則

- 醫療影像批次維護模組  
  處理資料缺漏與錯誤，提升批次成功率（詳見專案案例）

- 語音播放模組（TTS）  
  整合 Google Cloud Speech API，實現語音生成與前端播放

- 檔案完整性校驗模組  
  使用 MD5 + Shell Script 建立自動檢查與異常還原機制

- Log 分析工具  
  解析 Tomcat log，計算回應時間與 SLA 指標

---

## 📄 如何使用本 Repository

本專案使用 Markdown 方式完整撰寫所有展示內容：  
你可直接瀏覽以下 `.md` 檔案獲取各模組功能與程式邏輯：

- [`HospitalSystemFeatures.md`](./experiences/hospital/HospitalSystemFeatures.md)
- [`WorkExperience_BotAndScript.md`](./experiences/bot/WorkExperience_BotAndScript.md)
- [`README.md`](./README.md)

---

## 📎 最後說明

> 🔒 本頁展示內容均不涉及任何實體商業資料、病患資訊或專案原始碼，所有程式碼片段與流程內容皆為簡化與去識別化版本，**僅供技術能力展示與經歷參考**。
## 聯絡方式

- Email: 4980d103@gmail.com  
- GitHub: https://github.com/jackwu55  

---

