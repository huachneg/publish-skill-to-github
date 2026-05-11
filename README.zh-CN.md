# publish-skill-to-github

[English README](README.md)

这是一个用于将本地 Agent skill 安全发布或同步到 GitHub 的 skill。

它会引导 AI Agent 完成：定位本地 skill 目录、检查必要文件、扫描敏感信息和风险文件、准备公开说明文档、选择当前环境里最可靠的 GitHub 发布方式，以及发布后读回校验。

## 能做什么

- 定位不同 Agent 环境中的本地 skill 目录。
- 发布前检查 skill 结构。
- 扫描 API Key、token、私钥、密码、本地专用文件、生成产物和大体积二进制文件。
- 帮助准备公开、平台中性的 README。
- 支持通过 GitHub 工具、本地 Git，或用户浏览器辅助来创建和更新仓库。
- 发布后验证 GitHub 上的关键文件。

## 使用要求

- 一个支持本地 skills 的 AI Agent 环境。
- GitHub 访问能力，任选其一：
  - 已连接的 GitHub App 或 API 工具。
  - 本地已认证的 Git。
  - 用户可以在浏览器中创建仓库或完成授权。

这个 skill 本身不需要 API Key。

## 安装

### 方式一：一行命令安装

```bash
npx skills add https://github.com/huachneg/publish-skill-to-github --skill publish-skill-to-github
```

### 方式二：把下面这段话直接发给 AI

> 帮我安装 `publish-skill-to-github` 这个 Agent skill。请按下面步骤做：
>
> 1. 确保 `~/.claude/skills/` 目录存在，不存在就创建。
> 2. 执行 `git clone https://github.com/huachneg/publish-skill-to-github.git ~/.claude/skills/publish-skill-to-github`。
> 3. 验证该目录里能看到 `SKILL.md` 和 `agents/openai.yaml`。
> 4. 告诉我安装好了。

如果你的 agent 使用的不是 `~/.claude/skills/`，把它替换成该 agent 配置的 skill / plugin 目录即可。

### 方式三：手动命令行

```bash
mkdir -p ~/.claude/skills
git clone https://github.com/huachneg/publish-skill-to-github.git ~/.claude/skills/publish-skill-to-github
```

PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\skills"
git clone https://github.com/huachneg/publish-skill-to-github.git "$HOME\.claude\skills\publish-skill-to-github"
```

## 文件结构

- `SKILL.md`：skill 工作流和发布指引。
- `agents/openai.yaml`：OpenAI agent 展示元数据。

## 安全说明

发布任何 skill 前，这个 skill 都会要求 Agent 扫描敏感信息和风险文件。如果发现真实凭据，应立即停止发布，清理凭据，并在必要时轮换密钥。
