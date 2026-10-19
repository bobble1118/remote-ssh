---
name: remote-ssh
description: 透過 Tailscale + SSH 在指定的遠端電腦上執行指令、讀寫檔案、檢查狀態。當使用者說「在遠端/某台電腦上執行」、「ssh 到 xxx」、「幫我看遠端的 xxx」時使用。
---

# remote-ssh

用本機的 `ssh` / `scp` 操作遠端電腦（經由 Tailscale 連線）。遠端不需要安裝 Claude。

## 主機清單

主機以 `~/.ssh/config` 的 `Host` 別名為準。開始前先確認：

1. 執行 `tailscale status`，確認目標主機在線。
2. 讀取 `~/.ssh/config`，找出可用的主機別名。

使用者沒指定主機且只有一台時，用那台；有多台時先問。

## 執行方式

- 單一指令：`ssh -o BatchMode=yes -o ConnectTimeout=10 <host> "<command>"`
- 遠端為 Windows 時，預設 shell 是 cmd；要用 PowerShell 請寫：
  `ssh <host> powershell -NoProfile -Command "<cmd>"`
- 取檔：`scp <host>:<遠端路徑> <本機路徑>`
- 傳檔：`scp <本機路徑> <host>:<遠端路徑>`
- 引號、`$`、反引號要分兩層處理（本機 shell → 遠端 shell）。複雜的腳本請先用 scp 傳檔，再到遠端執行。

## 規則

1. 先測試連線：`ssh <host> "echo ok"`。失敗時依序檢查 `tailscale status`（對方是否在線）、`tailscale ping <host>`、遠端 sshd 是否開啟。不要隨意嘗試其他方式。
2. 唯讀指令（`ls`、`cat`、`df`、`ps`、`git status` 等）可以直接執行。
3. 會修改遠端的動作（寫檔、刪除、安裝套件、重啟服務、`git push`、關機）執行前，先向使用者說明「在哪台主機、做什麼」，並取得同意。
4. 不可把密碼、金鑰或 token 寫進指令列或檔案中。只使用金鑰或 Tailscale SSH 認證。
5. 每次回報都要註明在哪台主機執行，並附上指令結果與退出碼。
6. 長時間的工作請在遠端用 `nohup`（Linux/macOS）或 `Start-Process`（Windows）放到背景執行，之後再回頭查看輸出，避免 ssh 連線被卡住。

## 初次設定（一次性）

使用者要新增主機時，執行 `/remote-ssh:setup`，會以問答方式完成設定並測試連線。
若要手動設定，可在本機的 `~/.ssh/config` 加入以下內容（範本見 `ssh-config.example`）：

```
Host mypc
    HostName <tailscale 主機名稱或 100.x.y.z>
    User <遠端使用者>
    # 若使用金鑰驗證：
    # IdentityFile ~/.ssh/id_ed25519
```

若使用 Tailscale SSH，只需在遠端執行 `tailscale up --ssh`，不需要設定 `IdentityFile`。
