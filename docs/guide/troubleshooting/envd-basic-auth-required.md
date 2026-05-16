---
title: envd requires Basic Auth
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - runtime
  - envd
  - auth
lang: en-US
---

# envd requires Basic Auth

## Symptom

envd API requests return 401 Unauthorized.

## Environment

- Cube Sandbox version: v0.2.0
- Related components:  envd protocol port 49983/49999

## Root Cause

envd requires Basic Authentication for all API requests. The default credentials are username `root` with an empty password. Many users forget to include the Authorization header.

## Resolution

Include the `Authorization` header in all envd requests:

```bash
# Default: user=root, password=(empty)
# Base64 encoding of "root:" is cm9vdDo=
curl http://<sandbox-ip>:49983/exec \
  -H "Authorization: Basic cm9vdDo=" \
  -H "Content-Type: application/json" \
  -d '{"cmd": ["echo", "hello"]}'
```

For programmatic access (Python SDK):

```python
import base64
credentials = base64.b64encode(b"root:").decode()
headers = {"Authorization": f"Basic {credentials}"}
```

## References

- Related docs: [Authentication](/guide/authentication)
