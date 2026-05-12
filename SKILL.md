---
name: publish-skill-to-github
description: 将本地 AI Agent skill 安全发布或同步到 GitHub。用户要求分享、发布、上传、推送、同步、更新、维护某个 skill 仓库时使用；适用于 Codex、Claude Code、Cursor 或其他 AI Agent 的 skill，包含 README/中文 README、安装说明、.gitignore、敏感信息扫描、跨平台路径和 GitHub 更新流程。
---

# 发布 Skill 到 GitHub

## 目标

把本地 skill 文件夹安全发布到 GitHub，避免泄露密钥，并让仓库说明适合 Codex、Claude Code、Cursor 或其他 AI Agent 用户阅读。除非用户明确说明，否则不要把 skill 写成某一个 agent 专用。

## 总流程

1. 定位本地 skill 文件夹。
2. 检查目录结构和必要文件。
3. 上传前扫描敏感信息和不该发布的文件。
4. 准备或更新公开文档。
5. 用当前环境里最可靠的方式发布到 GitHub。
6. 读回 GitHub 仓库确认发布成功。
7. 简短说明更新了什么，以及以后如何继续同步。

## 定位 Skill

用户给了路径就直接使用。没给路径时，优先检查常见位置，但不要假设只能在某一个 agent 中使用：

- Claude Code：`~/.claude/skills/<skill-name>`
- Codex：`$CODEX_HOME/skills/<skill-name>` 或 `~/.codex/skills/<skill-name>`
- Cursor 或其他 agent：如果无法自动发现，询问它们配置的 skills/plugins 目录
- 当前工作区：用户可能已经有一个发布副本或 Git 仓库

对用户解释时使用平台中性的说法。路径只作为示例，不要暗示只能在 Windows、macOS 或某个 agent 里使用。

## 发布前检查

先读取文件列表，再决定如何发布。一个 skill 通常包括：

- `SKILL.md`：必需入口文件
- `scripts/`、`references/`、`assets/`：按需存在
- `README.md`、`README.zh-CN.md`：公开说明，可选但推荐
- `.gitignore`：推荐

不要假设每个 skill 都有 `assets/` 或 `references/`。README 里的验证步骤必须和真实文件结构一致。

## 敏感信息与风险文件扫描

任何 GitHub 写入前，都要扫描将要发布的文件。重点检查：

- `.env`、`.env.*`
- API Key、Bearer token、GitHub token、私钥、密码
- 看起来像真实密钥的字符串，例如 `sk-...`、`ghp_...`、`github_pat_...`
- 不应公开的本地账号路径
- 生成产物：图片、视频、日志、缓存、build 输出
- 大体积二进制文件，除非它们确实是 skill 的必要 assets

如果发现真实密钥，立即停止上传，告诉用户哪个文件需要清理。不要把密钥发到 GitHub。

可用的扫描模式示例：

```text
sk-[A-Za-z0-9]{12,}
ghp_[A-Za-z0-9]+
github_pat_
BEGIN (RSA|OPENSSH|PRIVATE) KEY
password\s*=
token\s*=
secret\s*=
```

扫描时排除 `.git/`。

## README 写法

公开 README 默认保持 agent 中性：

- 优先写 "Agent skill"，不要写成 "Codex skill"，除非仓库确实只服务 Codex。
- 可以把 Codex、Claude Code、Cursor 和其他 agent 当作示例。
- 如果 skill 依赖外部平台，把平台入口链接放在“使用要求 / Requirements”附近。
- 写清楚环境变量，但示例只能使用占位 key，例如 `sk-xxxxxxxx`。
- 不要放真实 API Key。

双语仓库推荐：

- `README.md`：英文默认版
- `README.zh-CN.md`：中文版
- 两个文件顶部互相链接

推荐安装说明结构：

````markdown
## 安装

### 方式一：一行命令安装（推荐）

```bash
npx skills add https://github.com/<owner>/<repo> --skill <skill-name>
```

### 方式二：把下面这段话直接发给 AI

> 帮我安装 `<skill-name>` 这个 Agent skill。请按下面步骤做：
>
> 1. 确保 `~/.claude/skills/` 目录存在，不存在就创建。
> 2. 执行 `git clone https://github.com/<owner>/<repo>.git ~/.claude/skills/<skill-name>`
> 3. 验证该目录里应该看到 `SKILL.md` 和实际存在的目录，例如 `scripts/`。
> 4. 告诉我安装好了。

如果你的 agent 使用的不是 `~/.claude/skills/`，把它替换成该 agent 配置的 skill / plugin 目录即可。

### 方式三：手动命令行

```bash
git clone https://github.com/<owner>/<repo>.git ~/.claude/skills/<skill-name>
```
````

英文 README 使用相同结构，翻译成 `Installation / Option 1 / Option 2 / Option 3` 即可。

## GitHub 发布路径选择

优先使用当前环境里最可靠的方式：

