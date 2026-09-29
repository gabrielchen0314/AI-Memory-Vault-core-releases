# AI Memory Vault — 記憶核心（安裝版發布）

給 AI 助手（Claude Code、VS Code、Cursor、Codex、Claude 桌面 App…）用的**長期記憶**：
把知識存成 Markdown，建語意索引，透過 MCP 讓 AI 讀寫與搜尋。在本機執行，不需要雲端服務。

> 本 repo 只放**安裝檔與更新資訊**（Release、`latest.json`、使用指南），不放原始碼。
> 本檔由主 repo 的 `AI_Engine/packaging/release-readme.md` 於每次發布時同步，請勿在這裡直接編輯。

---

## 同一組的發布 repo

| Repo | 內容 | 需要嗎 |
|---|---|---|
| **AI-Memory-Vault-core-releases**（本 repo） | 記憶核心 5.0 起：記憶的寫入、語意搜尋、索引、自我更新 | ✅ 必裝 |
| [AI-Memory-Vault-workflow-releases](https://github.com/gabrielchen0314/AI-Memory-Vault-workflow-releases) | Vault Workflow 插件：收工、晨報、日／週／月報與排程、Ai-Debate、starter pack | 選用，要先裝核心 |
| [AI-Memory-Vault-releases](https://github.com/gabrielchen0314/AI-Memory-Vault-releases) | 4.x 舊版（拆分前的單一版本），**已凍結、不再更新** | ❌ |

## 系統需求

- Windows 10 以上、64 位元
- 第一次啟動需要連網（下載語意搜尋用的嵌入模型，之後離線可用）

## 快速開始

1. **下載**：到 [Releases](../../releases/latest) 下載 `AI-Memory-Vault-Setup-v<版本>.exe` 並執行。
   保留「登入時自動啟動 MCP SSE Server」的勾選——編輯器經由它連線，斷線時看護排程會把它拉回來。
2. **設定**：開始功能表 → **AI Memory Vault → 環境設定**，設定知識庫（Vault）放哪裡、回應語言。
3. **接上編輯器**：MCP 設定指向轉接器
   `C:\Program Files\AI Memory Vault\vault-bridge\vault-bridge.exe`。以 Claude Code 為例：

   ```powershell
   claude mcp add --scope user ai-memory-vault -- "C:\Program Files\AI Memory Vault\vault-bridge\vault-bridge.exe"
   ```

   其他編輯器（VS Code、Cursor、Claude 桌面 App、Codex）的寫法見 **[USAGE.md](USAGE.md)**。

接好之後，編輯器的工具清單會出現 8 支：`recall`、`read`、`remember`、`update`、`forget`、`rename`、`context`、`sync`。

> ⚠️ Claude 桌面 App 與 Antigravity **開著時會把設定檔整份寫回**，改設定前要先完全結束它們（系統匣 → 結束）。

## 從 4.x 升級

4.x 的自動更新**不會**把你升到 5.0——5.0 起記憶核心與個人工作流拆成兩個產品，只裝核心會讓收工、排程、
Ai-Debate 靜默停止，所以刻意改成手動升級：

1. 安裝本 repo 的 `AI-Memory-Vault-Setup-v5.*.exe`
2. 要保留收工／晨報／排程／Ai-Debate → 安裝 [Vault Workflow 插件](https://github.com/gabrielchen0314/AI-Memory-Vault-workflow-releases)
3. 完全結束 Claude 桌面 App 與 Antigravity，從外部開的 PowerShell 執行插件附的一鍵遷移（先預覽）：

   ```powershell
   powershell -NoProfile -ExecutionPolicy Bypass -File "C:\Program Files\Vault Workflow\scripts\migrate-to-split.ps1" -DryRun
   ```

   確認無誤後拿掉 `-DryRun` 再跑一次。每一處改動都會留下還原腳本。

## 自動更新

裝好之後，核心每 6 小時檢查一次本 repo 的 `latest.json`，有新版時跳通知，按下去就會下載、驗證雜湊並安裝。
插件有自己的更新通道，兩者互不影響。

## 文件

- **[USAGE.md](USAGE.md)**：完整使用指南（各編輯器設定、常駐 server 與看護、第一次啟動、常見問題）
- 每一版改了什麼：看該版 [Release](../../releases) 頁面的說明

## 問題回報

請開 [Issue](../../issues)，附上版本號（`http://127.0.0.1:8765/version` 的回應）與
`%APPDATA%\AI-Memory-Vault\` 底下相關的 log（`sse-server.log`、`sse-watchdog.log`）。
