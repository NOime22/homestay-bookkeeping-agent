# Homestay Bookkeeping Agent · 民宿记账 Agent

> 我做了一个住在飞书群里的"记账小秘书"，把民宿日常记账从 **"每天对着一堆截图手动填表"** 变成 **"在群里发一张图，几秒后点确认即可入账"**。这是它的开源规范包。
>
> I built a "bookkeeping assistant" that lives in my Feishu group — it turns daily homestay bookkeeping from *"manually filling tables from screenshots"* into *"drop a screenshot in the group, click confirm in seconds."* This repo is the open-source spec for it.

[中文](#中文) · [English](#english)

---

## 中文

### 🎬 我为什么做这个

我自己在做民宿运营，每天要处理一堆零碎账目：

- 在拼多多买了壁画，要记成「运营支出」
- 客户付了房费，OTA 发来订单号，要记成「订单收入」
- 群里随手发一句"昨天交了电费 380"，要记成「运营支出·公用事业」

**以前**我自己怎么做：打开飞书多维表格 → 新建一行 → 一个个字段填 → 上传截图 → 保存。每条 1~3 分钟，一天几十条，腰酸背痛，还容易填错填漏。

**后来**我想，既然每天都在飞书群里和家人/合伙人沟通账目，何不让"账"自己长进表格里？于是我搭了这个 Agent。

**现在**我的日常变成这样：截图丢群 → 几秒后看到一张"确认卡片"，所有字段都填好了 → 我扫一眼，必要时改个下拉 → 点"确认记账" → 自动入账。一条账从识别到落表，**约 5 秒**。

```
┌─────────────────────────────────────────────────┐
│  我在飞书群: [发一张拼多多订单截图]                │
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
│  我点 [确认记账]                                  │
└─────────────────────┬───────────────────────────┘
                      ▼
┌─────────────────────────────────────────────────┐
│  Agent: ✅ 已记账，写入「07_运营收支001」表第 N 行 │
│  （证据图也已上传云盘并附在记录里）                │
└─────────────────────────────────────────────────┘
```

跑通之后，我决定把这套规范开源——不是因为它有多牛，而是因为**这条路上的坑我已经替你踩完了**。

---

### 📦 我开源的是什么

这个仓库是**规范 + 提示词 + 卡片契约 + 字典 + 踩坑笔记**——用 Markdown / YAML / JSON Schema 描述了我这个真实在跑的 Agent 的全部领域规则。

✅ **可以拿来直接抄**：业务规则、字段定义、卡片布局、提示词、字典、JSON Schema、案例
❌ **不是开箱即用的机器人**：这里没有 Python/TS 代码、没有部署脚本、没有可执行入口

要把它跑起来，你需要按这份规范在自己的 Agent 宿主（Hermes、LangChain、Dify、自研 Bot 等）里实现一层胶水代码。

**为什么不直接开源代码？** 因为我用的 Agent 宿主（Hermes）有它自己的特性，我的胶水代码和宿主深度耦合，对你没用。而**规则层 / 提示词层 / 卡片契约层** 是 host-neutral 的——你换什么宿主都能复用。这才是最值钱的部分。

---

### 🛠 我搭这个 Agent 用了什么

下面这张表是我实际用的工具栈。你想自己搭一套，至少需要凑齐同等角色的东西：

| 角色 | 我用的 | 在这个 Agent 里干嘛 | 必需？ |
|------|-------|---------------------|--------|
| **入口 / 出口** | 飞书群聊 | 我发证据、Agent 回卡片，全部在群里 | ✅ 必需 |
| **交互层** | 飞书消息卡片（CardKit）| 渲染"确认卡"、下拉框、按钮、文字修改回路 | ✅ 必需 |
| **存储层** | 飞书多维表格（Base）| 账本台账——订单台账、运营收支台账、字典表 | ✅ 必需 |
| **证据存档** | 飞书云盘（Drive）| 原始截图上传 → 拿到链接 → 附在表格行里 | ✅ 必需 |
| **大脑** | 一个支持 **原生看图** 的 LLM（GPT-4o / Claude Sonnet / Gemini / Qwen-VL）| 看截图、理解文字、产出结构化字段 | ✅ 必需 |
| **Agent 宿主** | 我用的是 [Hermes Agent](https://hermes-agent.nousresearch.com/docs)；你可以用任何能调飞书 OpenAPI + 跑 LLM 的运行时 | 串起以上四样的"胶水" | ✅ 必需（任选一种）|
| **飞书 API 调用工具** | `lark-cli` | 上传云盘、读写多维表格、发送卡片、订阅事件 | ✅ 必需（或飞书官方 SDK / 直接 HTTP）|
| **这个仓库** | 你正在看的 spec | 我所有的规则、提示词、卡片契约、字典、踩坑笔记 | ✅ 必需（这是核心）|

#### 关键工具补充说明

- **飞书 / Lark**：字节出品的协作平台。中国版叫"飞书"，国际版叫"Lark"。我所有交互都在它的**群聊 + 消息卡片 + 多维表格 + 云盘**这四个产品上完成，因为这是我日常已经在用的协作工具。
- **飞书消息卡片（CardKit）**：飞书的卡片消息系统，支持下拉、按钮、表单、回调。我用它做"确认卡"。**它有不少不公开的坑**，我都写在 [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md) 里了。
- **飞书多维表格（Base）**：飞书的"加强版 Excel"，支持关联表、视图、公式、API 增删改查。我把所有账目和字典都存这。建表规范见 [`docs/feishu-base-setup.md`](docs/feishu-base-setup.md)。
- **`lark-cli`**：一个封装了飞书 OpenAPI 的命令行工具，我用它上传云盘、写表格。你不一定要用这个——飞书官方 Python/Go SDK 或自己发 HTTP 都行，本仓库 host-neutral 没绑死。
- **LLM 必须支持原生看图**：不能用 "OCR + 文本拼接" 的两段式 pipeline。截图里的金额、平台、商品名要靠 LLM 直接"看"出来。原因见 [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md) 第 7.2 节——我亲自踩过这个坑。
- **关于 Hermes**：我自用是 Hermes，因为它支持多模型故障转移、原生 vision、可插拔 plugin。但**我的规范不绑 Hermes**——任何 LangChain / Dify / Coze / Cursor agent / 自研 Python bot 都能对接。适配指南我写在 [`adapters/`](adapters/) 里。

---

### 🔍 完整工作流（详细版）

下面是我这个 Agent 处理一条账务的完整链路，从"我发消息"到"账落表"全程拆解：

```
┌──────────────────────────────────────────────────────────────────┐
│  阶段 1: 触发                                                      │
│  ────────                                                         │
│  · 我在飞书群里发：截图 / 文本 / 截图+文本                          │
│  · 飞书把消息事件推送给我的 Agent 宿主（webhook 或长连接）          │
└─────────────────────────────┬────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 2: 识别 + 分类                                               │
│  ───────────────                                                  │
│  · 宿主调用 LLM，喂入：                                            │
│    - prompts/system.md（Agent 人设）                              │
│    - prompts/extraction.md（识别提示词）                          │
│    - 我的消息文本 + 截图（vision 通道，原生看图）                  │
│  · LLM 产出结构化 candidate（金额/日期/平台/科目...）              │
│  · 自动判型：运营类 / 订单类（截图证据优先于文本）                  │
└─────────────────────────────┬────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 3: 规范化                                                    │
│  ────────                                                         │
│  · 默认日期 = 我发消息的时间（截图无日期时兜底）                    │
│  · 默认交易人 = 发消息的我                                          │
│  · 词典归一化：「闪购」→「淘宝」、「便利店」→「线下」              │
│  · 房源匹配：地标别名 → 房源；唯一匹配自动填，多个匹配留空让我选    │
└─────────────────────────────┬────────────────────────────────────┘
                              ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 4: 渲染确认卡（核心交互）                                    │
│  ──────────────────────                                          │
│  · 调飞书 OpenAPI（我用 lark-cli）发送 interactive card           │
│  · 用预定义模板卡（不要即时拼 JSON 卡——见 LESSONS_LEARNED 2.x）   │
│  · 卡片显示：5 个文本字段 + 8 个下拉框 + 2 个按钮                 │
└──────────┬──────────────────────────────────────────────┬────────┘
           ▼                                              ▼
┌────────────────────────────┐         ┌────────────────────────────┐
│  分支 A: 我改下拉框          │         │  分支 B: 我点"有文字修改"   │
│  ────────────────           │         │  ─────────────────         │
│  · 飞书把回调推给宿主         │         │  · 宿主发一条极简文字模板    │
│  · 宿主更新 draft 内存状态    │         │    （金额：/日期：/...）   │
│  · 重新渲染卡片 + 返回 toast  │         │  · 我回复一行或多行          │
│    （这两样必须同时返回，     │         │  · 宿主按"字段：值"解析      │
│    否则下拉转圈不停！）       │         │  · 合并 draft，刷新真卡片    │
└──────────┬──────────────────┘         └───────────────┬────────────┘
           │                                            │
           └────────────────┬───────────────────────────┘
                            ▼
┌──────────────────────────────────────────────────────────────────┐
│  阶段 5: 我点「确认记账」                                          │
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

> 完整状态机图 + 端到端数据流图见 [`docs/runtime-architecture.md`](docs/runtime-architecture.md)

---

### 🚀 我自己用着的几个"真香"瞬间

跟"自己手填多维表格"的对比，是我做之前对之后的真实感受：

| 场景 | 我以前手填 | 我现在用 Agent |
|------|----------|--------------|
| 拼多多采购截图 | 打开表格 → 新建行 → 抄数字 → 选下拉 → 上传图 → 保存 | 截图丢群 → 点确认 |
| OTA 订单提醒 | 抄订单号 → 算晚数 → 选房源 → 选渠道 → 填金额 | 转发 OTA 文本到群 → 点确认 |
| 跨房源公共采购 | 想"这笔算哪个房源"想半天 | 直接发，房源留空，卡片下拉里选"公共" |
| 一条信息识别错类 | 删行重做 | 点"有文字修改"回一句话 |
| 多业主分账 | 不同业主开不同表，复制黏贴痛苦 | 每个业主独立群 → 自动落到自己的 base |
| 单次耗时 | ~90 秒 | **~5 秒** |
| 心智负担 | 高（要记字段、选下拉、记得传图）| 低（看一眼有没有错就行）|

更细的真香点：

- 🧠 **截图证据优先**：我可能图里写错文字描述，但 Agent 以图为准——不会被我自己手抖带偏
- 🏠 **房源自动识别**：截图里只要出现某地标，自动指向那个房源，我连地址都不用想
- 💸 **金额支持文字修正**：识别错了金额不用回去重发图，回一句"金额：50"就改了
- 📋 **下拉框来自字典表**：我想加新平台/新支付方式，直接去飞书表里加一行，Agent 下次就能识别
- 🔍 **证据可追溯**：每条记录都关联原始截图链接 + 消息 ID，三个月后翻账也能秒查
- 👥 **多团队隔离**：我一套 Agent 服务多个业主，账目不互通

---

### 👥 这个仓库适合谁看

我把这个开源出来，最希望对这几类人有用：

- **同样在做民宿/短租/多物业**的运营者，想搭一套结构化记账 Agent，可以直接抄我的字段规则、卡片布局、字典
- **在飞书内部做 LLM Agent** 的开发者，可以避开我踩过的 CardKit 坑（这是我自认为本仓库最值钱的部分）
- **研究"LLM + 真实交互卡片确认"模式**的 Agent 开发者，可以看我怎么设计文字修改回路、状态机、字段合并策略

---

### 📚 怎么读这份仓库

按这个顺序看，30 分钟能吃透：

| 顺序 | 文件 | 看完你会知道 |
|------|------|--------------|
| 1 | [`AGENT.md`](AGENT.md) | 我这个 Agent 的身份和职责边界 |
| 2 | [`RULES.md`](RULES.md) | 我的核心业务规则（分类、字段、防呆约束）|
| 3 | [`WORKFLOWS.md`](WORKFLOWS.md) | 我的端到端运行时行为 |
| 4 | [`CARD_CONTRACTS.md`](CARD_CONTRACTS.md) | 我的飞书卡片布局、按钮、回调契约 |
| 5 | [`DICTIONARY_SPECS.md`](DICTIONARY_SPECS.md) | 我用的下拉字典和数据源映射 |
| 6 | ⭐ [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md) | **我的飞书卡片血泪经验，最值钱的一份** |
| 7 | [`docs/feishu-base-setup.md`](docs/feishu-base-setup.md) | 我多维表格 8 张表怎么建 |
| 8 | [`docs/runtime-architecture.md`](docs/runtime-architecture.md) | 我的数据流图和状态机 |
| 9 | [`prompts/`](prompts/) | 我用的 4 份提示词 |
| 10 | [`schemas/`](schemas/) | 我定义的 3 份 JSON Schema |
| 11 | [`examples/`](examples/) | 5 个我跑过的真实风格用例 |

---

### 🏗 如果你想自己搭一套

最小可运行环境：

1. **飞书企业版** 或 **个人版 + 自建应用**（任意一种可用 OpenAPI 的形态）
2. **飞书多维表格 base**，按 [`docs/feishu-base-setup.md`](docs/feishu-base-setup.md) 建 8 张表
3. **一个支持 vision 的 LLM**（GPT-4o / Claude Sonnet 4 / Gemini 2.5 Pro / Qwen-VL 都行）
4. **一个 Agent 宿主** — 任选：
   - 我自用的 Hermes Agent：[`adapters/hermes.md`](adapters/hermes.md)
   - 其他框架：[`adapters/feishu.md`](adapters/feishu.md) 通用建议
5. **飞书 API 调用工具**：lark-cli / 飞书官方 SDK / 自己 HTTP

需要写多少代码？依宿主而异——我在 Hermes 上写了几百行胶水；纯 Python SDK 从零搭大概 1500~2000 行。

---

### 🤝 反馈和贡献

我最希望收到这几类反馈：

- 飞书新踩坑 → 加到 [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md)，我会非常感激
- 新字典词条 → 加到 [`DICTIONARY_SPECS.md`](DICTIONARY_SPECS.md)
- 新用例 → 加到 [`examples/`](examples/)

详见 [`CONTRIBUTING.md`](CONTRIBUTING.md)。

---

### 📦 现状

- 版本 `v0.1.0` · 阶段 active development
- 我当前的重心：**运营类记账卡全流程**（订单类规则已写好，UI 优化排期下一阶段）
- License: MIT
- 不含运行时代码——这是 spec-only 包

---

## English

### 🎬 Why I built this

I run a homestay business. Every day I deal with bookkeeping noise like:

- Bought a wall painting on Pinduoduo → log as "operations expense"
- A customer paid for a stay, OTA pings me with an order number → log as "order revenue"
- Someone in the group casually says "paid 380 for electricity yesterday" → log as "operations · utilities"

**Before**: open the spreadsheet → new row → copy fields → upload screenshot → save. 1~3 minutes each, dozens per day, error-prone.

**Now**: drop the screenshot in the Feishu group, glance at the confirmation card the agent posts, click "Confirm" — **about 5 seconds end-to-end**.

I built this for myself first. Then I figured the rules, prompts, card layout, and especially the Feishu CardKit gotchas I burned weeks discovering are worth sharing.

### 📦 What I'm open-sourcing

The **spec layer** — rules, prompts, card contracts, dictionaries, JSON Schema, lessons. Plain Markdown / YAML / JSON. Host-agnostic.

**Not** the runtime code — my glue is tightly coupled to my own agent host (Hermes), it wouldn't help you. The spec layer is where the real value is anyway.

### 🛠 The stack I'm using

| Role | What I use | Required |
|------|-----------|----------|
| In/out channel | Feishu group chat | ✅ |
| Interaction | Feishu Message Card (CardKit) | ✅ |
| Storage | Feishu Base | ✅ |
| Evidence archive | Feishu Drive | ✅ |
| Brain | A vision-capable LLM (GPT-4o, Claude Sonnet, Gemini, Qwen-VL) | ✅ |
| Agent host | [Hermes Agent](https://hermes-agent.nousresearch.com/docs) — but pick anything | ✅ |
| Feishu API tool | `lark-cli` — or official SDK / raw HTTP | ✅ |
| This spec | The repo you're reading | ✅ |

Native vision matters: don't go OCR-then-text. See [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md) §7.2.

### 🔍 End-to-end flow

6 stages: trigger → recognize → normalize → render card → (dropdown edits / text-edit branch) → confirm → writeback → receipt. Full state machine in [`docs/runtime-architecture.md`](docs/runtime-architecture.md).

### 🚀 Why I love using it daily

| Scenario | Manual entry | With my agent |
|----------|--------------|---------------|
| Pinduoduo purchase screenshot | Open table, new row, copy fields, upload image, save | Drop screenshot, click confirm |
| OTA order notification | Copy number, calc nights, pick property | Forward text, click confirm |
| Wrong classification | Delete row, redo | Click "edit by text", reply one line |
| Per-entry time | ~90s | **~5s** |
| Cognitive load | High | Low |

### 👥 Who I built this for

- Other homestay/short-rental operators wanting a structured agent
- Feishu LLM agent developers wanting to skip the CardKit pitfalls
- Anyone studying the "LLM + real interactive confirmation card" pattern

### 📚 Reading order

`AGENT.md` → `RULES.md` → `WORKFLOWS.md` → `CARD_CONTRACTS.md` → `DICTIONARY_SPECS.md` → `LESSONS_LEARNED.md` → `docs/` → `prompts/` → `schemas/` → `examples/`

### Status

v0.1.0 · active development · MIT · spec-only

### Contributing

PRs welcome — Feishu lessons, dictionary entries, and example cases especially. See [`CONTRIBUTING.md`](CONTRIBUTING.md).