1. GitHub connector / API 工具：账号已连接且仓库已存在时优先使用，尤其适合本机 Git 认证或网络不稳定的环境。
2. 本地 Git：本机 Git 认证、TLS、权限都正常时使用。
3. 浏览器辅助：仅在能控制浏览器，并且用户能自己处理登录、密码和 2FA 时使用。
4. 让用户先创建空仓库：当无法自动创建 repo 时，让用户创建后发送 URL，再继续上传文件。

不要要求用户在聊天里粘贴密码或 token。需要认证时，让用户在 GitHub 官方页面完成登录或授权。

### 本机 Git 可用性预检

不要因为本地有 `git` 命令就默认能 push。发布前先快速确认：

- `gh` 是否存在且 `gh auth status` 可用。
- `git credential fill` 或系统 credential helper 是否能返回 GitHub 凭据。
- 远端仓库是否已存在，且当前账号有 push 权限。
- 网络是否能稳定访问 `github.com:443`。

如果出现下列任意现象，不要反复重试 `git push`，直接切到 GitHub connector / API 或浏览器辅助：

- `could not read Username for 'https://github.com': Device not configured`
- `Failed to connect to github.com port 443`
- `Empty reply from server`
- TLS、ACL、permission denied、repository not found 等认证或网络类错误

本地 Git 失败后，要向用户说明这是运行环境的 Git 凭据或网络问题，不是 skill 内容本身的问题。

### GitHub 网页上传注意事项

GitHub 的 Upload files 页面适合上传根目录文件，但不要依赖它保留本地子目录结构。拖入 `scripts/run.py` 这类文件时，网页端可能只上传为根目录 `run.py`，导致仓库结构错误。

对带目录的文件优先使用：

- GitHub contents API / connector：按路径创建或更新，例如 `scripts/run.py`。
- GitHub 网页的 New file 页面：在文件名输入框中明确输入完整路径，例如 `scripts/run.py`。
- 低层 Git API：创建 blobs、tree、commit，再移动 branch ref。

如果不得不用 Upload files 页面，上传后必须检查文件路径；发现被拍扁到根目录时，先删除错误文件，再用正确路径重新创建。

## 创建或更新仓库

如果仓库不存在，且当前工具不能创建 GitHub repo，给用户最短指令：

```text
创建一个名为 <skill-name> 的空 GitHub 仓库，不要勾选 README、.gitignore 或 license。创建后把仓库 URL 发给我。
```

仓库已存在时：

- 本地 Git 可用：添加 remote 并 push。
- 本地 Git 因认证、TLS、ACL、权限或系统环境失败：改用 GitHub connector / API 逐文件创建或更新。
- 用 API 更新文件前，先 fetch 远程文件拿到 SHA。
- 不要盲目覆盖远程文件；如果远程已有用户修改，先比较或至少读回确认。
- 用网页上传时，不要把子目录文件当作普通上传文件处理；`scripts/run.py`、`references/foo.md` 等必须验证远端路径仍然带目录。
- 如果 connector 只有逐文件 contents API，按以下顺序处理：先创建 README 或根目录小文件让仓库初始化，再逐个创建 `.gitignore`、`SKILL.md`、`scripts/...` 等文件。
- 如果一次性提交很重要且 connector 暴露 blobs/tree/commit/ref 工具，优先用低层 Git API 做单提交；否则接受多次小提交，但最终回复中说明使用了 connector / 网页辅助。

## 跨平台命令原则

公开 README 中优先使用 POSIX 风格命令，因为 macOS/Linux/很多 agent 环境都容易理解：

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/<owner>/<repo>.git ~/.claude/skills/<skill-name>
```

需要照顾 Windows 用户时，可以补 PowerShell 示例：

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\skills"
git clone https://github.com/<owner>/<repo>.git "$HOME\.claude\skills\<skill-name>"
```

不要让说明变成 Windows-only、macOS-only 或 Codex-only。

## 发布后校验

发布完成后至少确认：

- GitHub 上存在必要文件：`SKILL.md`、README、脚本或资源目录。
- 敏感信息扫描通过。
- 如果 skill 有可执行脚本，本地能运行基本帮助命令或最小 smoke test。
- README 里的 GitHub owner、repo、skill name、安装路径都正确。
- 能从 GitHub 读回至少一个关键文件，证明更新已经落地。
- 对所有带子目录的关键文件，必须按完整路径读回，例如 `scripts/run.py`，并确认根目录没有误上传的 `run.py`。
- 如果曾使用网页 Upload files 页面，额外检查是否有被拍扁的重复文件；发现 `run.py`、`foo.md` 等错误根目录文件时，删除后再结束。
- 记录最终远端最新提交 SHA；如果过程产生多个提交，优先报告最新 SHA，并简短说明原因。

## 最终回复格式

简短告诉用户：

- GitHub 仓库 URL
- 创建或更新了哪些文件
- 提交 SHA，如果可用
- 是否有特殊限制，例如本地 Git 不可用、改用 connector 更新
- 以后用户该如何要求继续同步

不要把底层工具日志堆给用户，除非用户明确要求。
