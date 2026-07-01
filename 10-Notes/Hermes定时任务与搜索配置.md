---
title: Hermes定时任务与搜索配置
date: 2026-07-01
tags: [Hermes, cron, web-search, 配置, 自动化]
---

## 背景

2026-07-01 配置了每日 AI 资讯自动整理任务，过程中学习了 Hermes cron 机制和 web_search 后端配置。

## DDGS Web Search 后端

**DuckDuckGo (DDGS)** 是 Hermes 内置的免费 web_search 后端，无需 API Key。

```bash
# 配置
hermes config set web.backend ddgs
```

| 特性 | 状态 |
|------|------|
| web_search | ✅ 支持 |
| web_extract（网页抓取） | ❌ 不支持 |
| 费用 | 免费 |
| API Key | 不需要 |

**备选方案**（如需 web_extract）：
- Tavily：1000 次/月免费
- Firecrawl：网页抓取专用

## 每日 AI 资讯 Cron 任务

### 任务信息

| 项目 | 详情 |
|------|------|
| 任务 ID | `5a21a1713f5e` |
| 名称 | 每日AI资讯整理 |
| 调度 | `0 10 * * *`（每天 10:00） |
| 输出路径 | `C:\Users\fanye\Desktop\AI资讯\AI资讯_YYYY-MM-DD.md` |
| 覆盖方向 | 大模型、AI 工具、政策监管、开源、投融资、学术 |
| 通知 | 微信 |
| 启用工具集 | `web`, `file` |

### 创建命令

```bash
hermes cronjob create \
  --name "每日AI资讯整理" \
  --schedule "0 10 * * *" \
  --enabled-toolsets web,file \
  --prompt "你是一个AI资讯编辑..."
```

## Cron 上下文工具限制（重要教训）

首次运行（10:00）**失败**：cron 子进程中 `web_search` 不可用，因为当时 DDGS 后端尚未配置。

**已知限制**：
- `memory` 工具：cron 上下文不可用（`skip_memory=True`）
- `hindsight_*` 工具：需安装 Hindsight MCP server 才在 cron 中可用
- 可用工具集由 `enabled_toolsets` 白名单控制

**修复方式**：
1. 先配置 DDGS：`hermes config set web.backend ddgs`
2. 更新 cron 任务 prompt 使用 `web_search` 工具名

## 微信通知

- 微信 bot 需要用户在微信端手动批准账号后才能接收消息
- Cron 任务可设置 `deliver='all'` 推送到所有平台
