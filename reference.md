# 参考：官方文档、工具与排错

## 环境与工具

以下说明 **Cursor**、**Claude Code** 及 **Agent Skills** 开放约定下的安装与调用方式。

### 通用说明

本目录为 **Agent Skills** 形态的技能包（入口文件 `SKILL.md`），遵循开放约定 [agentskills.io](https://agentskills.io/)，可被多种智能体开发工具识别。`name` 与 YAML 中的 `description` 用于**发现与触发**；正文为智能体执行时的操作说明。无需智能体时，也可将 `SKILL.md`、`reference.md` 当作人工检查清单阅读。

### 在 Cursor 中使用

| 项目 | 说明 |
|------|------|
| **安装位置** | 用户级：`~/.cursor/skills/alipay-open-platform-keys/`；项目级：`.cursor/skills/alipay-open-platform-keys/`。部分版本亦支持 `.agents/skills/`（以当前软件为准）。 |
| **加载方式** | 由 Agent 根据 `description` 与对话内容**自动匹配**；若支持 slash，可在 Agent 对话中输入 **`/alipay-open-platform-keys`** 显式调用（以 Cursor 当前版本行为为准）。 |
| **管理** | 可在 **Cursor Settings → Rules** 等入口查看与 Agent、规则相关的配置（界面随版本变化）。 |
| **权威文档** | [Cursor 文档：Agent Skills](https://cursor.com/docs/skills) |

### 在 Claude Code 中使用

| 项目 | 说明 |
|------|------|
| **安装位置** | 用户级：`~/.claude/skills/alipay-open-platform-keys/`；项目级：`.claude/skills/alipay-open-platform-keys/`。单仓多包时，子目录下亦可使用嵌套的 `.claude/skills/`。 |
| **加载方式** | 与 `description` 匹配时自动选用；或在对话中输入 **`/alipay-open-platform-keys`**（`name` 字段与 slash 命令名一致）。 |
| **权威文档** | [Claude Code：Extend Claude with skills](https://code.claude.com/docs/en/skills) |

### 其他方式

- **仅文档化使用**：复制仓库到团队 Wiki 或内部规范，不要求安装 Cursor/Claude Code。
- **其他兼容 Agent Skills 的 IDE/CLI**：将本目录放入各产品文档指定的 `skills` 目录即可；路径与 slash 语法以**各产品官方文档**为准。

---

## 官方文档（请以文档中心最新页面为准）

以下链接为支付宝开放平台 **文档中心** 常用入口（路径可能随改版调整，检索关键词：**生成密钥**、**RSA2**、**接口加签**）：

| 主题 | 说明 |
|------|------|
| [生成密钥](https://opendocs.alipay.com/common/02kipl) | 密钥生成步骤、工具使用、公钥/证书模式说明 |
| [如何生成及配置 RSA2 密钥](https://opendocs.alipay.com/support/01raut) | RSA2 配置、控制台操作要点 |
| [支付宝密钥工具](https://opendocs.alipay.com/mini/02c7i5) | 密钥工具功能与使用说明（Windows / macOS） |

开放平台首页：[https://open.alipay.com](https://open.alipay.com) → 控制台 → 对应应用 → **开发设置** 中配置密钥与查看 **APPID**。

**说明**：文档中若提供密钥工具 **下载地址**，应使用文档当前页链接下载，避免使用来路不明的安装包。

## 官方密钥工具（摘要）

- **用途**：生成 RSA2 密钥对、在证书模式下生成 **CSR** 等，与控制台「接口加签方式」配合使用。
- **平台**：常见为 **Windows、macOS**；部分文档或社区会提及 **Homebrew** 安装方式（如 `alipay-key-tool`），以**文档中心当前说明**为准。
- **原则**：私钥仅在本地安全保存；上传控制台的应是 **应用公钥** 或按流程提交 **CSR**，不要把私钥上传到任何非受控页面。

## 证书模式与 SDK 常见配置名

若使用 Java/PHP 等官方 SDK 的 **证书模式**，文档中常出现三类文件（名称因 SDK 而异）：

- **应用公钥证书**（应用身份）
- **支付宝公钥证书**
- **支付宝根证书**（校验证书链）

三者路径需与代码/SDK 配置一致；根证书或链错误时，可能出现验签或 TLS 相关问题，需对照文档排查。

## 常见错误提示与含义（排查密钥时）

| 现象或文案 | 常见原因 | 处理方向 |
|------------|----------|----------|
| **验签失败** / **invalid signature** / **INVALID_SIGNATURE** | 应用私钥与控制台 **应用公钥** 不是一对；或回调侧 **支付宝公钥** 非当前应用/已过期；或待签参数被改写（含编码、`+` 与空格） | 核对密钥对与控制台；回调单独核对支付宝公钥；检查 query/body 解析 |
| **sub_code 与签名相关**（具体码以文档为准） | 请求未按规范加签、缺参、`sign_type` 与算法不一致 | 对照待签串规则与 `RSA2` |
| 沙箱与正式混用 | 沙箱 APPID、网关、密钥与正式环境交叉配置 | 沙箱、正式 **分别** 配置密钥与网关 |
| 证书过期 | 应用证书或支付宝证书超过有效期 | 控制台续期或重新申请，更新本地路径与部署 |
| 仅回调失败、下单成功 | 多为 **支付宝公钥** 配置错误或 URL 解码问题 | 更新公钥；解析参数时对 `sign` 与其它字段区分解码 |

**注意**：开放平台返回的 **具体错误码与 sub_msg** 以当时接口文档及响应为准，本表仅作分类参考。

## 注意事项（安全与运维）

1. **私钥泄露**：视为严重安全事件，应立即在控制台轮换 **应用公钥/证书** 并更换私钥，检查审计日志。
2. **加签方式一致**：控制台选「公钥」或「证书」须与代码/SDK 一致；混用会导致请求验签失败。
3. **勿提交密钥**：`.gitignore` 忽略 `*.pem`、`*.env`、`*local.env`；CI 使用密钥变量或密钥管理服务。
4. **多人协作**：通过安全通道传递密钥片段；禁止在即时通讯中发送完整私钥文件。

## PEM 格式与验签实现

- **`BEGIN PRIVATE KEY`**：PKCS#8 私钥，常见于工具导出。
- **`BEGIN PUBLIC KEY`**：SPKI 公钥，常用于「支付宝公钥」文本配置。
- **`BEGIN CERTIFICATE`**：需按 X.509 加载后再取公钥，不可与 SPKI 公钥 PEM 混用。

同步跳转 GET 参数中 **`sign` 为 Base64**，解析查询串时需避免把 `sign` 内的 `+` 误当作空格；其它参数如 `timestamp` 中 `+` 表示空格的行为需与待签串一致（见各项目解析实现）。

## 开源打包与分发

克隆或下载本仓库后，将 `alipay-open-platform-keys` 目录复制到目标环境的 skills 路径（见上文 **环境与工具**）。发布到 GitHub 时，建议在 README 中附上 Cursor、Claude Code 两节安装说明及官方文档链接。
