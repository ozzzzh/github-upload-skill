# GitHub Upload Skill

一个用于 Codex 的 GitHub 项目上传 skill，帮助安全地初始化 Git 仓库、检查待上传内容、创建或关联远程仓库，并推送到 GitHub。

## 功能

- 检查 Git 和 GitHub CLI 登录状态
- 检查 Git 状态、分支、远程地址和最近提交
- 识别 `.env`、密钥、证书、Token、密码等敏感文件
- 提醒依赖目录、构建产物和大文件风险
- 支持新建 GitHub 仓库或推送到已有仓库
- 默认使用 `public` 可见性，也支持明确指定 `private`
- 支持 HTTPS 或 SSH 认证
- 推送后验证远程仓库、分支和工作区状态

## 使用

在 Codex 中调用：

```text
使用 $github-upload 将当前项目上传到 GitHub
```

也可以指定项目目录、仓库名和可见性：

```text
使用 $github-upload 上传：
项目路径：D:\Projects\my-app
仓库名：my-app
可见性：private
```

首次创建远程仓库或推送前，skill 会展示目标和待提交内容，并要求确认。它不会强制推送、删除远程仓库或覆盖远程历史。

## 前置条件

- 已安装 Git
- 推荐安装 GitHub CLI (`gh`)
- 已通过 `gh auth login` 登录 GitHub，或已配置可用的 SSH 认证

SSH 推送成功后，远程地址通常类似：

```text
git@github.com:OWNER/REPOSITORY.git
```

## README 行为

上传 skill 不会默认替项目自动创建 README。项目已有的 README 会随项目一并上传；项目缺少 README 时，skill 会提示用户。只有用户明确要求时，才会根据项目内容生成 README。

## 安全边界

不会上传 SSH 私钥、访问令牌、密码、证书或包含秘密的配置文件。公钥可以添加到 GitHub，但私钥必须只保存在本机。
