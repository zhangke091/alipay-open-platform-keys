# alipay-open-platform-keys（Agent Skill）

**核心能力**：在本 Skill 与 `reference.md` 内即可闭环 **密钥生成 → 控制台绑定应用公钥 → 配置与加验签**（优先 **OpenSSL**，无需 pip）；若用 **Python**，先按 `reference.md` 检查 **`cryptography`** 等依赖。执行失败时按 **常见报错引导** 排查。**不依赖** MCP 或桌面密钥工具（控制台网页绑定与官方工具仅作必要/可选补充）。

- 主说明：`SKILL.md`（YAML `description` + 端到端流程）
- 命令与排错：`reference.md`（含 OpenSSL 示例、文档链接）

## 安装与使用

### Cursor

| 步骤 | 操作 |
|------|------|
| 复制目录 | 将 `alipay-open-platform-keys` 放到 `~/.cursor/skills/`（全局）或项目内 `.cursor/skills/`（部分版本亦支持 `.agents/skills/`，以软件为准）。 |
| 使用 | Agent 根据对话与 `description` 自动选用；若支持 slash，可输入 **`/alipay-open-platform-keys`**。 |
| 文档 | [Cursor：Agent Skills](https://cursor.com/docs/skills) |

### Claude Code

| 步骤 | 操作 |
|------|------|
| 复制目录 | 放到 `~/.claude/skills/alipay-open-platform-keys/`（全局）或项目 `.claude/skills/alipay-open-platform-keys/`。 |
| 使用 | 自动匹配描述，或 **`/alipay-open-platform-keys`**。 |
| 文档 | [Claude Code：Skills](https://code.claude.com/docs/en/skills) |

### 其他

- 兼容 [Agent Skills](https://agentskills.io/) 的工具：按各产品文档放入 skills 目录。
- 无需 Agent 时，仍可把本仓库当**本地密钥生成与配置清单**阅读。

更完整的安装说明见 **[reference.md](reference.md)**「环境与工具」一节。

## 文件说明

| 文件 | 说明 |
|------|------|
| `SKILL.md` | 主技能：端到端流程与 Agent 约束 |
| `reference.md` | OpenSSL 示例、官方文档、排错、Cursor/Claude 安装 |

## 开源

可将本目录单独作为 GitHub 仓库发布；根目录可自行添加 `LICENSE`（如 MIT）。
