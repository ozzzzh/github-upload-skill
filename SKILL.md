---
name: github-upload
description: 将本地项目安全地初始化、提交并推送到 GitHub；适用于首次上传项目、关联已有 GitHub 仓库或更新远程仓库。涉及远程仓库创建和 push 时，必须先确认目标与用户授权。
---

# GitHub 项目上传

使用本 skill 将当前项目或用户指定的本地项目上传到 GitHub。目标是完成可验证的 Git 提交和远程推送，同时避免误传敏感文件或覆盖远程历史。

## 工作流程

1. 确认项目目录、GitHub 账户、仓库名、可见性（`public` 或 `private`）以及是否要创建新仓库。若用户没有说明，优先使用当前工作目录、目录名作为仓库名，默认可见性为 `public`；用户明确指定 `private` 时按 `private` 处理。无论采用哪种默认值，在真正创建远程仓库或 push 前都要向用户确认目标与授权。
2. 检查工具：
   - `git` 必须可用。
   - 优先使用 `gh` 管理 GitHub 仓库和认证；运行 `gh auth status`。
   - 如果 `gh` 不可用，说明需要用户先安装 GitHub CLI，或让用户提供已经存在的 HTTPS/SSH 远程地址；不要把令牌、密码或私钥写入命令、文件或聊天记录。
3. 检查仓库状态：
   - 使用 `git status --short`, `git branch --show-current`, `git remote -v`, `git log --oneline -5`。
   - 如果还不是 Git 仓库，先检查项目内容，再执行 `git init`。
   - 如果已经存在远程仓库，先核对远程 URL 和当前分支；不要擅自更换或删除远程。
4. 在添加文件前检查敏感信息和大文件。至少检查 `.env*`、私钥、证书、云服务凭据、token、密码、构建产物、依赖目录和明显的大文件。将应排除的内容加入 `.gitignore`，但不要覆盖用户已有的 `.gitignore`；修改前先读取并保留其规则。
5. 展示拟提交的文件摘要和目标远程仓库。若发现疑似密钥、未跟踪的大文件、已有远程历史，暂停并向用户说明，除非用户明确授权继续。
6. 在用户授权后执行：
   - `git add` 只添加确认过的项目文件。
   - 用清晰的提交信息执行 `git commit`。
   - 新仓库默认将当前分支规范为 `main`，但若项目已有明确分支约定则保留。
   - 新远程仓库优先使用 `gh repo create <owner>/<repo> --source=. --remote=origin --push`；如果仓库已存在，则使用现有 remote 并执行普通 `git push -u origin <branch>`。
7. 推送后验证：检查 `git status`, `git remote -v`, `git log -1 --oneline`，并用 `gh repo view --web` 或 `gh repo view` 确认远程仓库可访问。

## 安全边界

- 永远不要使用 `git push --force`、`--force-with-lease`、删除远程仓库或覆盖远程分支，除非用户明确提出并确认风险。
- 永远不要提交 `.env`、`.env.local`、SSH 私钥、云凭据、访问令牌、密码、签名证书或包含秘密的配置文件。
- 不要为了“上传成功”删除用户文件、重写已有提交历史或清空远程仓库。
- 如果远程分支包含本地没有的提交，先停止并说明需要选择合并、rebase 或新分支；不要直接强推。
- 对 GitHub 认证失败，提示用户在终端运行 `gh auth login`，不要索要或代替用户保存凭据。
- 文件扫描或状态检查发现的问题要如实报告；不要把警告隐藏在成功消息后面。

## 常用命令

```powershell
gh auth status
git status --short
git remote -v
git branch --show-current
git add <confirmed-files>
git commit -m "Initial commit"
git push -u origin main
gh repo view
```

如果用户明确要求只上传到已存在的远程地址，可以跳过创建仓库步骤，但仍需完成敏感信息检查、文件确认和 push 前授权。
