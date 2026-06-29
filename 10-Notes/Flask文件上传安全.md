# Flask 文件上传安全

> 2026-06-29

## 核心风险

`file.filename` 来自客户端 `Content-Disposition` 头，完全不可信。

| 攻击 | 示例 | 后果 |
|------|------|------|
| 路径穿越 | `../../../etc/passwd` | 覆盖系统文件 |
| Windows 保留名 | `CON`, `NUL`, `COM1` | 写入失败/系统异常 |
| 同形异义 | `арр.py`（西里尔 `р`） | 绕过黑名单 |

## `secure_filename()` 局限

```python
from werkzeug.utils import secure_filename
secure_filename("../../../etc/passwd")  # → "etc_passwd" ✅
secure_filename("CON.py")               # → "CON.py"      ❌ 未处理
```

**只防路径穿越，不防冲突、保留名、同形异义。**

## 推荐做法

```python
import uuid
from pathlib import Path

def safe_save(file, upload_dir: Path):
    ext = Path(file.filename).suffix[:16]  # 只取扩展名，截断防超长
    name = f"{uuid.uuid4()}{ext}"
    path = (upload_dir / name).resolve()
    if not str(path).startswith(str(upload_dir.resolve())):
        raise ValueError("路径穿越")
    file.save(str(path))
    return name
```

**原则**：完全不信任客户端文件名，服务端 UUID 生成唯一文件名。
