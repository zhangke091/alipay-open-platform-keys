# alipay-open-platform-keys（Agent Skill）

**能力**：RSA2 密钥与证书模式 CSR 的本地生成（**`scripts/generate_alipay_rsa2_keys.sh`**，仅需 OpenSSL）、控制台与加验签衔接说明；排错与官方文档见 **`reference.md`**。

| 文件 | 说明 |
|------|------|
| `SKILL.md` | 流程、Agent 约束、YAML 描述（触发匹配） |
| `reference.md` | 依赖、脚本要点、报错表、文档链接、PEM/安全 |
| `scripts/generate_alipay_rsa2_keys.sh` | 公钥模式 / `--cert` 证书模式；`--help` 查看参数 |

## 安装

将本目录放到 `~/.cursor/skills/`、`~/.claude/skills/` 或项目内 `.cursor/skills/` / `.claude/skills/`（以各产品文档为准）。详见 [Cursor Skills](https://cursor.com/docs/skills)、[Claude Code Skills](https://code.claude.com/docs/en/skills)、[agentskills.io](https://agentskills.io/)。

## 开源

可单独建仓库发布；可自行添加 `LICENSE`（如 MIT）。
