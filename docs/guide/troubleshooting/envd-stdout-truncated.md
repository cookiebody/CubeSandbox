---
title: envd stdout output truncated
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - runtime
  - envd
lang: en-US
---

# envd stdout output truncated

## Symptom

When executing commands via envd, large output is truncated to approximately 90 characters.

## Environment

- Cube Sandbox version: v0.2.0
- Related components:  envd protocol port 49983

## Root Cause

The envd protocol has a stdout buffer size limitation. Large output from command execution gets cut off. This is a known limitation of the current envd implementation.

## Resolution

For large data transfers, do not rely on stdout. Instead, start a temporary HTTP server inside the sandbox and download the file directly:

```python
# Inside the sandbox, serve the file via HTTP
import subprocess
subprocess.run(["python3", "-m", "http.server", "8888"])

# Then download from the host
curl http://<sandbox-ip>:8888/output.xlsx -o output.xlsx
```

For small outputs, the stdout method works fine. The truncation only affects larger outputs.

## References

- Related issue: Host mount and file transfer discussions
