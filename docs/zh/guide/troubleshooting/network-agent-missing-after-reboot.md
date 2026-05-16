---
title: 服务器重启后 network-agent 丢失
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - operations
  - service
lang: zh-CN
---

# 服务器重启后 network-agent 丢失

## 症状

服务器重启后，创建沙箱报连接错误。运行 `quickcheck.sh` 显示 network-agent 缺失或未注册。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  PVM / 裸金属,  Ubuntu 24.04 LTS

## 根因

`network-agent` 进程不受 systemd 或 supervisor 管理，初始化设置时手动启动，服务器重启后不会自动重启。没有 network-agent，eBPF 虚拟交换机（CubeVS）无法工作，沙箱网络失败。

## 解决方法

```bash
# 启动 network-agent
mkdir -p /tmp/cube
nohup /usr/local/services/cubetoolbox/network-agent/bin/network-agent \
  -eth-name eth0 \
  -grpc-listen unix:///tmp/cube/network-agent-grpc.sock \
  -tap-fd-listen unix:///tmp/cube/network-agent-tap.sock \
  > /var/log/network-agent.log 2>&1 &

# 验证
curl -s http://127.0.0.1:19090/health  # 应返回 200
```

创建 systemd service 实现开机自启：

```bash
cat > /etc/systemd/system/cube-network-agent.service << 'EOF'
[Unit]
Description=Cube Sandbox Network Agent
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/services/cubetoolbox/network-agent/bin/network-agent \
  -eth-name eth0 \
  -grpc-listen unix:///tmp/cube/network-agent-grpc.sock \
  -tap-fd-listen unix:///tmp/cube/network-agent-tap.sock
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

mkdir -p /tmp/cube
systemctl enable cube-network-agent
systemctl start cube-network-agent
```

## 参考

- 相关文档: [多机集群部署](/zh/guide/multi-node-deploy)
