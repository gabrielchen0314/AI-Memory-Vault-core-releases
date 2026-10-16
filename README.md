# Espalio Memory — 記憶核心（安裝版發布）

**Espalio Memory**（記憶核心，原名 AI Memory Vault）是給 AI 助手（Claude Code、VS Code、Cursor、Codex、Claude 桌面 App…）用的**長期記憶**：
把知識存成 Markdown，建語意索引，透過 MCP 讓 AI 讀寫與搜尋。在本機執行，不需要雲端服務。

> 本 repo 只放**安裝檔與更新資訊**（Release、`latest.json`、使用指南），不放原始碼。
> 本檔由主 repo 的 `AI_Engine/packaging/release-readme.md` 於每次發布時同步，請勿在這裡直接編輯。
>
> 5.1.0 起產品名改為 Espalio Memory。安裝檔檔名、安裝目錄 `C:\Program Files\AI Memory Vault`、開始功能表群組「AI Memory Vault」、
> 執行檔與設定都沒改，下文照實際名稱寫。

---

## 同一組的產品

| 產品 | 取得 | 需要嗎 |
|---|---|---|
| **Espalio Memory**（記憶核心，本 repo） | 5.0 起的 Release：記憶的寫入、語意搜尋、索引、自我更新 | ✅ 必裝 |
| **Espalio Workflow**（工作流插件，原名 Vault Workflow） | [AI-Memory-Vault-workflow-releases](https://github.com/gabrielchen0314/AI-Memory-Vault-workflow-releases)：收工、晨報、日／週／月報與排程、Ai-Debate、starter pack | 選用，要先裝核心 |
| **Espalio**（平台） | 選用；取得方式見 Espalio 的發布說明。裝了之後由它啟動、看護核心，編輯器只需註冊一個 MCP 入口 | 選用 |
| 4.x 舊版（拆分前的單一版本） | [AI-Memory-Vault-releases](https://github.com/gabrielchen0314/AI-Memory-Vault-releases)，**已凍結、不再更新** | ❌ |

## 系統需求與相依

- Windows 10 以上、64 位元
- **不需要 Python**：安裝檔已內含執行環境
- 需要支援 MCP 的編輯器（Claude Code、VS Code、Cursor、Codex、Claude 桌面 App…）
- 第一次啟動需要連網（下載語意搜尋用的嵌入模型，之後離線可用）
- 工作流插件與 Espalio 都是選用，只裝核心就有完整的記憶功能

## 快速開始

1. **下載並安裝**：到 [Releases](../../releases/latest) 下載 `AI-Memory-Vault-Setup-v<版本>.exe` 並執行。
   - 沒裝 Espalio：保留「登入時自動啟動 MCP SSE Server」的勾選——編輯器經由它連線，斷線時看護排程會把它拉回來。
   - 裝了 Espalio：核心由 Espalio 啟動與看護，上一條建議不適用，依 Espalio 的說明。
2. **設定**：開始功能表 → **AI Memory Vault → 環境設定**，設定知識庫（Vault）放哪裡、回應語言。
3. **接上編輯器**：
   - **裝了 Espalio**：編輯器只註冊 Espalio 的 `espalio` 這一個 MCP 入口（註冊方式見 Espalio 的說明）。
     記憶工具在那裡帶 `memory_` 前綴：`memory_recall`、`memory_read`、`memory_remember`…
   - **只裝核心**：MCP 設定指向轉接器 `C:\Program Files\AI Memory Vault\vault-bridge\vault-bridge.exe`。以 Claude Code 為例：

     ```powershell
     claude mcp add --scope user ai-memory-vault -- "C:\Program Files\AI Memory Vault\vault-bridge\vault-bridge.exe"
     ```

     接好之後工具清單會出現 8 支：`recall`、`read`、`remember`、`update`、`forget`、`rename`、`context`、`sync`。

   其他編輯器（VS Code、Cursor、Claude 桌面 App、Codex）的寫法見 **[USAGE.md](USAGE.md)**。

> ⚠️ Claude 桌面 App 與 Antigravity **開著時會把設定檔整份寫回**，改設定前要先完全結束它們（系統匣 → 結束）。

## 升級

**5.x 之間**：直接執行新版安裝檔即可。它會裝進原本的目錄，設定、索引、Vault 都沿用。
5.1.0 只改產品名，安裝路徑、執行檔名、設定都不變。

裝好之後，核心每 6 小時檢查一次本 repo 的 `latest.json`，有新版時跳通知，按下去就會下載、驗證雜湊並安裝。
插件有自己的更新通道，兩者互不影響。

## 從 4.x 升級

4.x 的自動更新**不會**把你升到 5.x——5.0 起記憶核心與個人工作流拆成兩個產品，只裝核心會讓收工、排程、
Ai-Debate 靜默停止，所以刻意改成手動升級：

1. 安裝本 repo 的 `AI-Memory-Vault-Setup-v5.*.exe`
2. 要保留收工／晨報／排程／Ai-Debate → 安裝 [Espalio Workflow 工作流插件](https://github.com/gabrielchen0314/AI-Memory-Vault-workflow-releases)
3. 完全結束 Claude 桌面 App 與 Antigravity，從外部開的 PowerShell 執行插件附的一鍵遷移（先預覽）：

   ```powershell
   powershell -NoProfile -ExecutionPolicy Bypass -File "C:\Program Files\Vault Workflow\scripts\migrate-to-split.ps1" -DryRun
   ```

   確認無誤後拿掉 `-DryRun` 再跑一次。每一處改動都會留下還原腳本。

## 解除安裝

Windows「設定 → 應用程式」裡的 Espalio Memory（5.0.x 顯示為 AI Memory Vault），或開始功能表「解除安裝 AI Memory Vault」。
程式目錄會移除；`%APPDATA%\AI-Memory-Vault\` 的設定、索引與 log，以及你的 Vault 都保留。
沒裝 Espalio 時，先移除看護排程（解除安裝不會自動移除，步驟見 [USAGE.md](USAGE.md)）。

## 文件

- **[USAGE.md](USAGE.md)**：完整使用指南（各編輯器設定、常駐 server 與看護、升級與解除安裝、常見問題）
- 每一版改了什麼：看該版 [Release](../../releases) 頁面的說明

## 問題回報

請開 [Issue](../../issues)，附上版本號（`http://127.0.0.1:8765/version` 的回應）與
`%APPDATA%\AI-Memory-Vault\` 底下相關的 log（`sse-server.log`、`sse-watchdog.log`）。
