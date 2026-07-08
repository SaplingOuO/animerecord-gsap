# 📌 AnimeRecord-GSAP - 前端視覺互動與多媒體 API 防禦實驗室

這個專案是一個專注於**前端極致視覺特效與跨來源影音整合**的技術研發實驗室（R&D Sandbox）。專案的最初核心目標是挑戰國際頂尖網頁（如 Palmer Dinnerware）的沉浸式無滾動條互動體驗，並在使用 **GSAP (GreenSock Animation Platform)** 探索物理動態的過程中，逐步演進為一套結合 **YouTube IFrame API** 與**自動降級防禦機制**的影音整合系統。

雖然部分高級滾動功能受限於商業授權（GSAP 核心插件付費限制）與跨裝置拉動極限而策略性停擺，但本專案完整記錄了前端工程師在面對技術瓶頸時的評估思維，以及對第三方 API 數據清洗與強韌性設計的深度實踐。

---

## 🛠️ 核心研發技術棧 (Tech Stack)

* **動畫與視覺引擎**：GSAP 3 (包含 Draggable 插件)、Three.js (動態粒子儲備)
* **多媒體串接**：YouTube Data API v3 & YouTube IFrame Player API
* **數據處理**：Axios & 原生 JavaScript 集合演算法 (`Set`)
* **前端框架**：Vue 3 (Composition API `<script setup>`)
* **字型視覺**：@fontsource/orbitron (科幻數位感字體)

---

## 🚀 核心技術亮點與防禦性設計 (Engineering Highlights)

### 1. 影音串接層的「多層級防禦與自動降級機制」(`youtube.js` + `BackgroundVideo.vue`)
在處理動態影音背景時，外部 API 的不確定性（如影片被刪除、版權限制、區域鎖定）極易導致前端報錯。為此我設計了一套嚴謹的**防禦性降級（Fallback）架構**：
* **單例全域載入**：實作 `loadYoutubeAPI()`，確保 YouTube IFrame API 在全域環境中**只會被載入一次**，避免組件重複掛載時造成的效能浪費。
* **地理區域與權限檢查**：利用 YouTube Data API v3，在影片渲染前帶入 `regionCode: "TW"`，深度檢查該動漫預告片在台灣地區是否被限制播放、或是否被作者禁用嵌入（Embed）。
* **無縫視覺降級**：當 `checkYoutubeVideo()` 偵測到影片不可播放時，下游組件 `BackgroundVideo.vue` 會在 `catch` 或條件分支中**自動切換為靜態隨機動漫圖片背景**。此機制確保了不論外部 API 發生何種波動，使用者畫面絕對不會崩潰或留白。

### 2. 獨立資料處理層與動態清洗 (`gamerAcg.js`)
專案雖採用本地靜態 JSON，但為了確保組件間的資料流足夠扁平與乾淨，獨立抽離了 `gamerAcg.js` 服務層：
* **高效去重 (`Set`)**：利用 `new Set()` 演算法，從成百上千條歷史動畫資料中，秒級提取出不重複的「年份清單」供側邊欄使用（`getAllYears`）。
* **動態時間排序**：利用 `startsWith(year + '年')` 進行精準的字串調配，並在前端即時執行 `new Date()` 的時間差排序（`sort`），展現良好的資料邏輯處理能力。

### 3. GSAP Draggable 物理拉動與吸附實驗 (`CardList.vue` + `YearList.vue`)
為打破傳統網頁的滾動束縛，專案在 `App.vue` 中大膽宣告 `overscroll-behavior: none` 與 `touch-action: none`，完全接管瀏覽器的原生滾動。
* **中央線吸附演算法 (`getSnapPoints`)**：在底部橫向 `CardList` 與側邊 `YearList` 中，引入 GSAP `Draggable` 與 `InertiaPlugin`。透過動態計算螢幕中線（`window.innerWidth / 2`）與每個 HTML 元素節點的相對距離，嘗試計算出物理吸附點（Snap Points），營造出如同實體轉盤般的微互動干預。

---

## 📈 開發反思與技術止損 (R&D Lessons)

這個專案雖然最終未完全達到 Palmer Dinnerware 的完美動態，但對我而言是一次極具價值的**技術評估（Technical Assessment）實戰**：

1. **商業授權與開源邊界**：在研發中後期發現，要達到該網站極度滑順的「無縫滾動修飾」與「慣性物理緩動」，必須付費升級為 GSAP 商業會員以取得特定的進階插件。這讓我學會了在專案前期進行**技術選型時，必須將「授權成本」納入核心評估指標**。
2. **完美主義與技術止損**：拉動功能在跨裝置適配上出現計算邊界溢出的問題。在評估時間成本後，我決定保留已研發成功的 YouTube API 防禦層、API 單例模式與資料處理邏輯，將本專案封存為「技術實驗室」，並將學到的非同步防禦思維與 API 封裝技巧，成功反哺到後續的專案（如 `animerecord-vite`）中。