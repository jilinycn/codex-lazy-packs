# Codex 懒人包 #01：连接 Google NotebookLM

> 版本：v0.1（Codex 版）
> 更新日期：2026-04-26

> 📌 **本懒人包可独立执行**：会自动检查并安装所需工具。

---

## 这个懒人包会帮你做什么？

让 Codex CLI 能直接操控 NotebookLM：建 notebook、上传资料来源、产生简报/资讯图表/音讯/影片/心智图/测验等，成品自动下载到本机资料夹。

---

## 原理

```
Codex CLI ←(MCP 协定)→ nlm（翻译官）←(Google 登入)→ NotebookLM
```

`nlm` 是 stdio MCP server，Codex 透过 MCP 协定呼叫它，它再去模拟浏览器操作 NotebookLM。

> 💡 **跟 Claude Code 版的差别**：MCP server 本体（`notebooklm-mcp-cli`）一模一样，只是注册到 Codex 的方式不同（Codex 用 `~/.codex/config.toml`，Claude Code 用 `~/.claude/settings.json`）。

---

## 先备条件

- [ ] Codex CLI 已安装
- [ ] Google 帐号
- [ ] 电脑有网路连线

---

## 请 Codex 帮我执行以下步骤

### 步骤零：环境检查

1. 作业系统
2. `git --version`，没有就装
3. `uv --version`，没有则步骤一安装
4. 网路连线
5. `codex --version`

---

### 步骤一：安装 uv（如果没装）

**Windows（PowerShell）**：
```bash
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**macOS / Linux**：
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

### 步骤二：安装 NotebookLM MCP CLI

```bash
uv tool install notebooklm-mcp-cli
```

确认：
```bash
nlm --version
```

> `nlm: command not found` → 重开终端机；仍失败代表 uv 工具路径没进 PATH，告诉使用者把 `uv tool dir --bin` 印出的路径加进 PATH。

---

### 步骤三：登入 Google 帐号

```bash
nlm login
```

> 🖐️ 浏览器会开 Google 登入页，登入后 CLI 自动撷取认证。

确认：
```bash
nlm doctor
```

---

### 步骤四：把 NotebookLM 注册为 Codex 的 MCP server

> ✅ 三种 Codex 共用 `~/.codex/config.toml`，做一次三边都吃到。任选一条路：

**方法 A：Codex Desktop GUI（最推荐）**

1. 开 Codex Desktop → 设定 → **Integrations & MCP**
2. 点 **Add server**（或类似的「新增」按钮）
3. 填：
   - Name：`notebooklm`
   - Command：`nlm`
   - Args：`mcp`
4. 储存

**方法 B：手动编辑 `~/.codex/config.toml`**

> Desktop：设定 → Integrations & MCP → 「Open config.toml」也能直接打开这档；IDE：齿轮 → MCP settings → Open config.toml；用记事本/编辑器开亦可。

```toml
[mcp_servers.notebooklm]
command = "nlm"
args = ["mcp"]
```

> 💡 路径：Windows `C:\Users\<你>\.codex\config.toml`，macOS/Linux `~/.codex/config.toml`。档案不存在就新建。
>
> ⚠️ **Section 名称必须是 `mcp_servers`**（底线、复数）。写成 `mcp-servers` 或 `mcpservers` Codex 会静默忽略。

**方法 C：CLI（只给有装 CLI 的人）**

```bash
codex mcp add notebooklm -- nlm mcp
```

---

### 步骤五：建立本地资料夹

在 Documents 下建：
```
Documents/
  └── NotebookLM/
      ├── slides/
      ├── infographics/
      ├── audio/
      ├── video/
      ├── docs/
      ├── sheets/
      ├── mindmaps/
      └── quizzes/
```

---

### 步骤六：重启 Codex 并验证

> 🖐️ **Desktop**：完全结束 app（不只是关视窗），重新开启。
> **IDE 扩充**：Reload Window（VSCode：`Cmd/Ctrl+Shift+P` → Reload Window）。
> **CLI**：`exit` 后重新 `codex`。

验证：
1. 对 Codex 说「列出我的 NotebookLM 笔记本清单」
2. 能成功列出（即使空的）→ 连接成功
3. Desktop 用户可在 Integrations & MCP 设定面板看 `notebooklm` 显示为 ✅ 连线中

---

### 步骤七：功能测试

1. 建一个叫「测试笔记本」的 notebook
2. 确认建立成功
3. 删除它
4. ✅ 「全部完成！Codex 已连接 NotebookLM。」

---

## 如果失败

对 Codex 说：「NotebookLM 懒人包执行失败，清除设定重跑。」

复原：
- **Desktop**：设定 → Integrations & MCP → 找到 `notebooklm` → 删除/停用
- **手动**：编辑 `~/.codex/config.toml` 移除 `[mcp_servers.notebooklm]` 段
- **CLI**：`codex mcp remove notebooklm`

清掉 nlm 本体：
```bash
uv tool uninstall notebooklm-mcp-cli
nlm logout 2>/dev/null
```

---

## 常见问题

| 问题 | 解法 |
|------|------|
| `nlm: command not found` | 重开终端机；把 `uv tool dir --bin` 路径加进 PATH |
| 登入后 `nlm doctor` 显示未认证 | 重跑 `nlm login` |
| Codex 看不到 NotebookLM 工具 | Desktop：设定 → Integrations & MCP 看 `notebooklm` 是否启用 / 连线；CLI：`codex mcp list` |
| `codex mcp` 指令找不到 | 你没装 CLI，用 Desktop GUI 或手动编辑 config.toml |
| 设定改了没生效 | section 名称要 `mcp_servers`（底线、复数），写错会被静默忽略 |
| Windows 上指令格式错误 | 用 PowerShell 或 Git Bash，别用 CMD |

---

## 相关连结

- [notebooklm-mcp-cli GitHub](https://github.com/jacob-bd/notebooklm-mcp-cli)
- [Codex MCP 官方文件](https://developers.openai.com/codex/mcp)
