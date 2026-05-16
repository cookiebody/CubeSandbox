# Troubleshooting

::: warning Bilingual PR Required
Every contribution in this section must include both an English file under `docs/guide/troubleshooting/` and a Chinese file under `docs/zh/guide/troubleshooting/`. PRs that update only one language will not be merged.
:::

This section collects practical troubleshooting write-ups for Cube Sandbox deployments and daily operations.

## Articles

| Article | Tags |
|---------|------|
| [PVM guest kernel not used despite setting CUBE_PVM_ENABLE=1](./pvm-enable-overridden-by-env) | deployment, PVM |
| [kvm_pvm module not loaded after reboot](./kvm-pvm-missing-after-reboot) | deployment, PVM |
| [network-agent missing after server reboot](./network-agent-missing-after-reboot) | operations, service |
| [disable_tso missing field error](./disable-tso-missing-field) | configuration, shim |
| [Template build stuck in UNPACKING phase](./template-build-stuck-unpacking) | template, disk |
| [Template has no ready replica](./template-no-ready-replica) | template, cluster |
| [envd stdout truncated](./envd-stdout-truncated) | runtime, envd |
| [envd requires Basic Auth](./envd-basic-auth-required) | runtime, envd, auth |
| [CoreDNS container repeatedly restarts](./coredns-repeatedly-restarts) | networking, DNS |
| [Insufficient disk space](./insufficient-disk-space) | operations, disk |

## How to contribute

1. Copy `_template.md` and rename it to an English kebab-case slug such as `e2b-api-401-timeout.md`.
2. Create both files at the same time:
   - `docs/guide/troubleshooting/<slug>.md`
   - `docs/zh/guide/troubleshooting/<slug>.md`
3. Keep the filename identical in both languages to keep the URLs aligned.
4. Fill in the required frontmatter fields and complete the troubleshooting sections.
5. Add your article to the table above in both the English and Chinese index pages.
6. Open a PR with enough context for reviewers to validate the issue and fix.
