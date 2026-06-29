# VS Code 配置

> 2026-06-29

## Python 开发环境

| 配置 | 值 |
|------|-----|
| Python 版本 | 3.11.15 |
| 测试框架 | pytest 9.0.2 |
| Linter/Formatter | Ruff 0.15.10（Rust 原生，保存时自动修复） |
| 调试器 | debugpy 1.8.20 |

## Pylance 智能补全

```json
{
  "python.analysis.diagnosticMode": "workspace",
  "python.analysis.include": ["."],
  "python.analysis.extraPaths": ["./src"],
  "editor.quickSuggestionsDelay": 0
}
```

- `diagnosticMode: workspace` → 扫描整个项目（非默认的 `openFilesOnly`）
- `quickSuggestionsDelay: 0` → import 后立即弹出补全

## Git 集成

| 扩展 | 版本 | 用途 |
|------|------|------|
| GitLens | 18.2.0 | 行级 blame、历史对比 |
| Git Graph | 1.30.0 | 可视化分支图 |

```json
{
  "git.untrackedChanges": "hidden",
  "git.enableSmartCommit": true,
  "git.confirmSync": false
}
```

- `untrackedChanges: hidden` → 未追踪文件不在源码管理面板显示标记
- 常用快捷键：`Ctrl+Shift+G` 打开源码管理、`Ctrl+Enter` 提交

## 快捷键

| 功能 | 快捷键 | 备注 |
|------|--------|------|
| 命令面板 | **F1**（首推）或 `Ctrl+Shift+P` | F1 不受键盘固件拦截 |
| 源码管理 | `Ctrl+Shift+G` | |
| 提交 | `Ctrl+Enter` | |
| 重载窗口 | `F1` → `Reload Window` | 解决扩展异常 |

## Ruff 配置

```json
{
  "ruff.lint.args": ["--config=pyproject.toml"],
  "ruff.format.args": ["--config=pyproject.toml"],
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll.ruff": "explicit",
      "source.organizeImports.ruff": "explicit"
    }
  }
}
```

## 已知问题

| 问题 | 原因 | 解决 |
|------|------|------|
| Ruff 报错 | 扩展访问了失效的 Python 解释器路径 | 更新 `python.defaultInterpreterPath` 或重载窗口 |
| import 无补全 | Pylance 默认 `openFilesOnly` | 改为 `workspace` 模式 |
| Windows 下 Python 路径 ENOENT | 写死了 Linux 路径 | 使用 `${workspaceFolder}` 相对路径或 venv 名称 |

## 扩展列表

- Python / Pylance
- Ruff
- GitLens + Git Graph
- 已安装但禁用未追踪文件标记（`git.untrackedChanges: hidden`）
