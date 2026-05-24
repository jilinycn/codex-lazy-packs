# Codex 懒人包 #03：连接 Obsidian 第二大脑

> 版本：v0.2（Codex 版）
> 更新日期：2026-04-26

---

## 这个懒人包会帮你做什么？

把 Codex 连到你的 Obsidian 笔记本，让 Codex 可以在不同专案中协助你：

- 找到你的 Obsidian vault 实际位置
- 读取、搜寻、整理 `.md` 笔记
- 建立或修改笔记
- 建立全域设定，让 Codex 记得「我的笔记本在哪里」
- 确认跨专案读写是否可用，并带你完成必要设定

> [!important]
> 这份懒人包分成两层：
>
> 1. **全域记忆设定**：让 Codex 知道你的 Obsidian vault 路径。
> 2. **读写权限 / MCP 设定**：让 Codex 真的能跨专案读写那个资料夹。
>
> 只写 AGENTS.md 只能解决「知道在哪里」，不一定等于「任何专案都能直接写入」。
> 跨专案读写不一定非要 MCP；如果 Codex App 已授权那个资料夹，档案系统也能直接读写。MCP 的价值是让 vault 成为稳定工具来源，并提供搜寻、读写、frontmatter、批次读取等专门能力。

---

## 先备条件

- [ ] 已安装 Codex Desktop、Codex CLI，或支援 Codex 的 IDE 外挂
- [ ] 已安装 Obsidian，或准备新建一个 Obsidian vault
- [ ] 电脑有网路连线
- [ ] 若要安装 MCP：需要 Node.js 与 npm

---

## 先做选择：你是哪一种使用者？

| 状况 | 建议流程 |
|------|----------|
| 已经有 Obsidian 笔记本 | 走「阶段一：找到现有 vault」 |
| 还没有 Obsidian 笔记本 | 走「阶段二：建立新 vault」 |
| 想在任何专案都读写笔记 | 完成「阶段四：跨专案读写设定」与「阶段五：跨专案验证」 |
| 只想让 Codex 记得路径 | 完成「阶段三：全域 AGENTS.md」即可 |

---

## 阶段一：找到现有 Obsidian vault

### 1-1. 先问使用者

请先问使用者：

> 你的 Obsidian 笔记本现在放在哪里？如果不知道，我可以帮你找。

常见位置：

| 同步方式 | 常见路径 |
|----------|----------|
| OneDrive | `C:\Users\<使用者>\OneDrive\文件\<vault名称>` |
| Google Drive | `G:\我的云端硬碟\<vault名称>` |
| iCloud | `C:\Users\<使用者>\iCloudDrive\<vault名称>` |
| 本机文件 | `C:\Users\<使用者>\Documents\<vault名称>` |
| macOS iCloud | `/Users/<使用者>/Library/Mobile Documents/iCloud~md~obsidian/Documents/<vault名称>` |
| macOS Google Drive | `/Users/<使用者>/Library/CloudStorage/GoogleDrive-<帐号>/My Drive/<vault名称>` |

### 1-2. Windows 自动搜寻候选 vault

请 Codex 在 PowerShell 执行：

```powershell
$roots = @(
  "$env:USERPROFILE\OneDrive",
  "$env:USERPROFILE\Documents",
  "$env:USERPROFILE\Desktop",
  "G:\我的云端硬碟",
  "G:\My Drive"
)

$roots |
  Where-Object { Test-Path $_ } |
  ForEach-Object {
    Get-ChildItem -Path $_ -Recurse -Directory -Force -ErrorAction SilentlyContinue |
      Where-Object { Test-Path (Join-Path $_.FullName ".obsidian") } |
      Select-Object FullName
  }
```

如果找到多个候选路径，请列出来让使用者选，不要自行猜。

### 1-3. macOS / Linux 自动搜寻候选 vault

```bash
find "$HOME" -type d -name ".obsidian" -prune 2>/dev/null | sed 's#/.obsidian$##'
```

### 1-4. 验证这真的是 Obsidian vault

一个资料夹可以视为 Obsidian vault，至少要符合：

