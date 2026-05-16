# 故障排查

::: warning 需要双语 PR
本目录下的每篇贡献都必须同时包含英文文件（`docs/guide/troubleshooting/`）和中文文件（`docs/zh/guide/troubleshooting/`）。只更新一种语言的 PR 不会被合并。
:::

本页面收集了 Cube Sandbox 在部署、配置和运行过程中的真实故障排查文章。

## 文章列表

| 文章 | 标签 |
|------|------|
| [设置 CUBE_PVM_ENABLE=1 但未使用 PVM 内核](./pvm-enable-overridden-by-env) | 部署, PVM |
| [重启后 kvm_pvm 模块未加载](./kvm-pvm-missing-after-reboot) | 部署, PVM |
| [服务器重启后 network-agent 丢失](./network-agent-missing-after-reboot) | 运维, 服务 |
| [disable_tso 字段缺失错误](./disable-tso-missing-field) | 配置, shim |
| [模板构建卡在 UNPACKING 阶段](./template-build-stuck-unpacking) | 模板, 磁盘 |
| [模板没有可用副本](./template-no-ready-replica) | 模板, 集群 |
| [envd stdout 输出被截断](./envd-stdout-truncated) | 运行时, envd |
| [envd 需要 Basic Auth 认证](./envd-basic-auth-required) | 运行时, envd, 鉴权 |
| [CoreDNS 容器反复重启](./coredns-repeatedly-restarts) | 网络, DNS |
| [磁盘空间不足](./insufficient-disk-space) | 运维, 磁盘 |

## 如何贡献

1. 复制 `_template.md` 并重命名为英文短横线命名的文件名，如 `e2b-api-401-timeout.md`。
2. 同时创建两个文件：
   - `docs/guide/troubleshooting/<slug>.md`
   - `docs/zh/guide/troubleshooting/<slug>.md`
3. 两种语言的文件名必须保持一致，以确保 URL 对齐。
4. 填写 frontmatter 字段并完成排查章节。
5. 将你的文章添加到上方中英文索引页的表格中。
6. 提交 PR，提供足够的上下文让审阅者验证问题和解决方案。
