---
title: network-agent missing after server reboot
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - operations
  - service
lang: en-US
---

# network-agent missing after server reboot

## Symptom

After a server reboot, sandbox creation fails with connection errors. Running `quickcheck.sh` shows network-agent as missing or not registered.

## Environment

- Cube Sandbox version: v0.2.0
- Deployment mode: PVM / Bare-Metal
- Host OS: Ubuntu 24.04 LTS
- Related components: network-agent, cubelet

## Root Cause

The `network-agent` process is not managed by systemd or supervisor. It is started manually during initial setup and does not automatically restart after a server reboot. Without network-agent, the eBPF-based virtual switch (CubeVS) cannot function, and sandbox networking fails.

## Resolution

```bash
# Start network-agent
mkdir -p /tmp/cube
nohup /usr/local/services/cubetoolbox/network-agent/bin/network-agent \
  -eth-name eth0 \
  -grpc-listen unix:///tmp/cube/network-agent-grpc.sock \
  -tap-fd-listen unix:///tmp/cube/network-agent-tap.sock \
  > /var/log/network-agent.log 2>&1 &

# Verify it is running
curl -s http://127.0.0.1:19090/health  # Should return 200
```

To automate this on boot, create a systemd service unit:

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

## References

- Related docs: [Multi-Node Cluster](/guide/multi-node-deploy)
