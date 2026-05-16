---
title: envd 需要 Basic Auth 认证
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - runtime
  - envd
  - auth
lang: zh-CN
---

# envd 需要 Basic Auth 认证

## 症状

envd API 请求返回 401 Unauthorized。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  envd 协议端口 49983/49999

## 根因

envd 对所有 API 请求需要 Basic Authentication。默认凭证为用户名 `root`，密码为空。很多用户忘记包含 Authorization 头。

## 解决方法

在所有 envd 请求中包含 `Authorization` 头：

```bash
# 默认: user=root, password=(空)
# "root:" 的 Base64 编码是 cm9vdDo=
curl http://<sandbox-ip>:49983/exec \
  -H "Authorization: Basic cm9vdDo=" \
  -H "Content-Type: application/json" \
  -d '{"cmd": ["echo", "hello"]}'
```

编程访问（Python SDK）：

```python
import base64
credentials = base64.b64encode(b"root:").decode()
headers = {"Authorization": f"Basic {credentials}"}
```

## 参考

- 相关文档: [鉴权](/zh/guide/authentication)
