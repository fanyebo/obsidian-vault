# Windows 系统清理

> 2026-06-29

## 清理项目

| 项目 | 方法 | 效果 |
|------|------|------|
| `%TEMP%` + `C:\Windows\Temp` | `Remove-Item -Recurse` | 用户+系统临时文件 |
| Prefetch | `Remove-Item C:\Windows\Prefetch\*` | 预读缓存 |
| 回收站 | `Clear-RecycleBin -Force` | |
| Chrome/Edge 缓存 | 删除 `Cache` + `Code Cache` 目录 | |
| Windows Update 缓存 | `SoftwareDistribution\Download\*` | **需先停 wuauserv 服务** |
| npm cache | `npm cache clean --force` | |
| uv cache | `uv cache clean` | 本次释放 **1.0 GiB** |
| DISM 组件 | `Dism /online /Cleanup-Image /StartComponentCleanup /ResetBase` | **需管理员权限** |

## DISM 提权执行

```powershell
Start-Process -Verb RunAs Dism.exe -ArgumentList "/online /Cleanup-Image /StartComponentCleanup /ResetBase /Quiet"
```

弹出 UAC 点"是"即可，确认后自动返回。

## 本次结果

C 盘已用：86.2 GB → 84.6 GB，释放约 **1.6 GB**（uv cache 是主要贡献者）。
