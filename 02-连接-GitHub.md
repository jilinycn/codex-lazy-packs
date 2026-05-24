# Codex 懒人包 #02：连接 GitHub

> 版本：v0.3（Codex Desktop 版）
> 更新日期：2026-04-27

> 本懒人包可独立执行：会先检查 Git、GitHub CLI、登入状态，再用网页端登入完成 GitHub CLI 授权，接著引导连接 Codex Desktop 的 GitHub connector。

---

## 这个懒人包会帮你做什么？

让 Codex 可以帮你操作 GitHub，包括：

- 建立 GitHub repo
- 把 Codex 做好的网页、教材、互动 HTML 推送上线
- 开启 GitHub Pages，让学生扫 QR Code 就能开启
- 之后用自然语言完成 commit、push、Pages 发布
- 在 Codex 对话中读取 GitHub repo、issue、PR 内容

> [!important]
> GitHub 连线不是只靠 Codex 本身完成，而是靠电脑上的 Git、GitHub CLI，以及 GitHub 帐号授权。Codex 的角色是帮你检查、执行、判读错误、一步一步带你完成。

> [!tip]
> 完整连线分成两段：`gh` CLI 负责本机 push / 建 repo / 开 Pages；Codex Desktop 的 GitHub connector 负责让 Codex 在对话里读取 GitHub repo、issue、PR。

---

## 适用情境

本懒人包以 **Codex Desktop app（Windows）** 为主线，并使用 PowerShell 指令。

也可套用到：

| 工具 | 说明 |
|------|------|
| Codex Desktop app | 推荐，新手照本懒人包走 |
| Codex IDE 扩充 | 可沿用同样的 Git / gh 设定 |
| Codex CLI | 可沿用同样指令，但操作画面不同 |

---

## 先备条件

- [ ] Codex Desktop app 已安装并能正常对话
- [ ] 有 GitHub 帐号；如果没有，请先到 <https://github.com/signup> 注册
- [ ] 电脑有网路连线
- [ ] 愿意在浏览器完成一次 GitHub 授权

---

## 请 Codex 帮我执行以下步骤

> [!warning]
> 以下是给 Codex 读的操作流程。遇到需要登入、授权、输入验证码的地方，Codex 会停下来请使用者手动完成。

---

## 步骤零：环境检查

请 Codex 先检查目前环境，不要直接假设工具都已安装。

### 0.1 检查 Git

```powershell
git --version
```

成功时会看到类似：

```text
git version 2.53.0.windows.1
```

如果没有安装 Git：

```powershell
winget install --id Git.Git --accept-source-agreements --accept-package-agreements
```

安装完若 Codex 还是找不到 `git`，请重新启动 Codex Desktop app。

### 0.2 检查 GitHub CLI

```powershell
gh --version
```

如果没有安装 GitHub CLI：

```powershell
winget install --id GitHub.cli --accept-source-agreements --accept-package-agreements
```

也可以到 GitHub CLI 官方页面下载安装：

<https://cli.github.com/>

Windows 常见安装位置：

```text
C:\Program Files\GitHub CLI\gh.exe
```

### 0.3 检查 Git 使用者资讯

```powershell
git config --global user.name
git config --global user.email
```

如果没有设定，请使用者提供姓名与 email，再设定：

```powershell
git config --global user.name "你的姓名"
git config --global user.email "你的email@example.com"
```

---

## 步骤一：确认 GitHub 登入状态

先检查是否已登入：

```powershell
gh auth status
```

成功时应该看到类似：

```text
github.com
  ✓ Logged in to github.com account your-github-name
  - Active account: true
  - Git operations protocol: https
  - Token scopes: 'gist', 'read:org', 'repo'
```

重点确认：

| 栏位 | 要看到什么 |
|------|------------|
| account | 是你的 GitHub 帐号 |
| Active account | `true` |
| Git operations protocol | `https` |
| Token scopes | 至少要包含 `repo` |

> [!tip]
> 如果你只是要操作公开 repo，有些权限可以比较少；但如果 Codex 要帮你建立、推送、修改私有 repo，建议确认 scope 有 `repo`。

---

## 步骤二：用网页端登入 GitHub

如果 `gh auth status` 显示尚未登入，请执行：

```powershell
gh auth login --web --git-protocol https
```

接著会进入网页端授权流程：

1. PowerShell 会显示一组一次性验证码。
2. 浏览器通常会自动开启 GitHub 授权页面。
3. 如果浏览器没有自动开启，手动开启：

