---
title: Insufficient disk space
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - operations
  - disk
lang: en-US
---

# Insufficient disk space

## Symptom

Various operations fail silently or hang. Template builds stall. Sandbox creation times out. No clear error message is displayed.

## Environment

- Cube Sandbox version: v0.2.0
- Related components:  any deployment mode

## Root Cause

Disk space exhaustion, typically on the system partition or `/data/cubelet`. Log files, Docker layers, and template snapshots consume significant space over time.

## Resolution

```bash
# Check space usage
df -h
du -sh /data/cubelet/* 2>/dev/null | sort -rh | head -10

# Clean up old logs
find /var/log -name "*.log.*" -delete
find /data/log -name "*.log.*" -size +100M -delete

# Clean up Docker
docker system prune -f
docker volume prune -f

# Clean up old template snapshots (keep recent ones)
ls -lh /usr/local/services/cubetoolbox/cube-snapshot/
```

> **Prevention**: Mount a separate data disk at `/data/cubelet` to prevent the system disk from filling up. Set up log rotation for Cube services.

## References

- Related docs: [PVM Deployment](/guide/pvm-deploy)
