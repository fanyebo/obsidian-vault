---
title: Python 标准库安全工具
date: 2026-07-02
tags: [Python, 安全, 标准库]
---

# Python 标准库安全工具

> 三个常用的 Python 内置安全工具，无需 `pip install`。

## `ast.literal_eval()` — 安全解析字面量

**用途**：将 Python 字面量字符串安全解析为 Python 对象，不执行代码。

```python
import ast

s = "{'name': 'abc', 'age': 25, 'active': True, 'data': None}"
d = ast.literal_eval(s)  # → {'name': 'abc', 'age': 25, ...}
```

### 三种解析方式对比

| 方法 | 安全性 | 能解析 | 不能 |
|------|:---:|------|------|
| `ast.literal_eval()` | ✅ 安全 | Python 字面量（dict/list/str/int/None/True/False） | 变量、函数调用 |
| `eval()` | ❌ 危险 | 任意 Python 代码 | — |
| `json.loads()` | ✅ 安全 | 仅合法 JSON（双引号） | 单引号、`None`/`True`/`False` |

### 典型场景

`str(dict)` 输出 Python 字面量（单引号），不是 JSON（双引号），用 `json.loads` 会报错。此时 `ast.literal_eval` 是最佳选择。

### 版本

Python 3.0+ 内置，标准库，0 依赖。

---

## `shlex.quote()` — Shell 参数安全包装

**用途**：用单引号包裹字符串，使 shell 不解析内部特殊字符。

```python
import shlex

shlex.quote("foo; rm -rf /")   # → "'foo; rm -rf /'"
shlex.quote("It's a test")     # → "'It'\"'\"'s a test'"
shlex.quote("$(cat /etc/passwd)")  # → "'$(cat /etc/passwd)'"
```

### 本质

| 机制 | 说明 |
|------|------|
| 外壳 | 单引号 `'...'` 包裹参数，shell 不解析 `;` `\|` `$()` 等 |
| 内部单引号 | `'` → `'"'"'`（闭合+双引号单引号+重开） |
| 效果 | 整串作为字面量传给命令 |

### 限制

- 只保护单个参数，不保护整个命令字符串
- 不防 `-rf` 等选项注入
- 仅在 shell 上下文有效，`subprocess.run(cmd_list)` 不需要

### 版本

Python 2.6+ 内置，标准库，0 依赖。

---

## `shlex.split()` — 类 Shell 分词

```python
import shlex

shlex.split('echo "hello world" foo')
# → ['echo', 'hello world', 'foo']  （引号内空格保留）
```

类似 shell 的分词行为，但更安全可预测。

---

## `subprocess.run()` 传列表 — 从根源防注入

**首选方案**：传列表而非字符串，不经过 shell。

```python
import subprocess

user_input = "foo; rm -rf /"

# ✅ 安全：参数直接传给进程，不经过 shell
subprocess.run(["echo", user_input])

# ❌ 危险：字符串经过 shell 解析
subprocess.run(f"echo {user_input}", shell=True)
```

| 方案 | 安全性 | 推荐度 |
|------|:---:|:---:|
| `subprocess.run(cmd_list)` | ✅ 最高 | ⭐⭐⭐ 首选 |
| `os.system` + `shlex.quote()` | ✅ 高 | ⭐⭐ 备选 |
| 白名单正则校验 | ✅ 高 | ⭐ 输入格式已知时 |

### 版本

Python 3.5+ 内置。

---

## 总结

| 工具 | 用途 | 一句话 |
|------|------|--------|
| `ast.literal_eval` | 解析 Python 字面量 | 比 `eval` 安全，比 `json.loads` 兼容性好 |
| `shlex.quote` | Shell 参数转义 | `os.system` 的安全底线 |
| `subprocess.run` | 执行外部命令 | 传列表，从根源消除 shell 注入 |
