---
title: disable_tso 字段缺失错误
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - configuration
  - shim
lang: zh-CN
---

# disable_tso 字段缺失错误

## 症状

创建沙箱时报 `failed to create shim task: missing field 'disable_tso'`。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  PVM,  Ubuntu 24.04 LTS,  cube-shim

## 根因

`cube-shim` 配置文件（`config-cube.toml`）缺少必需的网络设置。这通常发生在配置文件来自旧版本或手动编辑时遗漏了 `[network]` 段。

## 解决方法

在 `/usr/local/services/cubetoolbox/cube-shim/conf/config-cube.toml` 中添加：

```toml
[network]
disable_tso = true
disable_ufo = true
disable_check_sum = true
```

然后重启 cube-shim 服务：

```bash
cd /usr/local/services/cubetoolbox/scripts/one-click/
bash down.sh && sleep 2 && bash up.sh
```

## 参考

- 相关文档: [本地构建部署](/zh/guide/self-build-deploy)
