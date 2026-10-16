# Espalio Memory — 使用指南（安裝版）

> 這份是給**下載安裝包來用**的人。想從原始碼架設 / 開發 → 見主 repo 的 `README.md` 與 `USAGE.md`。
>
> 本檔由 `AI_Engine/packaging/publish-update.ps1` 於每次發布時自動推送至發布 repo，
> **請勿在發布 repo 直接編輯**——那裡的版本會在下次發布時被覆蓋。
> 要修改請改主 repo 的 `AI_Engine/packaging/release-usage.md`。

---

## 0. 先分清楚產品

**Espalio Memory**（記憶核心）就是原本的 AI Memory Vault，5.1.0 起改名。安裝檔檔名、安裝目錄 `C:\Program Files\AI Memory Vault`、
開始功能表群組「AI Memory Vault」、執行檔與設定都沒改，下文照實際名稱寫。

| 產品 | 做什麼 | 取得 | 必裝？ |
|---|---|---|---|
| **Espalio Memory**（記憶核心） | 記憶的寫入、語意搜尋、索引、自我更新 | [AI-Memory-Vault-core-releases](https://github.com/gabrielchen0314/AI-Memory-Vault-core-releases/releases/latest) | ✅ |
| **Espalio Workflow**（工作流插件，原名 Vault Workflow） | 收工、晨報、日／週／月報與排程、Ai-Debate、starter pack、Profile 切換 | [AI-Memory-Vault-workflow-releases](https://github.com/gabrielchen0314/AI-Memory-Vault-workflow-releases/releases/latest) | 選用 |
| **Espalio**（平台） | 啟動並看護核心與插件、觸發它們的排程；編輯器只需註冊一個 MCP 入口 | 選用；取得方式見 Espalio 的發布說明 | 選用 |

只要記憶功能 → 只裝核心。要個人工作流 → **先裝核心，再裝插件**（插件的安裝檔會檢查核心版本，低於 5.0 會拒絕安裝）。

## 1. 安裝

需要：Windows 10 以上、64 位元；支援 MCP 的編輯器。**不需要 Python**（安裝檔已內含執行環境）。工作流插件與 Espalio 都是選用。

執行 `AI-Memory-Vault-Setup-<版本>.exe`，預設安裝到 `C:\Program Files\AI Memory Vault`。
安裝精靈與 Windows「應用程式」清單顯示的名稱是 Espalio Memory。

「登入時自動啟動 MCP SSE Server」的勾選：

- **沒裝 Espalio**：**保留勾選**。編輯器經由常駐的 server 連線，也靠它的看護排程在斷線時自己回來。
- **裝了 Espalio**：核心由 Espalio 啟動與看護，上一條建議不適用，依 Espalio 的說明。

## 2. 首次設定

開始功能表 → **AI Memory Vault → 環境設定**，依序問：

1. **Vault 路徑**——知識庫要放哪（建議放在有備份的磁碟）
2. **回應語言**——預設 `zh-TW`

重跑也安全：每一題按 Enter 保留方括號內的現值，其他設定不會動。
設定檔在 `%APPDATA%\AI-Memory-Vault\config.json`。

> 安裝程式**不會**把執行檔加進 PATH。命令列要用完整路徑，例如
> `& "C:\Program Files\AI Memory Vault\vault-cli.exe" --setup`。

## 3. 接上編輯器（MCP）

### 裝了 Espalio

編輯器只註冊 Espalio 的 `espalio` 這**一個** MCP 入口，註冊方式見 Espalio 的說明。記憶工具在那裡帶 `memory_` 前綴：
`memory_recall`、`memory_read`、`memory_remember`、`memory_update`、`memory_forget`、`memory_rename`、`memory_context`、`memory_sync`。
這時**不要**再另外註冊下面的 `vault-bridge.exe`，否則同一組工具會以兩種名稱各出現一次。

### 只裝核心

編輯器指向**轉接器** `vault-bridge.exe`（輕量、不載入模型；記憶核心常駐在背景，多個編輯器共用一份）：

```
C:\Program Files\AI Memory Vault\vault-bridge\vault-bridge.exe
```

> 沒裝 Espalio、但裝了工作流插件的話，改指插件的 gateway（見插件的使用指南）。

**Claude Code**

```powershell
claude mcp add --scope user ai-memory-vault -- "C:\Program Files\AI Memory Vault\vault-bridge\vault-bridge.exe"
```

**VS Code**（`%APPDATA%\Code\User\mcp.json`）

```json
{
  "servers": {
    "ai-memory-vault": { "type": "stdio", "command": "C:/Program Files/AI Memory Vault/vault-bridge/vault-bridge.exe" }
  }
}
```

**Cursor**（`%USERPROFILE%\.cursor\mcp.json`）與 **Claude 桌面 App**（`%APPDATA%\Claude\claude_desktop_config.json`）

```json
{
  "mcpServers": {
    "ai-memory-vault": { "command": "C:/Program Files/AI Memory Vault/vault-bridge/vault-bridge.exe" }
  }
}
```

**Codex**（`%USERPROFILE%\.codex\config.toml`）

```toml
[mcp_servers.ai-memory-vault]
command = 'C:\Program Files\AI Memory Vault\vault-bridge\vault-bridge.exe'
```

> ⚠️ 安裝時若改過目錄，路徑要換成實際位置。`ai-memory-vault` 只是設定裡的名稱，可以自己取。
> ⚠️ **Claude 桌面 App 與 Antigravity 開著時會把設定檔整份寫回**：請先完全結束（系統匣 → 結束）再改，否則重開後又變回舊的。

接好之後，編輯器的工具清單會出現 8 支：`recall`、`read`、`remember`、`update`、`forget`、`rename`、`context`、`sync`。

## 4. 從 4.x 升級（拆分前的單一版本）

5.0 起記憶核心與個人工作流拆成兩個產品。**4.x 的自動更新不會把你升到 5.x**（刻意的：只裝核心會讓收工、排程、
Ai-Debate 靜默停止）。要升級請手動照順序做：

1. 安裝 `AI-Memory-Vault-Setup-v5.*.exe`（偵測到 4.x 且沒裝插件時，會先說明哪些功能會搬走並讓你確認）
2. 要保留收工／晨報／排程／Ai-Debate → 安裝工作流插件的 `Vault-Workflow-Setup-v*.exe`
3. **完全結束 Claude 桌面 App 與 Antigravity**，從外部開的 PowerShell 執行插件附的一鍵遷移（先預覽）：

   ```powershell
   powershell -NoProfile -ExecutionPolicy Bypass -File "C:\Program Files\Vault Workflow\scripts\migrate-to-split.ps1" -DryRun
   ```

   確認沒問題後拿掉 `-DryRun` 再跑一次。它會遷移設定檔、把編輯器與排程改接插件，每處改動都留還原腳本。
   結束碼 2 代表有 App 還開著、那個檔先跳過了——關掉後重跑即可。

只裝核心、不裝插件也可以：記憶功能完整；舊的收工／晨報排程會找不到插件而停止，編輯器接回 `vault-bridge.exe`（見第 3 節）。

## 5. 常駐 server 與看護

記憶核心以 SSE server 常駐在 `127.0.0.1:8765`，所有編輯器共用。

### 沒裝 Espalio

安裝時勾選「登入時自動啟動 MCP SSE Server」，安裝程式會做兩件事：

| 項目 | 觸發 | 管什麼 |
|---|---|---|
| 啟動資料夾捷徑 `AI Memory Vault SSE Server` | 登入時 | 開機後有 server |
| 排程 `Vault-SseServer-Watchdog` | 每 5 分鐘 | 死了會自己回來 |

沒勾自啟、事後想補：`& "C:\Program Files\AI Memory Vault\scripts\register-sse-autostart.ps1"`。
不帶參數時它註冊兩支排程：`Vault-SseServer-AtLogon`（登入時啟動，取代捷徑的角色）與 `Vault-SseServer-Watchdog`；
已有啟動資料夾捷徑、只缺看護時加 `-WatchdogOnly`；加 `-Unregister` 是移除這些排程。
`vault-bridge.exe` 發現 server 不在時，也會自己觸發看護（或登入啟動）排程。

server 內建記憶維護定時器（索引同步、備份、lesson 衰減、更新檢查），電腦關著錯過的會在下次啟動後補跑。

### 裝了 Espalio

核心由 Espalio 啟動與看護，維護排程也由 Espalio 觸發（核心內建的定時器不跑）。上面的登入自啟（啟動資料夾捷徑或
`Vault-SseServer-AtLogon`）與 `Vault-SseServer-Watchdog` 都不適用，依 Espalio 的說明處理，不要自己重新建立或啟用。
核心安裝檔升級時，只要啟動資料夾捷徑還在，就會重新拉起 SSE server 並重新註冊 `Vault-SseServer-Watchdog`，升級後依 Espalio 的說明確認它們的狀態。

## 6. 第一次啟動會發生什麼

- **第一次需要連網**：語意搜尋用的嵌入模型（`paraphrase-multilingual-MiniLM-L12-v2` 的 ONNX 版）在第一次啟動時
  從 Hugging Face 下載並快取，之後離線可用。
  這段期間編輯器可能顯示連線中，等它下載完即可。
- **Vault 裡原本就有筆記**：啟動後的維護會把它們收進索引；想立刻搜得到，在編輯器裡呼叫一次 `sync`。

## 7. 產出的執行檔

| 執行檔 | 用途 |
|--------|------|
| `vault-bridge\vault-bridge.exe` | 只裝核心時，編輯器的 MCP 指向它（輕量轉接到常駐 server） |
| `vault-mcp.exe` | MCP server 本體；`--mode api` 為 SSE 常駐（登入自啟、看護與 Espalio 啟動的都是它） |
| `vault-cli.exe` | 互動式 CLI（搜尋、讀筆記、`doctor` 健檢）；開始功能表的「主選單 → CLI 互動模式」 |

## 8. 更新

**自動更新**：server 每 6 小時檢查一次新版，有更新時跳通知，按下後自動下載並安裝，完成／失敗都會通知。
不想更新這一版：`& "C:\Program Files\AI Memory Vault\vault-cli.exe" --dismiss-update`。
插件有自己的更新通道，互不影響。

**5.x 之間手動升級**：直接執行新版的 `AI-Memory-Vault-Setup-v<版本>.exe` 即可。它會先結束執行中的核心、裝進原本的目錄，
設定、索引、Vault 都沿用。5.1.0 只改產品名，安裝路徑、執行檔名、設定都不變。

**回到較舊的 5.x**：執行那一版的安裝檔，再用上面的 `--dismiss-update` 讓自動更新不再提示剛才那個新版。

## 9. 解除安裝

1. 沒裝 Espalio 時，先移除看護排程（解除安裝不會自動移除它們）：
   `& "C:\Program Files\AI Memory Vault\scripts\register-sse-autostart.ps1" -Unregister`
2. Windows「設定 → 應用程式」→ **Espalio Memory**（5.0.x 顯示為 AI Memory Vault）→ 解除安裝；
   或開始功能表的「解除安裝 AI Memory Vault」。

程式目錄會整個移除。`%APPDATA%\AI-Memory-Vault\`（設定、索引、log）與你的 Vault 都保留；確定不再使用再自己刪除。
編輯器裡的 MCP 設定也要自己拿掉。

## 10. 常見問題

**Q：搜尋不到剛寫的筆記？**
用編輯器以外的方式（Obsidian、檔案總管）改過檔案後，呼叫一次 `sync`。

**Q：編輯器一直說 Vault 斷線？**
1. `curl http://127.0.0.1:8765/version`——有回版本號代表 server 正常，問題在編輯器那端的設定（見第 3 節）。
2. 沒回應，且沒裝 Espalio → 看 `%APPDATA%\AI-Memory-Vault\sse-watchdog-heartbeat.txt` 的時間戳。
   停在幾小時前 = 看護排程沒在跑，照第 5 節重新註冊；是新的 → 看同目錄的 `sse-watchdog.log` 與 `sse-server.log`。
   裝了 Espalio → 核心由 Espalio 重啟，先看 Espalio 那邊的狀態。
3. 想立刻恢復（沒裝 Espalio）：`& "C:\Program Files\AI Memory Vault\vault-sse-watchdog.ps1"`。

**Q：剛啟動時工具回 `[warming_up]`？**
核心剛啟動、還在載入模型與索引，稍等再試即可。

**Q：`netstat` 看得到 8765 在聽，為什麼還是連不上？**
那是「行程活著、但已經不回話」。只做 TCP 連線的檢查都會說它健康，要真的送一個請求才知道（上面的 `curl`）。
看護每 5 分鐘會自己處理。

**Q：改了設定檔，重開 Claude 桌面 App 又變回去？**
App 開著時會把記憶體裡的舊設定整份寫回。完全結束它（系統匣 → 結束）之後再改。

**Q：健康檢查？**
開始功能表「主選單 → CLI 互動模式」，輸入 `doctor`。

**Q：怎麼知道這版改了什麼？**
看該版 Release 頁面的說明，內容取自 CHANGELOG。

---

<sub>問題回報請到發布 repo 的 Issues。</sub>
