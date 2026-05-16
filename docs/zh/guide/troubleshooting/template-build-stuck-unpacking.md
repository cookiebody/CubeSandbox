---
title: 模板构建卡在 UNPACKING 阶段
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - template
  - disk
lang: zh-CN
---

# 模板构建卡在 UNPACKING 阶段

## 症状

模板构建进度停滞在 `UNPACKING`，长时间无进展。构建任务一直处于 PENDING 或 RUNNING 状态。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  PVM,  ext4 文件系统

## 根因

可能原因：
- 磁盘空间不足（尤其是 `/data/cubelet` 或系统分区）
- 网络问题导致基础镜像拉取失败
- 文件系统不兼容 — 推荐使用 XFS 以支持 reflink；ext4 可能在快照操作时出现问题

## 解决方法

```bash
# 检查磁盘空间
df -h /data/cubelet

# 清理旧日志和 Docker 残留
find /var/log -name "*.log.*" -delete
find /data/log -name "*.log.*" -size +100M -delete
docker system prune -f

# 查看模板构建日志
tail -f /var/log/cubelet.log

# 清理后重试构建
```

如果文件系统是 ext4，考虑重新格式化为 XFS 以获得更好的快照支持。

> **建议**: 将独立数据盘挂载到 `/data/cubelet`，防止系统盘被占满。

## 参考

- 相关文档: [模板概览](/zh/guide/templates)
