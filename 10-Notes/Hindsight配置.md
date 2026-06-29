# Hindsight 记忆系统配置

> 2026-06-29

## 当前状态

| 指标 | 值 |
|------|-----|
| 版本 | v0.8.2（Cloud） |
| Bank | `hermes-kb` |
| 记忆节点 | 329 |
| 知识链接 | 9,712 |
| 文档数 | 12 |

## 减少上下文冗余的配置

`config.json`：

```json
{
  "recall_budget": "mid",
  "recall_types": "experience,world",
  "recall_max_tokens": "4096"
}
```

## 关键机制

- **知识图谱自动合并**：`consolidation` 将同类事实合并为 observation
- **按 credits 计费**：不是存储量，而是 LLM 调用量
- **LLM 费用**：由 DeepSeek API Key 承担，不在 Hindsight 管控范围
- **免费额度**：无硬上限，节点增长速度约 20/天（可控）

## 扩容方案

| 方案 | 说明 |
|------|------|
| 自建 | 开源 Docker 本地部署 |
| 新 Bank | 按领域拆分，换电脑按需加载 |
| 付费 | 充值 credits |
