---
description: 互動式設定遠端主機（寫入 ~/.ssh/config 並測試連線）
argument-hint: [主機別名]
allowed-tools: Bash, AskUserQuestion, Read, Write, Edit
---

幫使用者設定一台新的遠端主機，讓 remote-ssh 技能可以使用。$ARGUMENTS 如果有給，就當作主機別名的預設值。

依序進行，每一步都先確認，不要一次假設所有答案：

1. **檢查環境**
   - 執行 `tailscale status`，確認本機已登入 Tailscale。未安裝或未登入時，告訴使用者先處理，然後停止。
   - 執行 `ssh -V`，確認有 OpenSSH。

2. **用 AskUserQuestion 詢問**（可以合併成一次呼叫，最多 4 題）：
   - 主機別名（`Host`，例如 `mypc`）。若 $ARGUMENTS 有值就當作預設。
   - 遠端的 Tailscale 主機名稱或 `100.x.y.z` 位址（可以從上一步 `tailscale status` 的輸出中列出選項）。
   - 遠端的登入使用者名稱。
   - 驗證方式：
     - Tailscale SSH（遠端會執行 `tailscale up --ssh`，不需要金鑰）
     - SSH 金鑰（需要再問金鑰檔路徑，預設 `~/.ssh/id_ed25519`）

   **絕對不要詢問或記錄密碼、token 或金鑰內容。** 只詢問金鑰檔的路徑。

3. **組出設定區塊**，格式如下，並先把完整內容給使用者看，請使用者確認：

   ```
   Host <別名>
       HostName <主機名稱或 IP>
       User <使用者>
       # 金鑰驗證時才加：
       IdentityFile <金鑰路徑>
       IdentitiesOnly yes
   ```

4. **寫入 `~/.ssh/config`**（使用者確認後才寫）：
   - 若檔案已存在，先備份為 `~/.ssh/config.bak`。
   - 若已有相同的 `Host` 別名，先詢問要覆蓋還是改名，不要直接覆蓋。
   - 檔案不存在時就建立它。

5. **測試連線**：執行 `ssh -o BatchMode=yes -o ConnectTimeout=10 <別名> "echo ok"`。
   - 成功：回報「連線成功」。
   - 失敗：依序檢查 `tailscale ping <主機>`、遠端 sshd 或 `tailscale up --ssh` 是否已設定，並把錯誤訊息告訴使用者。不要改用其他驗證方式。

6. **結束時**告訴使用者可以怎麼用，例如：「在遠端 `<別名>` 上執行 `df -h`」。

回覆請使用繁體中文。
