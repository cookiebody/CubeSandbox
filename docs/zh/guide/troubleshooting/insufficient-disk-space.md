---
title: 磁盘空间不足
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - operations
  - disk
lang: zh-CN
---

# 磁盘空间不足

## 症状

各种操作静默失败或挂起。模板构建停滞。沙箱创建超时。没有明确的错误提示。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  任意部署模式

## 根因

磁盘空间耗尽，通常在系统分区或 `/data/cubelet`。日志文件、Docker 层和模板快照会随时间消耗大量空间。

## 解决方法

```bash
# 检查空间使用情况
df -h
du -sh /data/cubelet/* 2>/dev/null | sort -rh | head -10

# 清理旧日志
find /var/log -name "*.log.*" -delete
find /data/log -name "*.log.*" -size +100M -delete

# 清理 Docker
docker system prune -f
docker volume prune -f

# 清理旧模板快照（保留最近的）
ls -lh /usr/local/services/cubetoolbox/cube-snapshot/
```

> **预防**: 将独立数据盘挂载到 `/data/cubelet`，防止系统盘被占满。为 Cube 服务设置日志轮转。

## 参考

- 相关文档: [PVM部署](/zh/guide/pvm-deploy)
