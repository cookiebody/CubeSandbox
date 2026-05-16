---
title: envd stdout 输出被截断
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - runtime
  - envd
lang: zh-CN
---

# envd stdout 输出被截断

## 症状

通过 envd 执行命令时，大量输出被截断到约 90 个字符。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  envd 协议端口 49983

## 根因

envd 协议的 stdout 缓冲区大小有限制，大量输出会被截断。这是当前 envd 实现的已知限制。

## 解决方法

对于大数据传输，不要依赖 stdout。改为在沙箱内启动临时 HTTP 服务器，然后直接下载文件：

```python
# 在沙箱内通过 HTTP 提供文件服务
import subprocess
subprocess.run(["python3", "-m", "http.server", "8888"])

# 然后从宿主机下载
curl http://<sandbox-ip>:8888/output.xlsx -o output.xlsx
```

对于小量输出，stdout 方法正常。截断只影响大量输出。

## 参考

- 相关 issue: 宿主机挂载和文件传输讨论