```text
https://github.com/login/device
```

4. 在 GitHub 页面输入验证码。
5. 点选授权。
6. 回到 Codex / PowerShell，确认登入完成。

登入后再检查一次：

```powershell
gh auth status
```

> [!important]
> 这次实测成功的路线就是网页端登入。不要卡在纯终端机输入帐密，GitHub 现在通常会要求走浏览器授权。

---

## 步骤三：连接 Codex Desktop 的 GitHub connector

完成 `gh` CLI 登入后，建议再把 Codex Desktop 内建的 GitHub connector 也连起来。

这一步不是 PowerShell 指令，而是在 Codex Desktop app 的设定介面完成。

### 3.1 打开 Codex 的连接器设定

请使用者在 Codex Desktop app 里依序寻找类似位置：

```text
Settings / 设定
Connectors / Apps / Integrations
GitHub
Connect / Sign in / Install
```

不同版本介面文字可能略有差异，重点是找到 GitHub 连接器或 GitHub App。

### 3.2 用浏览器登入并授权

Codex 会开启 GitHub 网页授权流程。

请使用者依画面完成：

1. 登入 GitHub 帐号。
2. 选择要授权的帐号或 organization。
3. 选择允许 Codex 存取的 repo 范围。
4. 点选 Install / Authorize / Connect。
5. 回到 Codex Desktop app。

> [!tip]
> 新手建议先授权自己的个人帐号，repo 范围可依需求选「全部 repo」或「只选指定 repo」。如果之后 Codex 找不到某个 repo，通常是 GitHub App 没有被授权到那个 repo。

### 3.3 请 Codex 验证 connector 是否成功

连接完成后，请使用者在 Codex 对话中输入：

```text
帮我确认 GitHub connector 是否已连接，并列出我可以存取的 GitHub 帐号与几个 repo。
```

成功时，Codex 应该能看到：

| 检查项目 | 成功状态 |
|----------|----------|
| GitHub 帐号 | 看得到使用者帐号 |
| installation | 看得到 GitHub App 安装资讯 |
| repo 权限 | 看得到 repo，并能辨识 pull / push / admin 等权限 |

### 3.4 connector 失败时怎么办

如果 Codex 显示：

```text
accounts: []
installations: []
repositories: []
```

通常代表 GitHub connector 还没有真的连上。

处理方式：

1. 回到 Codex Desktop 的 GitHub connector 设定页。
2. 重新 Connect / Sign in / Install。
3. 确认浏览器登入的是正确 GitHub 帐号。
4. 确认 GitHub App 有授权到需要的 repo。
5. 回到 Codex，重新请它检查 connector。

> [!important]
> `gh auth status` 成功，只代表本机 GitHub CLI 登入成功；不代表 Codex Desktop connector 一定已连接。两个都成功，才是完整 GitHub 工作流。

---

## 步骤四：建立测试 repo 验证

为了确认 Codex 真的能写入 GitHub，建议建立一个测试 repo。

### 4.1 建立测试资料夹

```powershell
New-Item -ItemType Directory -Path "$env:USERPROFILE\Documents\github-test" -Force
Set-Location "$env:USERPROFILE\Documents\github-test"
```

### 4.2 建立测试网页

```powershell
@"
<!doctype html>
<html lang="zh-Hant">
<head>
  <meta charset="utf-8">
  <title>GitHub 连接成功</title>
</head>
<body>
  <h1>Hello！GitHub 连接成功！</h1>
</body>
</html>
"@ | Set-Content -Encoding UTF8 -Path ".\index.html"
```

### 4.3 初始化 Git 并送上 GitHub

```powershell
git init
git add .
git commit -m "建立 GitHub 连线测试页"
gh repo create github-test --public --source=. --push
```

### 4.4 开启 GitHub Pages

```powershell
gh api repos/{owner}/github-test/pages -X POST -f build_type=legacy -f source.branch=main -f source.path=/
```

如果上面指令失败，可能是 repo 还没完全建立完成。等 30 秒后再试一次，或请 Codex 帮你到 GitHub 网页检查 Pages 设定。

GitHub Pages 网址通常是：

```text
https://你的帐号.github.io/github-test/
```

请使用者打开网址确认是否看到：

```text
Hello！GitHub 连接成功！
```

> [!warning]
> GitHub Pages 第一次部署可能需要 1 到 3 分钟。看到 404 时，先等一下再重新整理，不一定是失败。

