---
title: CoreDNS container repeatedly restarts
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - networking
  - DNS
lang: en-US
---

# CoreDNS container repeatedly restarts

## Symptom

`cube-proxy-coredns` container crashes and restarts in a loop. Logs show `bind: cannot assign requested address`.

## Environment

- Cube Sandbox version: v0.2.0
- Related components:  Docker container,  CoreDNS

## Root Cause

CoreDNS configuration binds to `169.254.254.53:53`, an IP address that does not exist on any host network interface. Without this IP being assigned to an interface, the bind fails and the container exits.

## Resolution

Option 1: Create a dummy interface with the required IP:

```bash
ip link add name dummy0 type dummy
ip addr add 169.254.254.53/32 dev dummy0
ip link set dummy0 up

# Make persistent across reboots
cat > /etc/network/interfaces.d/dummy0 << 'EOF'
auto dummy0
iface dummy0 inet static
    address 169.254.254.53
    netmask 255.255.255.255
    pre-up ip link add name dummy0 type dummy
EOF
```

Option 2: Modify CoreDNS config to bind to an available IP (e.g., 127.0.0.1 or the host's primary IP). This requires updating the CoreDNS Corefile and restarting the container.

## References

- Related docs: [HTTPS & Domain Resolution](/guide/https-and-domain)
