---
title: LLM API 价格对比
date: 2026-07-02
tags: [AI, API, 价格, LLM]
---

# LLM API 价格对比（2026 年 7 月）

> 数据来源：各厂商官方定价 + OpenRouter / BenchLM，截至 2026-06-28。

## 主要模型价格（$/百万 tokens）

| 模型 | 输入 | 输出 | 厂商 | 备注 |
|------|-----:|-----:|------|------|
| DeepSeek V4 Flash | $0.14 | $0.28 | DeepSeek | 辅助模型，性价比极高 |
| DeepSeek V4 Pro | $0.27 | $1.10 | DeepSeek | 主力模型，上下文 1M |
| DeepSeek V4 Pro (OpenRouter) | $0.435 | $0.87 | OpenRouter | 统一价，不限时段 |
| GPT-5.5 | $5.00 | $30.00 | OpenAI | 旗舰，complex coding |
| GPT-5.3-Codex | $1.75 | $14.00 | OpenAI | Agent 编程专用 |
| Claude Opus 4.8 | $5.00 | $25.00 | Anthropic | 旗舰 |
| Gemini 3.1 Pro | $2.00 | $12.00 | Google | — |
| Gemini 3.1 Flash-Lite | $0.25 | $1.50 | Google | 低价位 |
| GLM-5.2 | $1.40 | $4.40 | 智谱 | 国产 |
| Qwen 3.7-Max | 促销价 | — | 阿里 | Agent 编程 |

## 我当前的配置

| 项 | 值 |
|------|-----|
| 主力模型 | `deepseek-v4-pro`（DeepSeek 官方直连） |
| 辅助路由 | `deepseek-v4-flash`（compression/web_extract/vision） |
| 备用方案 | OpenRouter（API key 已在 `.env`，DeepSeek 涨价后切换） |

## 切换至 OpenRouter 的命令

```bash
hermes config set model.provider openrouter
hermes config set model.base_url "https://openrouter.ai/api/v1"
hermes config set model.default deepseek/deepseek-v4-pro
hermes config set auxiliary.compression.provider openrouter
hermes config set auxiliary.vision.provider openrouter
hermes config set auxiliary.web_extract.provider openrouter
```

## 关键观察

- DeepSeek V4 Pro 官方价 $0.27/$1.10，比 GPT-5.5 便宜约 **18 倍**（输入），比 Claude Opus 4.8 便宜约 **18 倍**
- OpenRouter 上 DeepSeek V4 Pro 略贵（$0.435/$0.87），但**无峰谷定价**，且备有 338 个模型可选
- DeepSeek 传说 7 月中旬可能调整峰谷定价，届时可切 OpenRouter
- 代码场景：GPT-5.3-Codex vs Qwen 3.7 vs DeepSeek V4 Pro 各有优势
