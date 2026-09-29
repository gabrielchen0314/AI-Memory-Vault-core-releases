# AI Memory Vault — 使用指南（安裝版）

> 這份是給**下載安裝包來用**的人。想從原始碼架設 / 開發 → 見主 repo 的 `USAGE.md`。
>
> 本檔由 `AI_Engine/packaging/publish-update.ps1` 於每次發布時自動推送至發布 repo，
> **請勿在發布 repo 直接編輯**——那裡的版本會在下次發布時被覆蓋。
> 要修改請改主 repo 的 `AI_Engine/packaging/release-usage.md`。

---

## 0. 兩個產品，先分清楚

| 產品 | 做什麼 | 下載 | 必裝？ |
|---|---|---|---|
| **AI Memory Vault**（記憶核心） | 記憶的寫入、語意搜尋、索引、自我更新 | [AI-Memory-Vault-core-releases](https://github.com/gabrielchen0314/AI-Memory-Vault-core-releases/releases/latest) | ✅ |
| **Vault Workflow**（插件） | 收工、晨報、日／週／月報與排程、Ai-Debate、starter pack、Profile 切換 | [AI-Memory-Vault-workflow-releases](https://github.com/gabrielchen0314/AI-Memory-Vault-workflow-releases/releases/latest) | 選用 |

只要記憶功能 → 只裝核心。要個人工作流 → **先裝核心，再裝插件**（插件的安裝檔會檢查核心版本，低於 5.0 會拒絕安裝）。

## 1. 安裝

執行 `AI-Memory-Vault-Setup-<版本>.exe`，預設安裝到 `C:\Program Files\AI Memory Vault`。
**保留「登入時自動啟動 MCP SSE Server」的勾選**——編輯器經由它連線，也靠它的看護排程在斷線時自己回來。

## 2. 首次設定

開始功能表 → **AI Memory Vault → 環境設定**，依序問：

1. **Vault 路徑**——知識庫要放哪（建議放在有備份的磁碟）
2. **回應語言**——預設 `zh-TW`

重跑也安全：每一題按 Enter 保留方括號內的現值，其他設定不會動。
設定檔在 `%APPDATA%\AI-Memory-Vault\config.json`；API 金鑰放同目錄的 `.env`。

> 安裝程式**不會**把執行檔加進 PATH。命令列要用完整路徑，例如
> `& "C:\Program Files\AI Memory Vault\vault-cli.exe" --setup`。

## 3. 接上編輯器（MCP）

編輯器一律指向**轉接器** `vault-bridge.exe`（輕量、不載入模型；記憶核心常駐在背景，多個編輯器共用一份）：

```
C:\Program Files\AI Memory Vault\vault-bridge\vault-bridge.exe
```

> 裝了 Vault Workflow 插件的話，改指插件的 gateway（見插件的使用指南）——它會把記憶功能一起轉給核心，
> 另外多出個人工作流的工具。

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

> ⚠️ 安裝時若改過目錄，路徑要換成實際位置。
> ⚠️ **Claude 桌面 App 與 Antigravity 開著時會把設定檔整份寫回**：請先完全結束（系統匣 → 結束）再改，否則重開後又變回舊的。

接好之後，編輯器的工具清單會出現 8 支：`recall`、`read`、`remember`、`update`、`forget`、`rename`、`context`、`sync`。

## 4. 從 4.x 升級（拆分前的單一版本）

5.0 起記憶核心與個人工作流拆成兩個產品。**4.x 的自動更新不會把你升到 5.0**（刻意的：只裝核心會讓收工、排程、
Ai-Debate 靜默停止）。要升級請手動照順序做：

1. 安裝 `AI-Memory-Vault-Setup-v5.*.exe`（偵測到 4.x 且沒裝插件時，會先說明哪些功能會搬走並讓你確認）
2. 要保留收工／晨報／排程／Ai-Debate → 安裝 `Vault-Workflow-Setup-v*.exe`
3. **完全結束 Claude 桌面 App 與 Antigravity**，從外部開的 PowerShell 執行插件附的一鍵遷移（先預覽）：

   ```powershell
   powershell -NoProfile -ExecutionPolicy Bypass -File "C:\Program Files\Vault Workflow\scripts\migrate-to-split.ps1" -DryRun
   ```

   確認沒問題後拿掉 `-DryRun` 再跑一次。它會遷移設定檔、把編輯器與排程改接插件，每處改動都留還原腳本。
   結束碼 2 代表有 App 還開著、那個檔先跳過了——關掉後重跑即可。

只裝核心、不裝插件也可以：記憶功能完整；舊的收工／晨報排程會找不到插件而停止，編輯器接回 `vault-bridge.exe`（見第 3 節）。

## 5. 常駐 server 與看護

記憶核心以 SSE server 常駐在 `127.0.0.1:8765`（安裝時勾選自啟即自動註冊）：

| 排程 | 觸發 | 管什麼 |
|---|---|---|
| `Vault-SseServer-AtLogon` | 登入時 | 開機後有 server |
| `Vault-SseServer-Watchdog` | 每 5 分鐘 | 死了會自己回來 |

沒勾自啟、事後想補：`& "C:\Program Files\AI Memory Vault\scripts\register-sse-autostart.ps1"`。
`vault-bridge.exe` 發現 server 不在時，也會自己觸發自啟排程。

server 內建記憶維護定時器（索引同步、備份、lesson 衰減、更新檢查），電腦關著錯過的會在下次啟動後補跑。

## 6. 第一次啟動會發生什麼

- **第一次需要連網**：語意搜尋用的嵌入模型（`paraphrase-multilingual-MiniLM-L12-v2` 的 ONNX 版）在第一次啟動時
  從 Hugging Face 下載並快取，之後離線可用。
  這段期間編輯器可能顯示連線中，等它下載完即可。
- **Vault 裡原本就有筆記**：啟動後的維護會把它們收進索引；想立刻搜得到，在編輯器裡呼叫一次 `sync`。

## 7. 產出的執行檔

| 執行檔 | 用途 |
|--------|------|
| `vault-bridge\vault-bridge.exe` | 編輯器的 MCP 指向它（輕量轉接到常駐 server） |
| `vault-mcp.exe` | MCP server 本體；`--mode api` 為 SSE 常駐（自啟排程跑的就是它） |
| `vault-cli.exe` | 互動式 CLI（搜尋、讀筆記、`doctor` 健檢）；開始功能表的「主選單 → CLI 互動模式」 |

## 8. 自動更新

server 每 6 小時檢查一次新版，有更新時跳通知，按下後自動下載並安裝，完成／失敗都會通知。
不想更新：`& "C:\Program Files\AI Memory Vault\vault-cli.exe" --dismiss-update`。
插件有自己的更新通道，互不影響。

## 9. 常見問題

**Q：搜尋不到剛寫的筆記？**
用編輯器以外的方式（Obsidian、檔案總管）改過檔案後，呼叫一次 `sync`。

**Q：編輯器一直說 Vault 斷線？**
1. `curl http://127.0.0.1:8765/version`——有回版本號代表 server 正常，問題在編輯器那端的設定（見第 3 節）。
2. 沒回應 → 看 `%APPDATA%\AI-Memory-Vault\sse-watchdog-heartbeat.txt` 的時間戳。
   停在幾小時前 = 看護排程沒在跑，照第 5 節重新註冊；是新的 → 看同目錄的 `sse-watchdog.log` 與 `sse-server.log`。
3. 想立刻恢復：`& "C:\Program Files\AI Memory Vault\vault-sse-watchdog.ps1"`。

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
