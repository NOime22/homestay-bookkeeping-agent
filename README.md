# Homestay Bookkeeping Agent · 民宿记账 Agent

> 把民宿日常记账从 **"每天对着一堆截图手动填表"** 变成 **"在飞书群里发一张图，几秒后点确认即可入账"** 的开源 Agent 规范包。
>
> An open-source agent spec that turns homestay bookkeeping from *"manually filling tables from screenshots every day"* into *"drop a screenshot in your Feishu group, click confirm in seconds."*

[中文](#中文) · [English](#english)

---

## 中文

### 🎬 它是什么

一句话：**一个住在飞书群里的"记账小秘书" Agent**。

你做民宿运营时每天会遇到这些消息：

- 拼多多买了壁画，截图是订单页 → 要记成「运营支出」
- 客户付了房费，OTA 后台发来订单号 → 要记成「订单收入」
- 群里随手发了句"昨天交了电费 380"→ 要记成「运营支出·公用事业」

**以前**：你打开多维表格 → 点击新建一行 → 一个个字段填 → 上传截图 → 保存。每条 1~3 分钟，一天几十条，腰酸背痛。

**现在**：你只需要把那张截图或那句话扔进飞书群。几秒后，Agent 自动发回一张"确认卡片"，所有字段都帮你填好了——你扫一眼，必要时用下拉框微调，按"确认记账"，写入多维表格，完事。

```
┌─────────────────────────────────────────────────┐
│  你在飞书群: [发一张拼多多订单截图]                │
└─────────────────────┬───────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────┐
│  Agent 几秒后回复一张"确认卡片":                  │
│  ─────────────────────────                       │
│  💼 运营类支出 · 待确认                           │
│  摘要：抽象壁画                                   │
│  金额：54.9 元                                    │
│  日期：2026-02-06                                 │
│  房源：[下拉: 示例路A弄N号 ▼]                     │
│  科目：[下拉: 耗材/客房装饰 ▼]                    │
│  平台：[下拉: 拼多多 ▼]                           │
│  支付：[下拉: 支付宝 ▼]                           │
│  数量：[下拉: 1 ▼]                                │
│  ─────────────────────────                       │
│  [ 确认记账 ]    [ 有文字修改 ]                   │
└─────────────────────┬───────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────┐
│  你点 [确认记账]                                  │
└─────────────────────┬───────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────┐
│  Agent: ✅ 已记账，写入「07_运营收支001」表第 N 行 │
│  （证据图也已上传云盘并附在记录里）                │
└─────────────────────────────────────────────────┘
```

**这就是它的全部。** 没有 App 要装、没有网页要登录——所有操作就在你日常用的飞书群里完成。

---

### 🛠 它依赖哪些工具和资源

这个 Agent 不是凭空跑起来的，它是几样东西"拼"出来的。下面这张表告诉你**谁干什么**：

| 角色 | 用的什么 | 在这个 Agent 里干嘛 | 必需？ |
|------|---------|---------------------|--------|
| **入口 / 出口** | 飞书群聊 | 用户发证据、Agent 回卡片，全部在群里 | ✅ 必需 |
| **交互层** | 飞书消息卡片（CardKit）| 渲染"确认卡"、下拉框、按钮、文字修改回路 | ✅ 必需 |
| **存储层** | 飞书多维表格（Base）| 账本台账——订单台账、运营收支台账、字典表 | ✅ 必需 |
| **证据存档** | 飞书云盘（Drive）| 原始截图上传 → 拿到链接 → 附在表格行里 | ✅ 必需 |
| **大脑** | 一个支持 **原生看图** 的 LLM（GPT-4o、Claude Sonnet、Gemini 等）| 看截图、理解文字、产出结构化字段 | ✅ 必需 |
| **Agent 宿主** | 任何能调飞书 OpenAPI + 跑 LLM 的运行时<br>（Hermes / LangChain / Dify / Coze / 自研 Bot）| 串起以上四样的"胶水" | ✅ 必需（任选一种）|
| **飞书 API 调用工具** | `lark-cli` 或飞书官方 SDK 或直接 HTTP | 上传云盘、读写多维表格、发送卡片、订阅事件 | ✅ 必需（任选）|
| **本仓库** | 你正在看的这份 spec | 提供规则、提示词、卡片契约、字典、踩坑笔记 | ✅ 必需（这是核心）|

#### 关键工具说明

- **飞书 / Lark**：字节出品的协作平台。中国版叫"飞书"，国际版叫"Lark"。本 Agent 的所有交互都在它的群聊、消息卡片、多维表格、云盘这四个产品上完成。
- **飞书消息卡片（CardKit）**：飞书的卡片消息系统，支持下拉、按钮、表单、回调。本 Agent 的"确认卡"就是用这个搭的。**它有不少不公开的坑**，详见 [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md)。
- **飞书多维表格（Base）**：飞书的"加强版 Excel"——支持关联表、视图、公式、API 增删改查。本 Agent 把所有账目和字典都存在这里。建表规范见 [`docs/feishu-base-setup.md`](docs/feishu-base-setup.md)。
- **`lark-cli`**：一个开源的飞书命令行工具，封装了 OpenAPI。本 Agent 用它上传云盘、写表格。当然你也可以用官方 Python/Go SDK 或直接发 HTTP——本仓库 host-neutral，没绑死。
- **LLM**：必须支持**原生看图**（不是 OCR + 文本拼接）。截图里的金额、平台、商品名要靠 LLM 直接"看"出来。详见 [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md) 第 7.2 节。
- **Agent 宿主**：作者自己用的是 **Hermes Agent**（[hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com/docs)），但本仓库**不绑死任何宿主**——LangChain / Dify / Coze / Cursor agent / 自研 Python bot 都能拿来对接。适配指南见 [`adapters/`](adapters/)。

---

### 🔍 完整工作流（详细版）

下面是一条真实账务从"发消息"到"入账"的全程拆解：

```
┌──────────────────────────────────────────────────────────────────┐
│  阶段 1: 触发                                                      │
│  ────────                                                         │
│  · 用户在飞书群里发：截图 / 文本 / 截图+文本                       │
│  · 飞书把消息事件推送给 Agent 宿主（webhook 或长连接）             │
└─────────────────────────────┬────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 2: 识别 + 分类                                               │
│  ───────────────                                                  │
│  · 宿主调用 LLM，喂入：                                            │
│    - prompts/system.md（Agent 人设）                              │
│    - prompts/extraction.md（识别提示词）                          │
│    - 消息文本 + 截图（vision 通道）                                │
│  · LLM 产出结构化 candidate（金额/日期/平台/科目...）              │
│  · 自动判型：运营类 / 订单类（截图优先于文本）                     │
└─────────────────────────────┬────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 3: 规范化                                                    │
│  ────────                                                         │
│  · 默认日期 = 消息发送时间（截图无日期时）                         │
│  · 默认交易人 = 发送消息的人                                       │
│  · 词典归一化：「闪购」→「淘宝」、「便利店」→「线下」              │
│  · 房源匹配：地标别名 → 房源；唯一匹配自动填，多个匹配留空让用户选 │
└─────────────────────────────┬────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 4: 渲染确认卡（核心交互）                                    │
│  ──────────────────────                                          │
│  · 调飞书 OpenAPI（lark-cli 或 SDK）发送 interactive card         │
│  · 用预定义模板卡（不要即时拼 JSON 卡——见 LESSONS_LEARNED 2.x）   │
│  · 卡片显示：5 个文本字段 + 8 个下拉框 + 2 个按钮                 │
└──────────┬──────────────────────────────────────────────┬────────┘
           ▼                                              ▼
┌────────────────────────────┐         ┌────────────────────────────┐
│  分支 A: 用户改下拉框        │         │  分支 B: 用户点"有文字修改"  │
│  ────────────────           │         │  ─────────────────         │
│  · 飞书把回调推给宿主         │         │  · 宿主发一条极简文字模板    │
│  · 宿主更新 draft 内存状态    │         │    （金额：/日期：/...）   │
│  · 重新渲染卡片 + 返回 toast  │         │  · 用户回复一行或多行       │
│    （这两样必须同时返回，     │         │  · 宿主按"字段：值"解析      │
│    否则下拉转圈不停！）       │         │  · 合并 draft，刷新真卡片    │
└──────────┬──────────────────┘         └───────────────┬────────────┘
           │                                            │
           └────────────────┬───────────────────────────┘
                            ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 5: 用户点「确认记账」                                        │
│  ────────────────                                                │
│  · 飞书回调到宿主                                                  │
│  · 宿主开始写账：                                                  │
│    1) 调飞书 Drive API：上传原始截图 → 拿到 file_token             │
│    2) 构造证据图链接：https://<base_host>/file/<token>             │
│    3) 调飞书 Base API：upsert 一行进对应账本表                    │
│       - 运营类 → 07_运营收支001                                   │
│       - 订单类 → 06_订单台账001                                   │
│    4) 把证据图链接、原始消息ID、交易人都写入对应字段              │
└─────────────────────────────┬────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 6: 回执                                                      │
│  ────                                                             │
│  · 把确认卡片刷新成"✅ 已记账"状态                                  │
│  · 显示写入表格名 + 关键字段，方便日后追溯                         │
└──────────────────────────────────────────────────────────────────┘
```

> **完整状态机图** 和 **端到端数据流图** 见 [`docs/runtime-architecture.md`](docs/runtime-architecture.md)

---

### 🚀 它用起来有多方便

跟"自己手填多维表格"对比：

| 场景 | 传统手填 | 用本 Agent |
|------|---------|-----------|
| 拼多多采购截图 | 打开表格 → 新建行 → 抄数字 → 选下拉 → 上传图 → 保存 | 截图丢群 → 点确认 |
| OTA 订单提醒 | 抄订单号 → 算晚数 → 选房源 → 选渠道 → 填金额 | 转发 OTA 文本到群 → 点确认 |
| 跨房源公共采购 | 想"这笔算哪个房源"想半天 | 直接发，房源留空，卡片下拉里选"公共" |
| 一条信息记错了类 | 删行重做 | 点"有文字修改"回一句话 |
| 多业主分账 | 不同业主开不同表，复制黏贴痛苦 | 每个业主独立群 → 自动落到自己的 base |
| 单次耗时 | ~90 秒 | **~5 秒** |
| 心智负担 | 高（要记字段、选下拉、记得上传图）| 低（看一眼有没有错就行）|

更省心的几个细节：

- 🧠 **截图证据优先**：图和文字有冲突时以图为准——文字写错了也不影响判型
- 🏠 **房源自动识别**：截图里出现"X新村"地名，自动指向那个房源
- 💸 **金额支持文字修正**：识别错了金额不用回去重发图，回一句"金额：50"就改了
- 📋 **下拉框来自字典表**：你想加新平台/新支付方式，直接去飞书表里加一行，Agent 下次就能识别
- 🔍 **证据可追溯**：每条记录都关联原始截图链接 + 消息 ID，三个月后翻账也能秒查
- 👥 **多团队隔离**：一套 Agent 服务多个业主，账目不互通

---

### 📚 怎么读这份仓库

按顺序看，30 分钟吃透：

| 顺序 | 文件 | 内容 |
|------|------|------|
| 1 | [`AGENT.md`](AGENT.md) | Agent 身份、职责边界 |
| 2 | [`RULES.md`](RULES.md) | 业务规则（分类、字段、约束）|
| 3 | [`WORKFLOWS.md`](WORKFLOWS.md) | 端到端运行时行为 |
| 4 | [`CARD_CONTRACTS.md`](CARD_CONTRACTS.md) | 飞书卡片布局/按钮/回调契约 |
| 5 | [`DICTIONARY_SPECS.md`](DICTIONARY_SPECS.md) | 字典与映射 |
| 6 | ⭐ [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md) | **飞书卡片血泪经验，最值钱** |
| 7 | [`docs/feishu-base-setup.md`](docs/feishu-base-setup.md) | 多维表格 8 张表建表规范 |
| 8 | [`docs/runtime-architecture.md`](docs/runtime-architecture.md) | 数据流图 + 状态机 |
| 9 | [`prompts/`](prompts/) | 4 份提示词 |
| 10 | [`schemas/`](schemas/) | 3 份 JSON Schema |
| 11 | [`examples/`](examples/) | 5 个真实风格用例 |

---

### 🏗 怎么落地跑

最小可运行环境：

1. **飞书企业版** 或 **个人版 + 自建应用**（任意一种可用 OpenAPI 的形态）
2. **飞书多维表格 base**，按 [`docs/feishu-base-setup.md`](docs/feishu-base-setup.md) 建 8 张表
3. **一个支持 vision 的 LLM**（GPT-4o / Claude Sonnet 4 / Gemini 2.5 Pro / Qwen-VL 都行）
4. **一个 Agent 宿主**——任选：
   - Hermes Agent（作者自用）：[`adapters/hermes.md`](adapters/hermes.md)
   - 其他框架：[`adapters/feishu.md`](adapters/feishu.md) 通用建议
5. **飞书 API 调用工具**：lark-cli 或飞书官方 SDK 或自己 HTTP

最少要写多少代码？依宿主而异——Hermes 上几百行胶水，纯 Python SDK 从零写大概 1500~2000 行。

---

### 🤝 反馈和贡献

最有价值的贡献：

- 飞书新踩坑 → 加到 [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md)
- 新字典词条 → 加到 [`DICTIONARY_SPECS.md`](DICTIONARY_SPECS.md)
- 新用例 → 加到 [`examples/`](examples/)

详见 [`CONTRIBUTING.md`](CONTRIBUTING.md)。

---

### 📦 现状

- 版本 `v0.1.0` · 阶段 active development
- 重心：**运营类记账卡全流程**（订单类规则已写，UI 优化排期下一阶段）
- License: MIT
- 不含运行时代码——这是 spec-only 包

---

## English

### 🎬 What this is

In one sentence: **a "bookkeeping assistant" agent that lives in your Feishu group**.

If you run a homestay business, every day you face messages like:

- Bought a wall painting on Pinduoduo, the screenshot is an order page → record as "operations expense"
- Customer paid for a room, OTA dashboard sends an order number → record as "order revenue"
- Someone in the group casually says "paid 380 for electricity yesterday" → record as "operations · utilities"

**Before**: open the spreadsheet → create a new row → copy numbers → select dropdowns → upload screenshot → save. 1~3 minutes per entry, dozens per day, painful.

**Now**: drop that screenshot or sentence into the Feishu group. Seconds later, the agent replies with a "confirmation card" with all fields pre-filled. You glance, optionally tweak dropdowns, click "Confirm", and it lands in your spreadsheet.

### 🛠 Tool stack

| Role | What | Purpose | Required |
|------|------|---------|----------|
| **In / out channel** | Feishu group chat | Where user sends evidence and agent replies | ✅ |
| **Interaction layer** | Feishu Message Card (CardKit) | Renders confirmation cards, dropdowns, buttons | ✅ |
| **Ledger storage** | Feishu Base (multi-base spreadsheet) | Order ledger, operations ledger, dictionaries | ✅ |
| **Evidence archive** | Feishu Drive | Upload screenshots, get URL, attach to row | ✅ |
| **Brain** | A vision-capable LLM (GPT-4o, Claude Sonnet, Gemini, etc.) | See screenshots, parse text, produce candidate | ✅ |
| **Agent host** | Any framework that can call Feishu OpenAPI + run an LLM | Glues everything together | ✅ (pick one) |
| **Feishu API tool** | `lark-cli`, official SDK, or raw HTTP | Drive upload, Base read/write, card send, event subscribe | ✅ (pick one) |
| **This repo** | The spec you're reading | Rules, prompts, card contracts, dictionaries, lessons | ✅ (this is the core) |

### Why a vision-capable LLM matters

The agent **must** use native vision on screenshots — not OCR-then-text. Amounts, platforms, item names live in pixel layout, not OCR output. See [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md) §7.2.

### Full data flow

See [`docs/runtime-architecture.md`](docs/runtime-architecture.md) — 6-stage state machine, end-to-end card refresh loop, text-edit merge path, and writeback contract.

### Why it's a joy to use

| Scenario | Manual entry | With this agent |
|----------|--------------|-----------------|
| Pinduoduo purchase screenshot | Open table, new row, copy fields, upload image, save | Drop screenshot in group, click confirm |
| OTA order notification | Copy order number, calc nights, select property | Forward OTA text, click confirm |
| Wrong classification | Delete row, redo | Click "edit by text", reply one line |
| Per-entry time | ~90s | **~5s** |
| Cognitive load | High | Low |

### Status

- Version `v0.1.0` · active development
- Focus: **end-to-end operations-side card loop**
- License: MIT
- Spec-only — no runtime code bundled

### Reading order

`AGENT.md` → `RULES.md` → `WORKFLOWS.md` → `CARD_CONTRACTS.md` → `DICTIONARY_SPECS.md` → `LESSONS_LEARNED.md` → `docs/` → `prompts/` → `schemas/` → `examples/`

### Contributing

PRs welcome — Feishu lessons, dictionary entries, and example cases especially. See [`CONTRIBUTING.md`](CONTRIBUTING.md).
