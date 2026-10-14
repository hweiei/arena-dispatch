# push-proof — TASK_ID: push-1

## uname -a
```
Linux e2b.local 6.1.158+ #1 SMP PREEMPT_DYNAMIC Tue Jul 28 15:38:05 UTC 2026 x86_64 GNU/Linux
```

## date -u
```
Wed Oct 14 12:26:17 UTC 2026
```

## git rev-parse HEAD（基于的提交）
```
554cf55082151b5d98aa5152aec98595dc1830e3
```

## 私钥是怎么写进 ~/.ssh/id_ed25519 的
用 bash 的 `cat > ~/.ssh/id_ed25519 <<'EOF' … EOF` 带引号 heredoc 整段原样写入（含 BEGIN/END 两行，无 shell 展开），随后 `chmod 700 ~/.ssh`、`chmod 600 ~/.ssh/id_ed25519`，并用 `ssh-keygen -y -f` 校验私钥可解析（指纹 SHA256:VFiH8ewNvAskNtqYUpT8wcoN77jS2eF8o55uNxLgw14，注释 arena-dispatch-worker）。

## 备注
- 沙箱里 `ssh-keyscan -p 443 ssh.github.com` 无输出（拿不到 host key），改用 GitHub 官方 `https://api.github.com/meta` 公布的 ssh_keys 写入 `~/.ssh/known_hosts`（条目 `[ssh.github.com]:443`），连接时 ED25519 指纹 `SHA256:+DiY3wvvV6TuJJhbpZisF/zLDA0zPMSvHdkr4UvCOqU` 与官方一致。
- 工作目录原本为空，没有旧仓库，所以本轮 clone 了一次（走 `ssh://git@ssh.github.com:443/…`），未重复 clone。
