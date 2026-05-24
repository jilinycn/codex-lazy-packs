# Codex 懒人包 #06：设定 Gemini 免费 API

> 版本：v0.1（Codex 版）
> 更新日期：2026-04-26

> 💡 **本懒人包跟 Claude Code 版几乎一样**——Gemini API 是给你做的「网页工具」用的 AI 能力，跟用哪个 agent 开发无关。

---

## 这个懒人包会帮你做什么？

设定 Google Gemini 免费 API，让你用 Codex 做出来的工具也能有 AI 能力：
- 完全免费，不需要信用卡
- 不用装任何软体（只要 API Key）
- 比本地模型强（适合复杂推理、出题、分析）

---

## 先备条件

- [ ] Codex CLI 已安装
- [ ] Google 帐号
- [ ] 电脑有网路

---

## 请 Codex 帮我执行以下步骤

### 步骤零：环境检查

1. 作业系统 / 网路
2. 是否已有 `GEMINI_API_KEY` 环境变数（有就跳到步骤三验证）

---

### 步骤一：申请 Gemini API Key

> 🖐️ 到 https://aistudio.google.com/apikey → Google 帐号登入 → Create API Key → 选或建专案 → 复制整串 Key。
>
> ⚠️ 不需要信用卡。Key 只显示一次，记得复制。

把 Key 贴给 Codex。

---

### 步骤二：安全存放 API Key

写到环境变数（不要写进程式码）：

**Windows**：
```bash
setx GEMINI_API_KEY "[使用者的Key]"
```

**macOS / Linux**：
```bash
echo 'export GEMINI_API_KEY="[使用者的Key]"' >> ~/.bashrc
source ~/.bashrc
```

> ⚠️ 不要写在 HTML/JS 原始码（公开网页看得到），不要 push 到 GitHub（会被扫描盗用）。

---

### 步骤三：验证

```bash
curl "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=$GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"contents":[{"parts":[{"text":"请用繁体中文回答：1+1 等于多少？"}]}]}'
```

正常回 JSON → 设定成功。

---

### 步骤四：测试从网页呼叫

建简单 HTML 测试呼叫 Gemini API。

> ⚠️ 测试阶段可在前端直呼。正式工具建议透过 Supabase Edge Functions / Cloud Functions 代理避免 Key 暴露。

✅ 「Gemini 免费 API 设定完成！」

---

## 完成！这样用

对 Codex 说：「这个工具的 AI 功能用 Gemini 免费 API，从环境变数读 Key。」

| 工具功能 | Gemini 做的事 |
|---|---|
| 智慧出题 | 根据单元和难度自动出题 |
| 作文批改回馈 | 分析写作结构、给具体建议 |
| 教材差异化 | A/B/C 三种难度版本 |
| 学生回馈分析 | 整理全班回馈找共同问题 |
| 多语翻译 | 140+ 语言 |

---

## 如果失败

对 Codex 说：「Gemini API 懒人包失败，帮我检查。」

重新申请 Key：到 https://aistudio.google.com/apikey 重建。

---

## 常见问题

| 问题 | 解法 |
|---|---|
| API 回 401 | Key 无效，重申请 |
| API 回 429 | 超过免费速率，等一分钟 |
| 环境变数读不到 | Win 重启终端机；mac/Linux `source ~/.bashrc` |
| 不确定 Key 对不对 | 到 aistudio.google.com/apikey 看 |
| 担心被收费 | 免费方案不要信用卡，不会被扣 |

---

## 免费方案

| 项目 | 额度 |
|---|---|
| 费用 | $0 |
| 信用卡 | 不需 |
| 模型 | Gemini 2.5 Flash、Flash-Lite 等 |
| 速率 | 每分钟有上限（教学使用不会超） |
| 期限 | 无期限 |

---

## 相关连结

- [Google AI Studio](https://aistudio.google.com)
- [Gemini API 文件](https://ai.google.dev/docs)
- [05 安装本地 AI Ollama](05-安装本地AI-Ollama.md)
