# File Map — P009 檔案地圖

核對基準：`da5f8e9a457fabc9d609ab3cea99b6c4cafff51b`。下表列出整理前檔案，大小來自 Git tree；新增管理文件另列於文末。

| 路徑 | 大小（bytes） | 類型 |
| --- | ---: | --- |
| [README.md](../README.md) | 60 | md |
| [index.html](../index.html) | 67422 | html |

## 管理入口

README.md、AGENTS.md、HANDOFF.md、PROJECT.json、CLAUDE.md、GEMINI.md、.github/copilot-instructions.md、docs/VERIFICATION.md。

## 檔內結構（行號索引，2026-10-18 Claude 依基準 `e9958bd` 整理）

只讀需要的段落；標 ⚠️ 的是超長單行資料，不要整行載入。

| 約略行號 | 內容 |
| ---: | --- |
| 7–549 | `<style>` |
| 551–671 | HTML 介面（跳石陣格子、狀態列、特效示範按鈕、結算視窗） |
| 672–2227 | 主程式 `<script>` |
| 672–690 | 版本說明（v3.5 修正項目） |
| 691–697 | 設定：`ROWS`、`COLS`、`TOTAL_TIME`、警告秒數、提醒間隔 |
| 699–833 | 語音 `speak()`、字幕、音效 `SFX` |
| 834–845 | 倒數數字字型 |
| 846–897 | `PATH_THEMES`：8 條路線 |
| 900–940 | `GAME` 狀態與 DOM 參照 |
| 941–1115 | 燈環繪製 `setStoneRing()`、建立格子、呼吸迴圈、角色、流程取消 |
| 1116–1230 | 字幕、覆蓋層、路線生成與繪製 |
| 1231–1898 | 16 種燈效 `fxChaos`…`fxMatrix` |
| 1899–1929 | `demoEffect()` 特效示範 |
| 1930–2059 | 遊戲流程：`startGame()`、`tickTimer()`、進度提醒 |
| 2060–2107 | `onStoneClick()` 踩踏判定 |
| 2108–2227 | `endGame()` 結算、關閉視窗、重設 |
