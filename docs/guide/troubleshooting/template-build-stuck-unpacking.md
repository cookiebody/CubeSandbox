---
title: Template build stuck in UNPACKING phase
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - template
  - disk
lang: en-US
---

# Template build stuck in UNPACKING phase

## Symptom

Template build progress stalls at `UNPACKING` and never completes. The build job remains in PENDING or RUNNING state indefinitely.

## Environment

- Cube Sandbox version: v0.2.0
- Related components:  PVM,  ext4 filesystem

## Root Cause

Multiple possible causes:
- Disk space is full (especially `/data/cubelet` or system partition)
- Network issues preventing the base image from being pulled
- Filesystem incompatibility — XFS is recommended for reflink support; ext4 may cause issues with snapshot operations

## Resolution

```bash
# Check disk space
df -h /data/cubelet

# Clean up old logs and Docker artifacts
find /var/log -name "*.log.*" -delete
find /data/log -name "*.log.*" -size +100M -delete
docker system prune -f

# Check template build logs
tail -f /var/log/cubelet.log

# Retry the build after cleanup
```

If the filesystem is ext4, consider reformatting to XFS for better snapshot support.

> **Tip**: Mount a separate data disk at `/data/cubelet` to prevent the system disk from filling up.

## References

- Related docs: [Templates Overview](/guide/templates)
