# Clash Verge 代理配置

> 2026-06-29

## 基本信息

| 项 | 值 |
|------|-----|
| 安装路径 | `D:\software\Clash Verge\` ？ |
| 配置目录 | `%AppData%\io.github.clash-verge-rev.clash-verge-rev\` |
| 订阅 | `mxdingyue.com` |

## 端口

| 端口 | 用途 |
|------|------|
| `7897` | mixed-port（HTTP + SOCKS5） |
| `7898` | socks-port |
| `7899` | redir-port |

## Git/SSH 通过代理

`~/.ssh/config`：
```
Host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519
    ProxyCommand connect -H 127.0.0.1:7897 %h %p
```

终端环境变量：
```bash
export https_proxy=http://127.0.0.1:7897
export http_proxy=http://127.0.0.1:7897
```
