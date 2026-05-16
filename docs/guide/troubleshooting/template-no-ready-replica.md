---
title: Template has no ready replica
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - template
  - cluster
lang: en-US
---

# Template has no ready replica

## Symptom

API returns error code 130400: `template base is not ready on any healthy node: template has no ready replica` when creating a sandbox.

## Environment

- Cube Sandbox version: v0.2.0
- Related components:  Single-node or Multi-node,  cubelet + cubemaster

## Root Cause

The cubelet has not reported template readiness to cubemaster, or the node heartbeat is not registered. This can happen when:
- The template build completed but cubelet has not yet synced status to cubemaster
- Node heartbeat connection to cubemaster is broken
- The template build did not actually complete successfully (phase != READY)

## Resolution

1. Check cubelet logs for cubemaster connection errors:

```bash
tail -f /var/log/cubelet.log
```

2. Verify template build completed successfully (phase=READY):

```bash
curl -s http://127.0.0.1:3000/templates/base \
  -H "X-API-Key: dummy" | python3 -m json.tool
```

3. Check node heartbeat status in cubemaster.

4. Wait ~30 seconds for cubelet to register with cubemaster, then retry sandbox creation.

5. If the issue persists, restart cubelet:

```bash
kill $(pgrep cubelet)
sleep 2
nohup /usr/local/services/cubetoolbox/Cubelet/bin/cubelet \
  --config /usr/local/services/cubetoolbox/Cubelet/config/config.toml \
  > /var/log/cubelet.log 2>&1 &
```

## References

- Related docs: [Template Inspection & Request Preview](/guide/template-inspection-and-preview)
