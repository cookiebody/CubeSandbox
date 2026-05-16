---
title: 重启后 kvm_pvm 模块未加载
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - deployment
  - PVM
lang: zh-CN
---

# 重启后 kvm_pvm 模块未加载

## 症状

模板构建在最后阶段报 `expected:VsockServerReady, actual:VmShutdown`，VM 启动后立即关闭。服务器重启后出现，此前 PVM 工作正常。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  PVM,  Ubuntu 24.04 LTS / OpenCloudOS 9

## 根因

`kvm_pvm` 内核模块只是手动加载了，未配置开机自启。服务器重启后模块丢失，PVM 能力不可用。没有 `kvm_pvm`，Hypervisor 无法创建 MicroVM，Guest 内核立即崩溃。

## 解决方法

```bash
# 立即加载模块
modprobe kvm_pvm

# 验证已加载
lsmod | grep kvm_pvm

# 配置开机自动加载
echo 'kvm_pvm' > /etc/modules-load.d/kvm-pvm.conf
```

加载模块后，重启受影响的服务：

```bash
cd /usr/local/services/cubetoolbox/scripts/one-click/
bash down.sh && sleep 2 && bash up.sh
```

## 参考

- 相关文档: [PVM部署](/zh/guide/pvm-deploy)
