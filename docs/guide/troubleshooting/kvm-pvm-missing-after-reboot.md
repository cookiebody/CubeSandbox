---
title: kvm_pvm module not loaded after reboot
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - deployment
  - PVM
lang: en-US
---

# kvm_pvm module not loaded after reboot

## Symptom

Template build fails at the final stage with `expected:VsockServerReady, actual:VmShutdown`. The VM starts and immediately shuts down. This happens after a server reboot even though PVM was working before.

## Environment

- Cube Sandbox version: v0.2.0
- Deployment mode: PVM
- Host OS: Ubuntu 24.04 LTS / OpenCloudOS 9
- Related components: kvm_pvm kernel module, hypervisor

## Root Cause

The `kvm_pvm` kernel module was loaded manually with `modprobe` but was not configured for automatic loading on boot. After a reboot, the module is missing and PVM capability is unavailable. Without `kvm_pvm`, the hypervisor cannot create MicroVMs, so the guest kernel crashes immediately.

## Resolution

```bash
# Load the module immediately
modprobe kvm_pvm

# Verify it is loaded
lsmod | grep kvm_pvm
# Expected output:
# kvm_pvm                49152  10
# kvm                   1175552  1 kvm_pvm

# Configure auto-loading on boot
echo 'kvm_pvm' > /etc/modules-load.d/kvm-pvm.conf
```

After loading the module, restart the affected services:

```bash
cd /usr/local/services/cubetoolbox/scripts/one-click/
bash down.sh && sleep 2 && bash up.sh
```

## References

- Related docs: [PVM Deployment](/guide/pvm-deploy)
