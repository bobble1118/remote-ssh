# remote-ssh

讓 Claude Code 透過 Tailscale + SSH 操作遠端電腦的技能。遠端不需要安裝 Claude，只要能用 SSH 登入即可。

## 前置需求

- 本機與遠端都已安裝並登入 [Tailscale](https://tailscale.com/)。
- 本機已安裝 OpenSSH 用戶端（`ssh`、`scp`）。
- 遠端已開啟 sshd，或使用 `tailscale up --ssh`。

## 安裝

1. 把本 repo 的 `SKILL.md` 複製到 Claude Code 的技能目錄：

   ```bash
   mkdir -p ~/.claude/skills/remote-ssh
   cp SKILL.md ~/.claude/skills/remote-ssh/SKILL.md
   ```

   Windows（PowerShell）：

   ```powershell
   New-Item -ItemType Directory -Force "$HOME\.claude\skills\remote-ssh"
   Copy-Item SKILL.md "$HOME\.claude\skills\remote-ssh\SKILL.md"
   ```

2. 參考 `ssh-config.example`，在 `~/.ssh/config` 新增自己的主機設定。

3. 測試連線：

   ```bash
   ssh mypc "echo ok"
   ```

## 使用

在 Claude Code 中說明即可觸發，例如：

- 「在遠端 mypc 上執行 `df -h`」
- 「ssh 到 mypc 看一下 /var/log 的內容」

## 安全注意事項

- 本 repo 不含任何 IP、帳號、金鑰或 token。請勿把自己的 `~/.ssh/config` 或金鑰提交到 repo。
- 技能會先要求確認才會修改遠端，請保留這項規則。
