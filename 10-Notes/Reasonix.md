# Reasonix

> 2026-06-29

## 概述

[DeepSeek-Reasonix](https://github.com/esengine/DeepSeek-Reasonix) — DeepSeek 原生的终端 AI 编程代理。esengine 开发，25K+ Stars。

## 关键特征

- **Go 单二进制**（v1.0）→ 零依赖，`CGO_ENABLED=0`
- **前缀缓存优化** → 长会话 Token 成本低
- **`reasonix.toml`** 声明式配置
- **插件**：MCP 兼容的 stdio JSON-RPC
- 安装：`npm i -g reasonix` 或 `brew`

## 本地状态

- 版本：v0.53.2
- 配置：`%AppData%\reasonix\`
- 已配置 3 个模型

## 与 Hermes 对比

| | Reasonix | Hermes |
|------|---------|--------|
| 语言 | Go | Python |
| 分发 | 单二进制 | pip/uv |
| 平台 | 纯终端 | CLI + TUI + 桌面 + 网关（20+） |
| 模型绑定 | DeepSeek 优化 | 多 provider 通用 |
