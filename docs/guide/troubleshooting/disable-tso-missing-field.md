---
title: disable_tso missing field error
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - configuration
  - shim
lang: en-US
---

# disable_tso missing field error

## Symptom

`failed to create shim task: missing field 'disable_tso'` when creating a sandbox.

## Environment

- Cube Sandbox version: v0.2.0
- Related components:  PVM,  Ubuntu 24.04 LTS,  cube-shim

## Root Cause

The `cube-shim` configuration file (`config-cube.toml`) is missing required network settings. This happens when the config file is from an older version or was manually edited and the `[network]` section was not added.

## Resolution

Add the following to `/usr/local/services/cubetoolbox/cube-shim/conf/config-cube.toml`:

```toml
[network]
disable_tso = true
disable_ufo = true
disable_check_sum = true
```

Then restart the cube-shim service:

```bash
cd /usr/local/services/cubetoolbox/scripts/one-click/
bash down.sh && sleep 2 && bash up.sh
```

## References

- Related docs: [Self-Build Deployment](/guide/self-build-deploy)