- 里面有 `.obsidian/` 资料夹
- 里面有 `.md` 笔记，或是使用者确认这是新 vault
- 使用者能在 Obsidian 里正常开启

把确认后的完整路径记成：

```text
<VAULT_PATH>
```

---

## 阶段二：建立新 vault（没有现成笔记本才做）

如果使用者还没有 Obsidian vault：

1. 请使用者先安装 Obsidian：`https://obsidian.md`
2. 询问要放在哪个同步位置：
   - OneDrive
   - Google Drive
   - Obsidian Sync
   - 本机资料夹
3. 建立资料夹，例如：

```text
Secondbrain/
├── 每日笔记/
├── 知识库/
├── 创作库/
├── Templates/
└── Clippings/
```

4. 请使用者用 Obsidian 开启这个资料夹。
5. 记下 vault 完整路径 `<VAULT_PATH>`。

> [!note]
> 不必强制使用 Google Drive。OneDrive、Obsidian Sync、本机资料夹都可以。重点是 Codex 要知道实际路径，且要有读写权限。

---

## 阶段三：建立全域设定，让 Codex 记得 vault 路径

### 3-1. 写入全域 AGENTS.md

Codex 会读取全域与专案层级的 `AGENTS.md`。建议把 Obsidian 固定路径写进：

```text
C:\Users\<使用者>\.codex\AGENTS.md
```

macOS / Linux：

```text
~/.codex/AGENTS.md
```

建议加入：

```markdown
## Obsidian 笔记本固定路径

主要 Obsidian Vault：

`<VAULT_PATH>`

当我说「Obsidian」、「Secondbrain」、「我的笔记本」、「第二大脑」时，预设指这个资料夹。

若任务涉及笔记、教学素材、专案驾驶舱、工作流程、索引整理，请优先参考：

- `<VAULT_PATH>\AGENTS.md`
- `<VAULT_PATH>\第二大脑\专案工作流程.md`
- `<VAULT_PATH>\第二大脑\逻辑专案模型 SOP.md`

可协助读取、整理、建立、修改 `.md` 笔记；但实际写入权限以 Codex App 当次工作区授权与 MCP 设定为准。
```

### 3-2. 在 vault 根目录建立 AGENTS.md

在 `<VAULT_PATH>\AGENTS.md` 建立你的笔记本规则。

范例：

```markdown
# 我的 Obsidian 笔记本

## 关于我
- 我是国中老师
- 这个 vault 是我的教学第二大脑

## 语言偏好
- 所有回应请使用繁体中文

## 笔记规则
- 新增笔记时保留 Obsidian 双向连结
- 新增正式笔记时加 frontmatter
- 不要未经确认改写个人声音强烈的文章

## 固定路径
- 主要 vault：`<VAULT_PATH>`
```

> [!important]
> 这一步只让 Codex「知道」笔记本位置。要跨专案直接读写，还要完成下一阶段。

---

## 阶段四：跨专案读写设定

跨专案读写有两条路线：

| 路线 | 适合情境 | 优点 | 限制 |
|------|----------|------|------|
| A. Codex 工作区 / 资料夹授权 | 使用 Codex Desktop，且可把 vault 加入可写范围 | 不需要 MCP，直接改档 | 受当次工作区与 sandbox 权限限制 |
| B. MCP / mcpvault | 想让 vault 在任何专案都像工具一样可搜寻、可读写 | 跨专案稳定，搜寻与笔记操作较完整 | 需要 Node.js 与 MCP 设定 |

如果只是要跨专案「知道 vault 在哪」，阶段三已足够。
如果要跨专案「稳定搜寻、读写、整理笔记」，建议走 MCP。

### 4A. 使用 Codex 工作区 / 资料夹授权

1. 在 Codex App 开启工作区时，直接选 `<VAULT_PATH>`；或
2. 在 Codex App 的资料夹授权 / 可写范围中加入 `<VAULT_PATH>`；或
3. 让当前工作区本身就是 `<VAULT_PATH>` 的上层或同一资料夹。

测试方式：

