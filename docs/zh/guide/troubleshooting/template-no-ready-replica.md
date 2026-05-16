---
title: 模板没有可用副本
author: yeezon
date: 2026-05-15
tags:
  - troubleshooting
  - template
  - cluster
lang: zh-CN
---

# 模板没有可用副本

## 症状

创建沙箱时 API 返回错误码 130400：`template base is not ready on any healthy node: template has no ready replica`。

## 环境

- Cube Sandbox 版本: v0.2.0
- 相关组件:  单节点或多节点,  cubelet + cubemaster

## 根因

cubelet 未向 cubemaster 报告模板就绪状态，或节点心跳未注册。可能原因：
- 模板构建完成但 cubelet 尚未同步状态到 cubemaster
- 节点到 cubemaster 的心跳连接断开
- 模板构建实际未成功完成（phase != READY）

## 解决方法

1. 检查 cubelet 日志中连接 cubemaster 的错误：

```bash
tail -f /var/log/cubelet.log
```

2. 验证模板构建已完成（phase=READY）：

```bash
curl -s http://127.0.0.1:3000/templates/base \
  -H "X-API-Key: dummy" | python3 -m json.tool
```

3. 检查 cubemaster 中的节点心跳状态。

4. 等待约 30 秒让 cubelet 注册到 cubemaster，然后重试。

5. 如果问题持续，重启 cubelet：

```bash
kill $(pgrep cubelet)
sleep 2
nohup /usr/local/services/cubetoolbox/Cubelet/bin/cubelet \
  --config /usr/local/services/cubetoolbox/Cubelet/config/config.toml \
  > /var/log/cubelet.log 2>&1 &
```

## 参考

- 相关文档: [模板检查与请求预览](/zh/guide/template-inspection-and-preview)
