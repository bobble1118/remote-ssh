# remote-ssh

讓 Claude Code 透過 Tailscale + SSH 操作遠端電腦的 plugin。遠端不需要安裝 Claude，只要能用 SSH 登入即可。

## 內容

- `skills/remote-ssh/`：技能本體，Claude 看到相關請求時會自動使用。
- `commands/setup.md`：`/remote-ssh:setup` 互動式設定指令。Claude 會逐一詢問主機別名、位址、使用者與驗證方式，確認後寫入 `~/.ssh/config` 並測試連線。

## 前置需求

- 本機與遠端都已安裝並登入 [Tailscale](https://tailscale.com/)。
- 本機已安裝 OpenSSH 用戶端（`ssh`、`scp`）。
- 遠端已開啟 sshd，或使用 `tailscale up --ssh`。

## 安裝

在 Claude Code 中執行：

```
/plugin marketplace add bobble1118/remote-ssh
/plugin install remote-ssh@bobble1118-plugins
```

安裝後執行設定：

```
/remote-ssh:setup
```

也可以直接說「幫我設定一台遠端電腦」，由 Claude 引導完成。

## 使用

在 Claude Code 中說明即可觸發，例如：

- 「在遠端 mypc 上執行 `df -h`」
- 「ssh 到 mypc 看一下 /var/log 的內容」

## 手動設定（不使用指令）

參考 `ssh-config.example`，在 `~/.ssh/config` 新增自己的主機設定，然後測試：

```bash
ssh mypc "echo ok"
```

## 安全注意事項

- 本 repo 不含任何 IP、帳號、金鑰或 token。請勿把自己的 `~/.ssh/config` 或金鑰提交到 repo。
- 設定指令只會寫入 `~/.ssh/config`，寫入前會先讓你確認，並備份既有檔案。
- 技能會先要求確認才會修改遠端，請保留這項規則。