```powershell
Test-Path "<VAULT_PATH>"
New-Item -ItemType Directory -Force -Path "<VAULT_PATH>\Codex 测试"
Set-Content -Encoding UTF8 -Path "<VAULT_PATH>\Codex 测试\档案系统写入测试.md" -Value "# 档案系统写入测试"
```

若成功，代表不靠 MCP 也能直接写入。

### 4B-1. 安装 Node.js

先检查：

```powershell
node --version
npm.cmd --version
```

如果没有 Node.js，Windows 可用：

```powershell
winget install --id OpenJS.NodeJS
```

安装后重开终端机，再检查一次。

### 4B-2. 安装 mcpvault

```powershell
npm.cmd install -g @bitbonsai/mcpvault
```

确认 mcpvault 路径：

```powershell
where.exe mcpvault
```

常见结果：

```text
C:\Users\<使用者>\AppData\Roaming\npm\mcpvault.cmd
```

### 4B-3. 注册 MCP 到 Codex

Codex 的 MCP 设定通常在：

```text
C:\Users\<使用者>\.codex\config.toml
```

macOS / Linux：

```text
~/.codex/config.toml
```

加入：

```toml
[mcp_servers.obsidian]
command = "C:\\Users\\<使用者>\\AppData\\Roaming\\npm\\mcpvault.cmd"
args = ["<VAULT_PATH>"]
```

Windows 范例：

```toml
[mcp_servers.obsidian]
command = "C:\\Users\\mathr\\AppData\\Roaming\\npm\\mcpvault.cmd"
args = ["C:\\Users\\mathr\\OneDrive\\文件\\Secondbrain"]
```

macOS / Linux 范例：

```toml
[mcp_servers.obsidian]
command = "mcpvault"
args = ["/Users/<使用者>/Library/CloudStorage/GoogleDrive-xxx/My Drive/Secondbrain"]
```

> [!warning]
> Windows 的 TOML 路径要用 `\\`，不能只写单一反斜线。
> Section 名称要是 `[mcp_servers.obsidian]`。

### 4B-4. 先手动测 MCP server

Windows PowerShell：

```powershell
'{"jsonrpc":"2.0","id":1,"method":"tools/list"}' |
  & "C:\Users\<使用者>\AppData\Roaming\npm\mcpvault.cmd" "<VAULT_PATH>"
```

如果成功，会看到 `read_note`、`write_note`、`search_notes`、`get_vault_stats` 等工具。

### 4B-5. 重启 Codex

- Codex Desktop：完全关闭后重开
- Codex CLI：结束后重新进入
- IDE：Reload Window 或重开 IDE

---

## 阶段五：跨专案读写验证

### 5-1. 在 vault 工作区内测试

请先把 Codex 工作区开在 `<VAULT_PATH>`，对 Codex 说：

> 请列出这个 Obsidian vault 根目录的资料夹。

接著说：

> 请在 `Codex 测试/` 建立一篇 `连线测试.md`，内容写「Codex 可以写入 Obsidian」。

如果成功，代表目前工作区有直接写入权限。

### 5-2. 在其他专案测试

把 Codex 工作区切到另一个专案资料夹，对 Codex 说：

> 请读取我的 Obsidian 笔记本根目录，确认能不能看到资料夹。

再说：

> 请在我的 Obsidian 笔记本建立 `Codex 测试/跨专案测试.md`，写入目前日期与所在专案名称。

判断结果：

| 结果 | 意义 |
|------|------|
| 成功读写 | MCP 或工作区授权已可跨专案操作 |
| 能读不能写 | 需要检查 MCP 权限、Codex 工作区可写范围，或用 Obsidian vault 作为工作区 |
| 找不到 vault | 全域 AGENTS.md 未生效，或 MCP 没成功连线 |
| 要求授权 | 依 Codex App 提示授权该资料夹 |

