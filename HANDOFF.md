# Handoff — P009 穿越跳石陣・環形燈條模式（v3.5）

- 更新時間：2026-10-18T03:25+08:00
- 更新者：Claude（逐檔讀原始碼，撰寫專案交接）
- 倉庫：https://github.com/jackeytw886/Champagne
- 分支：main
- 本次基準 SHA：`e9958bdb032b15e8f31771dab9305d3e9a527bfb`（Codex 管理整理提交）
- 核對層級：已讀全部原始碼；已執行語法檢查與無頭瀏覽器冒煙測試（見 docs/VERIFICATION.md）；**未接實體跳石陣測試**。

## 1. 這是什麼

實體互動裝置「穿越跳石陣」的**網頁模擬器**（單檔 HTML）。地上 10 排 × 3 列共 30 顆跳石，每顆跳石有一圈 WS2812 環形燈條；遊戲隨機選一條路線亮藍色，玩家要在 90 秒內只踩藍色跳石走到終點，踩到其他跳石算錯。網頁用滑鼠點跳石代替腳踩，用來展示玩法、調整燈效與語音內容。

- 線上網址：https://jackeytw886.github.io/Champagne/ （GitHub Pages）
- 版本：v3.5（8 種路線、倒數後直接開始、每 20 秒進度提醒）
- 相關：P010（Melody，同一遊戲的 v3.8「猜旋律」版）、P026（同時段建立的空倉庫）。實體版韌體為 Arduino Mega 單板 30 通道（`jumpstone_single_mega_30ch_v2.ino`，**不在此倉庫**）。

## 2. 檔案

只有 `index.html`（約 67 KB、2,230 行），無外部程式庫、無網路需求（可離線）。行號索引見 docs/FILE-MAP.md。

## 3. 程式怎麼運作

| 部分 | 位置／名稱 | 說明 |
| --- | --- | --- |
| 設定 | `ROWS=10`、`COLS=3`、`TOTAL_TIME=90`、`WARN_60_AT/30/10`、`PROGRESS_HINT_INTERVAL=20` | 改尺寸或時間從這裡開始 |
| 路線 | `PATH_THEMES`（8 條：銀河漫遊、神龍擺尾、渡海尋寶、閃電快線、森林小徑、月影折返、雙峰攀登、螢火蟲迴路） | 每條是 10×3 的 0/1 矩陣＋名稱、說明、開場語音；`generatePath()` 隨機選一條 |
| 狀態 | `GAME`（`phase`、`path`、`pathSet`、`steppedSet`、`redHits`、`errorCount`、`timeLeft`、`flowId`…） | `phase`：`idle → countdown → playing → result` |
| 燈環繪製 | `setStoneRing(r, c, color, intensity)` | **唯一**改變跳石外觀的入口（v3 修正後的規則），只改 border 與 box-shadow |
| 角色 | `setStoneRole(stone, role)`、`breathingLoop()` | `gold`（已踩對）、`hit-red`（踩錯）等角色由呼吸迴圈統一動畫 |
| 流程控制 | `newFlow()`、`isCurrentFlow()`、`waitForFlow()`、`beginEffect()`／`finishEffect()` | 用 flowId 取消舊流程，避免重設、示範特效、倒數、結算動畫互相重疊 |
| 語音 | `speak()`（Web Speech API，優先 zh-TW）、`setSubtitle()` | 字幕同步顯示 |
| 音效 | `SFX`（Web Audio 合成） | 倒數嗶聲、踩對、踩錯、勝利、時間到 |
| 特效 | `fxChaos`…`fxMatrix` 共 16 種 | 面板可單獨示範（`demoEffect(name)`） |

### 遊戲規則（`startGame()`、`onStoneClick()`、`endGame()`）

1. 按開始 → 歡迎語音 → 5～1 倒數（跳石上畫數字）→ 亮出路線並宣布路線名稱 → 開始計時。
2. 踩藍色（路線上）＝變金色並加進度；踩到非路線＝變紅、錯誤數 +1、語音報錯誤次數。
3. 每 20 秒提醒還剩幾顆藍色；剩 60／30／10 秒時語音警告。
4. 結算：全部踩完＝勝利（0 錯「完美探險家」＋皇冠動畫；1～2 錯「勇敢挑戰者」＋獎盃；3 錯以上「不放棄的小英雄」＋星星）；時間到＝失敗（愛心動畫）。

## 4. 與實體裝置的對應

- 30 顆跳石＝實體 30 個通道（腳踏感應＋WS2812 燈環），網頁的 (r, c) 對應韌體的通道編號規則**未在此倉庫記錄**，接實體前要和韌體核對。
- 網頁的語音句子對應實體 CF9510M 播放的 MP3；本版沒有檔案對照表（P010 有 `CF_FILE_MAP` 模擬層可參考）。
- 這個網頁**沒有** WebSerial／Arduino 連線，純模擬。

## 5. 已知問題與待辦

- 實體韌體、接線與音檔清單不在倉庫，若要整合需另行補上（docs/HARDWARE.md、docs/PROTOCOL.md）。
- 語音依賴瀏覽器內建中文語音，不同電腦音色不同；展場若沒有 zh-TW 語音會改用其他中文語音。
- 與 P010 是複製後各自修改的兩個檔案，修正 bug 時要考慮是否兩邊都改。

## 6. 測試方式

直接用瀏覽器開 `index.html`。自動化測試可在頁面中讀 `GAME.phase`、`GAME.path`、`GAME.steppedSet`，點擊 `.stone` 元素模擬踩踏（`data-r`、`data-c`）。

## 7. 修改時注意

- 新特效要經過 `setStoneRing()`，並用 `beginEffect()`／`finishEffect()` 包住，否則會和遊戲流程互搶。
- 新增非同步流程時，每個 `await` 後都要檢查 `isCurrentFlow(flowId)`。

## 8. 版本沿革

- 2026-05-16：經 EZPage 部署 v3.5。
- 2026-10-17：Codex 管理整理（代號 P009、AI 入口文件）。
- 2026-10-18：Claude 補寫本交接。

## 版本定位

基準 SHA 定位本次文件修改前的程式；本次文件提交由此檔所在的 Git 提交與倉庫歷史定位，不預填同一提交自己的 SHA。
