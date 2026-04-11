# 参考：本地生成、依赖、报错与排错

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

## 环境与依赖

在执行任何脚本前，Agent 应提示用户完成以下检查（或代为执行 `which` / `openssl version` / `python3 -c "import cryptography"`）。

### OpenSSL（推荐作为首选生成方式）

| 检查 | 说明 |
|------|------|
| 是否安装 | 终端执行 `openssl version`，应显示 OpenSSL 版本（如 1.1.x / 3.x）。 |
| 未找到命令 | **macOS**：可安装 Xcode Command Line Tools 或通过 Homebrew 安装 `openssl`；**Windows**：安装 OpenSSL 或 Git for Windows 自带环境；**Linux**：`apt/yum` 安装 `openssl`。 |
| 权限 | 私钥文件建议 `chmod 600`，避免全局可读。 |

使用 OpenSSL 生成密钥对 **不需要** Python 或 `pip`。适合「零依赖」闭环。

### Python + cryptography（若用户需要脚本生成或后续接入同一语言）

| 依赖 | 说明 |
|------|------|
| Python | 建议 **Python 3.9+**；用 `python3 --version` 确认。 |
| 包 | `cryptography`（RSA 密钥生成与 PEM 序列化）。 |
| 安装 | `python3 -m pip install cryptography`；若项目有 `requirements.txt`，添加一行：`cryptography>=42.0.0`。 |
| 虚拟环境（推荐） | `python3 -m venv .venv && source .venv/bin/activate`（Windows 用 `.\.venv\Scripts\activate`），再 `pip install cryptography`，避免污染系统 Python。 |
| 校验 | `python3 -c "from cryptography.hazmat.primitives.asymmetric import rsa; print('ok')"` |

若 `pip install` 报 **hash 不匹配**（部分环境启用 `--require-hashes`）：可改用未锁 hash 的安装命令，或在 venv 内安装；具体策略以团队安全规范为准。

---

## 本地脚本生成密钥

与支付宝桌面**密钥工具**目标一致（得到可接入开放平台的密钥材料），在本地用命令行完成。粘贴到控制台时以页面为准（有的要求去掉 PEM 头尾）。

### 推荐：OpenSSL（无 Python 依赖）

```bash
openssl genpkey -algorithm RSA -out app_private_key.pem -pkeyopt rsa_keygen_bits:2048
openssl pkey -in app_private_key.pem -pubout -out app_public_key.pem
```

- **`app_private_key.pem`**：请求 **RSA2 加签**（勿提交）。
- **`app_public_key.pem`**：上传 **应用公钥**。

再到 [开放平台](https://open.alipay.com) → 应用 → **开发信息** 绑定公钥，并保存 **支付宝公钥** 用于回调验签。

### 备选：传统私钥转 PKCS#8

```bash
openssl pkcs8 -topk8 -inform PEM -in rsa_legacy.pem -out app_private_key.pem -nocrypt
```

### Python 最小示例（需已安装 cryptography）

仅作示意；运行前**必须**已满足上文「环境与依赖」中的 pip 检查。

```python
from cryptography.hazmat.primitives.asymmetric import rsa
from cryptography.hazmat.primitives import serialization

key = rsa.generate_private_key(public_exponent=65537, key_size=2048)
priv_pem = key.private_bytes(
    encoding=serialization.Encoding.PEM,
    format=serialization.PrivateFormat.PKCS8,
    encryption_algorithm=serialization.NoEncryption(),
)
pub_pem = key.public_key().public_bytes(
    encoding=serialization.Encoding.PEM,
    format=serialization.PublicFormat.SubjectPublicKeyInfo,
)
# 将 priv_pem / pub_pem 写入文件，勿打印到日志
```

### 支付宝桌面密钥工具（可选）

与脚本二选一即可；**不要混用两套未对齐的公私钥**。

---

## 常见报错引导（命令行与 Python）

| 报错或现象 | 含义与处理 |
|------------|------------|
| `openssl: command not found` | 未安装或不在 `PATH`。按上文「OpenSSL」安装后重开终端。 |
| `Unable to load PEM` / `bad decrypt` | PEM 内容损坏、路径错误、或私钥加密但未提供密码。检查文件路径与格式。 |
| `Permission denied` 写文件 | 当前目录无写权限；换目录或 `chmod` 父目录。 |
| `ModuleNotFoundError: No module named 'cryptography'` | 未安装依赖。执行 `python3 -m pip install cryptography`（建议在 venv 内）。 |
| `pip` 报 hash 与 requirements 不符 | 使用 venv；或暂时不用 `-r requirements.txt` 的 hash 锁；或只安装 `cryptography` 单行无 hash。 |
| `ImportError` 其它模块 | 仅使用本 Skill 示例时一般只需 `cryptography`；若自研代码还依赖其它包，按报错补装。 |

---

## 官方文档（请以文档中心最新页面为准）

| 主题 | 说明 |
|------|------|
| [生成密钥](https://opendocs.alipay.com/common/02kipl) | 密钥格式、控制台配置、公钥/证书模式 |
| [如何生成及配置 RSA2 密钥](https://opendocs.alipay.com/support/01raut) | RSA2、开发信息要点 |
| [支付宝密钥工具](https://opendocs.alipay.com/mini/02c7i5) | 官方图形工具（可选） |

---

## 证书模式与 SDK 常见配置名

- **应用公钥证书**、**支付宝公钥证书**、**支付宝根证书**（若要求）。  
- CSR 的 DN 以开放平台文档为准。

---

## 常见业务侧错误（密钥相关）

| 现象或文案 | 常见原因 | 处理方向 |
|------------|----------|----------|
| **验签失败** / **invalid signature** | 公私钥不成对；支付宝公钥错误；待签参数被编码破坏 | 核对密钥与控制台；检查 query/body 解析 |
| **sub_code 与签名相关**（以文档为准） | 加签缺参、`sign_type` 不一致 | 对照待签串与 RSA2 |
| 沙箱与正式混用 | APPID、网关、密钥交叉 | 分环境配置 |
| 仅回调失败 | 支付宝公钥或 URL 解析问题 | 更新公钥；区分 `sign` 与其它字段解码 |

---

## 注意事项（安全与运维）

1. 私钥泄露：立即轮换控制台公钥与本地私钥。  
2. 加签方式与控制台一致（公钥 / 证书）。  
3. 勿将 `*.pem`、本地 env 提交仓库。  
4. 勿在即时通讯中发送完整私钥。

## PEM 与回调参数

- **`BEGIN PRIVATE KEY`**：PKCS#8 私钥。  
- **`BEGIN PUBLIC KEY`**：SPKI。  
- **`BEGIN CERTIFICATE`**：先解析证书再取公钥。  

同步跳转中 **`sign` 为 Base64**，勿破坏其中的 `+`。

## 开源打包与分发

将 `alipay-open-platform-keys` 目录复制到各环境的 skills 路径即可。
