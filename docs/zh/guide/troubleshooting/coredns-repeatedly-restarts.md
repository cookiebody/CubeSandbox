---
title: CoreDNS 容器反复重启
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - networking
  - DNS
lang: zh-CN
---

# CoreDNS 容器反复重启

## 症状

`cube-proxy-coredns` 容器崩溃并循环重启。日志显示 `bind: cannot assign requested address`。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  Docker 容器,  CoreDNS

## 根因

CoreDNS 配置绑定了 `169.254.254.53:53`，该 IP 在宿主机任何网络接口上都不存在。没有该 IP，绑定失败，容器退出。

## 解决方法

方案 1：创建 dummy 接口并分配所需 IP：

```bash
ip link add name dummy0 type dummy
ip addr add 169.254.254.53/32 dev dummy0
ip link set dummy0 up

# 配置开机持久化
cat > /etc/network/interfaces.d/dummy0 << 'EOF'
auto dummy0
iface dummy0 inet static
    address 169.254.254.53
    netmask 255.255.255.255
    pre-up ip link add name dummy0 type dummy
EOF
```

方案 2：修改 CoreDNS 配置绑定到可用 IP（如 127.0.0.1 或宿主机主 IP）。需要更新 CoreDNS Corefile 并重启容器。

## 参考

- 相关文档: [HTTPS 证书与域名解析](/zh/guide/https-and-domain)
