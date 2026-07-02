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

## 更新 2026-07-02

### 文件内容验证（补齐短板）

仅 UUID 重命名只能防**路径遍历**，不能防以下攻击：

| 攻击类型 | UUID 方案效果 | 说明 |
|---------|:---:|------|
| 扩展名欺骗 `shell.php` | ❌ | 虽然文件名变了，但 `.php` 后缀仍保留，若上传目录在 webroot 下可被直接执行 |
| 文件内容（webshell） | ❌ | 文件内容本身可以是 `<?php system($_GET['cmd']); ?>` |
| 双扩展名 `shell.php.jpg` | ⚠️ | `Path().suffix` 只取最后一个 `.jpg`，但某些服务器配置下仍可能执行 |
| 大小炸弹 | ❌ | 无上限校验，攻击者可上传超大文件耗尽磁盘 |

### 完整防护方案

```python
import uuid, magic  # pip install python-magic
from pathlib import Path

ALLOWED_MIME = {
    'image/jpeg': '.jpg', 'image/png': '.png',
    'image/gif': '.gif', 'application/pdf': '.pdf',
}
MAX_SIZE = 16 * 1024 * 1024  # 16MB

def secure_save(file_storage, upload_dir: Path) -> Path:
    # 1. 大小校验
    file_storage.seek(0, 2)
    if file_storage.tell() > MAX_SIZE:
        raise ValueError("File too large")
    file_storage.seek(0)

    # 2. magic byte 识别真实类型（不信任扩展名）
    mime = magic.from_buffer(file_storage.read(2048), mime=True)
    file_storage.seek(0)
    ext = ALLOWED_MIME.get(mime)
    if not ext:
        raise ValueError(f"Type {mime} not allowed")

    # 3. UUID + 白名单扩展名（完全不信任原文件名）
    name = uuid.uuid4().hex + ext
    path = upload_dir / name

    # 4. 确保上传目录不在 webroot 下（或禁用脚本执行）
    file_storage.save(str(path))
    return path
```

**核心原则升级**：不信任原文件名 + **magic byte 判断真实类型** + 白名单扩展名 + 大小限制 + 禁止上传目录脚本执行。