> [!important]
> 跨专案修改有两种路径：
>
> - **透过档案系统直接修改**：不需要 MCP，但会受 Codex App 当次工作区与可写资料夹限制。
> - **透过 MCP 修改**：需要安装与设定 MCP，但跨专案比较稳定，也有搜寻与笔记工具。
>
> 所以最稳的做法是：全域 AGENTS.md 记路径 + 至少一种跨专案读写路线 + 实测跨专案建立测试笔记。

---

## 阶段六：完成后可以怎么用

| 你说的话 | Codex 会做的事 |
|----------|----------------|
| 「搜寻我的 Obsidian 里有没有 XXX」 | 到 vault 搜寻相关笔记 |
| 「帮我新增一篇今天教学反思」 | 建立新的 `.md` 笔记 |
| 「整理这篇笔记成 YouTube 脚本」 | 读取笔记并改写成脚本 |
| 「这个专案上次做到哪？」 | 读取对应的专案驾驶舱 |
| 「把这个专案的进度写回第二大脑」 | 更新 Obsidian 内的工作流程笔记 |

---

## 如果失败，照这个顺序检查

1. `<VAULT_PATH>` 是否正确
2. `<VAULT_PATH>` 里是否有 `.obsidian/`
3. `C:\Users\<使用者>\.codex\AGENTS.md` 是否有写入固定路径
4. `C:\Users\<使用者>\.codex\config.toml` 是否有 `[mcp_servers.obsidian]`
5. `mcpvault.cmd` 路径是否正确
6. Codex 是否已完全重启
7. 是否在其他专案中实测过读写
8. 若是直接档案写入失败，确认 Codex App 是否授权该资料夹

---

## 实测纪录与踩坑笔记

### 2026-04-26 三师爸 Secondbrain 实测

实测环境：

| 项目 | 结果 |
|------|------|
| Vault | `C:\Users\mathr\OneDrive\文件\Secondbrain` |
| Node.js | `v24.14.0` |
| npm / npx | `11.9.0` |
| mcpvault | `@bitbonsai/mcpvault@0.11.0` |
| Codex 设定档 | `C:\Users\mathr\.codex\config.toml` |
| MCP 设定名称 | `obsidian` |
| 测试笔记 | `Codex-test/MCP-test.md` |

最后采用的 Codex MCP 设定：

```toml
[mcp_servers.obsidian]
command = "C:\\Users\\mathr\\AppData\\Roaming\\npm\\mcpvault.cmd"
args = ["C:\\Users\\mathr\\OneDrive\\文件\\Secondbrain"]
startup_timeout_sec = 20
tool_timeout_sec = 60
```

已完成验证：

- `tools/list` 成功回传 mcpvault 工具清单。
- `list_directory` 成功列出 vault 根目录。
- `write_note` 成功建立 `Codex-test/MCP-test.md`。
- `read_note` 成功读回测试笔记与 frontmatter。
- 重新启动 Codex 后，`mcp__obsidian__` 工具正式载入。
- 透过 MCP 成功建立 `Codex-test/MCP-loaded-test.md`，确认重启后可直接写入 vault。

### 踩坑 1：PowerShell 直接跑 `npm` / `npx` 被执行原则挡住

症状：

```text
因为这个系统上已停用指令码执行，所以无法载入 C:\Program Files\nodejs\npm.ps1
```

原因：

PowerShell 会优先叫到 `npm.ps1` / `npx.ps1`，但系统执行原则不允许 `.ps1`。

解法：

```powershell
npm.cmd --version
npx.cmd --version
npm.cmd install -g @bitbonsai/mcpvault
```

不要急著改 Windows Execution Policy；先用 `.cmd` 通常就能解。

### 踩坑 2：`npx` 在 sandbox 内写 npm cache 失败

症状：

```text
npm error code EPERM
npm error syscall mkdir
npm error path C:\Users\<使用者>\AppData\Local\npm-cache\_cacache\tmp
```

原因：

`npx` 需要下载与快取套件，会写到使用者 npm cache。若 Codex 当下 sandbox 没有该资料夹写入权限，就会失败。

解法：

1. 测试时授权 Codex 执行该命令；或
2. 改成先全域安装：

```powershell
npm.cmd install -g @bitbonsai/mcpvault
```

