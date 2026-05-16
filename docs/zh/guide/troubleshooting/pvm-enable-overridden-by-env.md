---
title: 设置 CUBE_PVM_ENABLE=1 但未使用 PVM 内核
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - deployment
  - PVM
lang: zh-CN
---

# 设置 CUBE_PVM_ENABLE=1 但未使用 PVM 内核

## 症状

一键安装日志显示 `using ordinary guest kernel` 而非 `using PVM guest kernel`。后续创建沙箱时出现 VM 相关错误。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  PVM 一键安装,  Ubuntu 24.04 LTS

## 根因

一键安装程序从安装目录中的 `.env` 文件读取配置。如果该文件已存在（例如之前的安装尝试留下的），其默认值 `CUBE_PVM_ENABLE=0` 会覆盖从父 shell 传入的环境变量。这是 PVM 未启用的最常见原因。

## 解决方法

运行安装脚本前，检查并更新 `.env` 文件：

```bash
# 如果 .env 存在，更新 PVM 标志
sed -i 's/^CUBE_PVM_ENABLE=0/CUBE_PVM_ENABLE=1/' .env
grep CUBE_PVM_ENABLE .env  # 应输出: CUBE_PVM_ENABLE=1
```

然后运行安装程序。验证日志中出现：

```
[one-click] CUBE_PVM_ENABLE=1, installed PVM guest kernel as .../cube-kernel-scf/vmlinux
```

如果日志仍显示 `using ordinary guest kernel`，说明 `.env` 文件未正确更新。

## 参考

- 相关文档: [PVM部署](/zh/guide/pvm-deploy), 相关 issue: [#147](https://github.com/TencentCloud/CubeSandbox/issues/147)
