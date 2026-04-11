# alipay-open-platform-keys（Agent Skill）

本目录为 **支付宝开放平台** RSA2 密钥配置的 Agent Skill：`SKILL.md` 为智能体说明，**`description` 适用范围、能力说明、使用时机**

## 安装与使用

### Cursor

| 步骤 | 操作 |
|------|------|
| 复制目录 | 将 `alipay-open-platform-keys` 放到 `~/.cursor/skills/`（全局）或项目内 `.cursor/skills/`（部分版本亦支持 `.agents/skills/`，以软件为准）。 |
| 使用 | Agent 根据对话与 `description` 自动选用；若当前版本支持 slash，可在 Agent 对话中输入 **`/alipay-open-platform-keys`**。 |
| 文档 | [Cursor：Agent Skills](https://cursor.com/docs/skills) |

### Claude Code

| 步骤 | 操作 |
|------|------|
| 复制目录 | 放到 `~/.claude/skills/alipay-open-platform-keys/`（全局）或项目 `.claude/skills/alipay-open-platform-keys/`。 |
| 使用 | 自动匹配描述，或输入 **`/alipay-open-platform-keys`**。 |
| 文档 | [Claude Code：Skills](https://code.claude.com/docs/en/skills) |

### 其他（Agent Skills 约定 / 仅阅读）

- 其他声明兼容 [Agent Skills](https://agentskills.io/) 的工具：将本目录放入其文档指定的 skills 路径。
- 不需要智能体时，可将 `SKILL.md`、`reference.md` 当作人工检查清单。

更完整的表格与注意事项见 **[reference.md](reference.md)** 中的「环境与工具」一节。

## 文件说明

| 文件 | 说明 |
|------|------|
| `SKILL.md` | 主技能（YAML `name` / `description` + 正文） |
| `reference.md` | 官方文档链接、排错表、环境与工具说明 |

