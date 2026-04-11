# alipay-open-platform-keys（Agent Skill）

本 Skill 面向 **[支付宝开放平台](https://open.alipay.com)**：帮助在本地完成 **RSA2 应用密钥的生成、轮换与公私钥校验**，并说明如何把产物与开放平台 **开发信息**（应用公钥/证书上传、支付宝公钥获取）以及业务代码中的 **加签、回调验签** 对齐。它不替代控制台配置，但覆盖开放平台接口常用的密钥侧工作流。

**能力**：在支付宝开放平台约定的 PEM 格式下，提供 **公钥模式** 密钥对与 **证书模式** CSR 的本地生成（**`scripts/generate_alipay_rsa2_keys.sh`**，仅需 OpenSSL）、与开放平台控制台及加验签的衔接说明；密钥排错与官方文档见 **`reference.md`**。

| 文件 | 说明 |
|------|------|
| `SKILL.md` | 流程、Agent 约束、YAML 描述（触发匹配） |
| `reference.md` | 依赖、脚本要点、报错表、文档链接、PEM/安全 |
| `scripts/generate_alipay_rsa2_keys.sh` | 公钥模式 / `--cert` 证书模式；`--help` 查看参数 |
| `scripts/test_generate_alipay_rsa2_keys.sh` | 自检脚本（需 `openssl`）：`bash scripts/test_generate_alipay_rsa2_keys.sh` |

## 安装

将本目录放到 `~/.cursor/skills/`、`~/.claude/skills/` 或项目内 `.cursor/skills/` / `.claude/skills/`（以各产品文档为准）。详见 [Cursor Skills](https://cursor.com/docs/skills)、[Claude Code Skills](https://code.claude.com/docs/en/skills)、[agentskills.io](https://agentskills.io/)。
