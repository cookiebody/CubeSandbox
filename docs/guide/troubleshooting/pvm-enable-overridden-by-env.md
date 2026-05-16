---
title: PVM guest kernel not used despite setting CUBE_PVM_ENABLE=1
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - deployment
  - PVM
lang: en-US
---

# PVM guest kernel not used despite setting CUBE_PVM_ENABLE=1

## Symptom

The one-click installation log shows `using ordinary guest kernel` instead of `using PVM guest kernel`. Subsequent sandbox creation fails with VM-related errors because the MicroVM was launched with the wrong kernel.

## Environment

- Cube Sandbox version: v0.2.0
- Deployment mode: PVM one-click install
- Host OS: Ubuntu 24.04 LTS
- Related components: one-click installer, cube-kernel-scf

## Root Cause

The one-click installer reads configuration from a `.env` file in the installation directory. If this file already exists (e.g., from a previous installation attempt), its default value `CUBE_PVM_ENABLE=0` overrides the environment variable passed from the parent shell during installation.

This is the most common cause of PVM not being enabled even when the installer is invoked with `CUBE_PVM_ENABLE=1`.

## Resolution

Before running the installer, check if `.env` exists and update it:

```bash
# If .env exists, update the PVM flag
sed -i 's/^CUBE_PVM_ENABLE=0/CUBE_PVM_ENABLE=1/' .env
grep CUBE_PVM_ENABLE .env  # Should output: CUBE_PVM_ENABLE=1
```

Then run the installer. Verify the log shows:

```
[one-click] CUBE_PVM_ENABLE=1, installed PVM guest kernel as .../cube-kernel-scf/vmlinux
```

If the log still shows `using ordinary guest kernel`, the `.env` file was not updated correctly and is overriding the shell variable.

## References

- Related docs: [PVM Deployment](/guide/pvm-deploy)
- Related issue: [#147](https://github.com/TencentCloud/CubeSandbox/issues/147)