---

## 步骤五：保留或删除测试 repo

测试完成后，请 Codex 询问使用者：

```text
测试成功了，这个 github-test repo 要保留，还是删除？
```

如果要删除 GitHub 上的测试 repo：

```powershell
gh repo delete github-test --yes
```

如果要删除本机测试资料夹：

```powershell
Remove-Item -LiteralPath "$env:USERPROFILE\Documents\github-test" -Recurse -Force
```

---

## 完成后可以怎么用？

| 你说的话 | Codex 会做的事 |
|----------|----------------|
| 「帮我把这个专案推到 GitHub」 | 检查 diff、commit、push |
| 「帮我建立 GitHub repo」 | 建 repo、设定 remote、推送 |
| 「帮我把这个网页上线」 | 推送 repo、设定 GitHub Pages、回报网址 |
| 「帮我同步 GitHub」 | 检查目前 repo 状态，需要时 commit / push |
| 「收工」 | 若已设定收工 skill，会一起检查 Obsidian、AGENTS.md、GitHub |

---

## Codex 环境下的注意事项

### 1. Codex 可能需要授权才能读写 GitHub CLI 设定

在 Codex Desktop app 里，有时直接执行 `gh --version` 或 `gh auth status` 会遇到类似：

```text
failed to read configuration: open C:\Users\<你>\AppData\Roaming\GitHub CLI\config.yml: Access is denied.
```

这通常不是 GitHub CLI 坏掉，而是 Codex 需要你允许它读取 GitHub CLI 的设定位置。

处理方式：

1. 让 Codex 重新执行检查，并同意授权。
2. 授权后再跑：

```powershell
gh auth status
```

3. 看到登入帐号与 `repo` scope 后再继续。

### 2. Codex App 连接器与 GitHub CLI 是两件事

Codex 里可能有 GitHub connector / app，也可能有本机 `gh` CLI。

| 类型 | 用途 |
|------|------|
| GitHub connector | 让 Codex 在对话中查 repo、issue、PR 等 GitHub 资料 |
| GitHub CLI (`gh`) | 让 Codex 在你的电脑上建立 repo、commit、push、开 Pages |

如果只是要让 Codex 在本机专案里帮你 commit / push，`gh` CLI 是最直接的路线。

如果要在对话中直接读 GitHub 内容、查 PR、看 issue，则要设定 Codex 的 GitHub connector。完整新手流程建议两个都设定。

### 3. 不要把 token 写进 AGENTS.md

AGENTS.md 可以记录：

```md
## GitHub

GitHub 帐号：your-github-name
预设 repo：private
需要发布网页时使用 GitHub Pages
```

不要记录：

```md
GitHub token: ghp_xxxxx
```

token、密码、一次性验证码都不要写进 repo 或 Obsidian 对外笔记。

---

## 踩坑笔记

| 状况 | 原因 | 解法 |
|------|------|------|
| `gh --version` 显示 Access is denied | Codex 被挡在 GitHub CLI 设定档外 | 允许 Codex 读取后重跑 |
| `gh auth status` 没有登入 | 尚未完成 GitHub 授权 | 执行 `gh auth login --web --git-protocol https` |
| Codex connector 显示 `accounts: []` | Codex Desktop 尚未连接 GitHub App | 到 Codex 设定里重新 Connect GitHub |
| connector 看得到帐号但找不到 repo | GitHub App 没有授权该 repo | 到 GitHub App installation 设定补授权 repo |
| 浏览器没有自动开 | 装置登入页没被自动唤起 | 手动开 `https://github.com/login/device` |
| push 被拒绝 | 权限不足或不是正确帐号 | 重跑 `gh auth status`，确认帐号与 `repo` scope |
| GitHub Pages 404 | Pages 尚未部署完成 | 等 1 到 3 分钟再重整 |
| Codex 可以读 repo 但不能 push | connector 可读不等于本机 git 有权限 | 检查 `gh auth status` 与 git remote |

---

## 更新纪录

| 日期 | 版本 | 更新内容 |
|------|------|----------|
| 2026-04-26 | v0.1 | Codex 初版 |
| 2026-04-27 | v0.2 | 改成 Codex Desktop 主线，补上网页端登入、PowerShell 指令、实测踩坑 |
| 2026-04-27 | v0.3 | 补上 Codex Desktop GitHub connector 的登入、授权与验证流程 |