然后在 `config.toml` 使用完整路径：

```toml
command = "C:\\Users\\<使用者>\\AppData\\Roaming\\npm\\mcpvault.cmd"
```

### 踩坑 3：中文路径经 PowerShell 管线送 JSON 时变成 `??`

症状：

```text
Failed to write file: Codex ??/MCP??.md
```

原因：

这次是手动用 PowerShell 管线送 JSON 给 MCP server，中文 JSON 内容在管线编码中被破坏。

解法：

- 手动命令列测试时，先用 ASCII 路径确认 MCP 写入能力，例如 `Codex-test/MCP-test.md`。
- 正式在 Codex MCP 工具中操作时，通常不需要手动经过 PowerShell JSON 管线。

### 踩坑 4：全域 npm 安装成功，但 sandbox 内找不到 `mcpvault`

症状：

```text
where.exe mcpvault
INFO: Could not find files for the given pattern(s).
```

原因：

`mcpvault.cmd` 安装在使用者全域 npm 目录：

```text
C:\Users\<使用者>\AppData\Roaming\npm\mcpvault.cmd
```

但该目录不一定在目前 shell / sandbox 的 PATH 里。

解法：

在 `config.toml` 直接写完整路径，不依赖 PATH。

### 踩坑 5：PowerShell 管线后执行字串路径要用 `&`

错误写法：

```powershell
'{"jsonrpc":"2.0","id":1,"method":"tools/list"}' | "C:\Users\<使用者>\AppData\Roaming\npm\mcpvault.cmd" "<VAULT_PATH>"
```

正确写法：

```powershell
'{"jsonrpc":"2.0","id":1,"method":"tools/list"}' |
  & "C:\Users\<使用者>\AppData\Roaming\npm\mcpvault.cmd" "<VAULT_PATH>"
```

### 踩坑 6：改完 `config.toml` 后，当前 Codex 对话不会立刻出现新 MCP 工具

原因：

Codex 通常在启动时读取 MCP 设定。已开启的对话不一定会即时载入新 server。

解法：

- 完全关闭并重开 Codex Desktop。
- 或在 Codex CLI / IDE 重新启动 session。
- 重启后再检查 MCP 工具是否出现。

### 踩坑 7：WindowsApps 里的 `codex.exe` 可能无法从 sandbox shell 执行

症状：

```text
Program 'codex.exe' failed to run: Access is denied
```

影响：

这代表本次无法用 `codex mcp list` 在 shell 中验证 Codex 是否读到 MCP 设定。

替代验证：

1. 直接用 `mcpvault.cmd` 测 `tools/list`。
2. 重启 Codex 后，在对话中测试是否能列出 Obsidian vault。

## 常见问题

| 问题 | 解法 |
|------|------|
| 我不知道 vault 在哪 | 用阶段一的搜寻指令找 `.obsidian/` |
| 我有多个 vault | 列出候选路径，请使用者选主要 vault |
| 我用 OneDrive 可以吗 | 可以，路径正确即可 |
| 我用 Google Drive 可以吗 | 可以，路径正确即可 |
| 我用 Obsidian Sync 可以吗 | 可以，MCP 看的是本机资料夹 |
| 全域 AGENTS.md 写了还不能改 | AGENTS.md 只提供记忆与规则，不保证档案权限 |
| 跨专案要怎么稳定修改 | 建议使用 MCP，并完成跨专案测试 |
| Windows TOML 路径一直失败 | 确认用 `\\`，例如 `C:\\Users\\...\\Secondbrain` |

---

## 本懒人包不做的事

- 不强迫使用 Google Drive
- 不强迫新建 vault
- 不假设每个人路径都一样
- 不宣称 MCP 是唯一跨专案路线
- 不承诺只靠 AGENTS.md 就能跨专案写档
- 不在未确认前改写使用者既有笔记

---

## 相关连结

- [mcpvault GitHub](https://github.com/bitbonsai/mcpvault)
- [Obsidian 官网](https://obsidian.md)
- [Codex MCP 官方文件](https://developers.openai.com/codex/mcp)
