# Homestay Bookkeeping Agent / 民宿记账 Agent

> A portable **spec package** for a Feishu-native bookkeeping agent built for multi-property homestay operations.
>
> 一个面向飞书的多房源民宿记账 Agent 的可移植**规范包**。

[English](#english) · [中文](#中文)

---

## 中文

### 这是什么 / 不是什么

✅ **这是一份"规范 + 提示词 + 卡片契约 + 字典 + 踩坑笔记"的可移植包。** 用 Markdown / YAML / JSON Schema 描述了一个真实在跑的民宿记账 Agent 的全部领域规则。

❌ **这不是一个"克隆即用"的飞书机器人。** 这里没有 Python/TS 实现、没有可执行入口、没有部署脚本。要把它跑起来，你需要按这份规范在自己的 Agent 宿主（Hermes、LangChain、Dify、自研 Bot 等）里实现一层适配。

### 为什么发出来

1. **飞书卡片 + LLM 工作流踩过的坑值得分享**——`select_static` 必须在 action 块里、`form_value` 恒 `null`、`initial_option` 报 230099、CardKit 数据形态等都是踩出来的。详见 [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md)。
2. **中文记账分类规则**——民宿场景的"运营/订单"二元分类、字段约束、字典词条等，可直接复用或参考。
3. **Host-neutral 设计**——这份规范不绑死某个 Agent 框架，任何宿主都能用。

### 适合谁看

- 用飞书做内部工作流，想接入 LLM 做识别 + 卡片确认的人
- 经营民宿 / 短租 / 多物业，想搭一套结构化记账 Agent 的人
- 研究"LLM + 真实交互卡片确认"模式的 Agent 开发者

### 怎么读这份仓库

按这个顺序看，30 分钟能吃透全貌：

| 顺序 | 文件 | 看完你会知道 |
|------|------|--------------|
| 1 | [`AGENT.md`](AGENT.md) | Agent 的身份、职责边界 |
| 2 | [`RULES.md`](RULES.md) | 核心业务规则（分类、字段、防呆约束）|
| 3 | [`WORKFLOWS.md`](WORKFLOWS.md) | 端到端运行时行为、状态机 |
| 4 | [`CARD_CONTRACTS.md`](CARD_CONTRACTS.md) | 飞书卡片布局、按钮、回调契约 |
| 5 | [`DICTIONARY_SPECS.md`](DICTIONARY_SPECS.md) | 下拉字典与数据源映射 |
| 6 | [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md) | **飞书卡片血泪经验，最有价值的一份** |
| 7 | [`docs/feishu-base-setup.md`](docs/feishu-base-setup.md) | 飞书多维表格建表字段规范 |
| 8 | [`docs/runtime-architecture.md`](docs/runtime-architecture.md) | 运行时架构与数据流 |
| 9 | [`prompts/`](prompts/) | 可移植的 system / extraction / confirmation prompt |
| 10 | [`schemas/`](schemas/) | candidate 与回调 payload 的 JSON Schema |
| 11 | [`examples/`](examples/) | 真实风格的案例集 |

### 一句话工作流

```
飞书消息（截图 / 文本 / 截图+文本）
   ↓
LLM 原生看图 + 文本理解，产出结构化 candidate
   ↓
发送飞书"确认卡"，下拉可改、可点"有文字修改"
   ↓
用户回复文字模板，合并修改
   ↓
用户点"确认记账"，写入飞书多维表格
   ↓
回执 ✅
```

### 现状

- 版本：`v0.1.0`
- 阶段：active development
- 当前发布重心：**运营类记账卡全流程**（订单类规则已写好，UI 优化次之）
- License：MIT

### 贡献 / 反馈

欢迎 PR / issue。尤其欢迎：
- 飞书卡片新踩坑反馈（往 `LESSONS_LEARNED.md` 加）
- 新字典词条（往 `DICTIONARY_SPECS.md` 加）
- 新案例（往 `examples/` 加）

详见 [`CONTRIBUTING.md`](CONTRIBUTING.md)。

---

## English

### What this is / isn't

✅ **A portable spec package**: rules, prompts, card contracts, dictionaries, and hard-won Feishu lessons that describe a real homestay bookkeeping agent. Plain Markdown / YAML / JSON Schema, host-agnostic.

❌ **Not a clone-and-run bot**: no runtime code, no entrypoint. You implement the host adapter; this package gives you the spec.

### Why it exists

1. **Feishu card + LLM gotchas** worth sharing (see [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md)).
2. **Chinese bookkeeping classification rules** for homestay/short-rental operations.
3. **Host-neutral design** — drop into any agent framework.

### Reading order

`AGENT.md` → `RULES.md` → `WORKFLOWS.md` → `CARD_CONTRACTS.md` → `DICTIONARY_SPECS.md` → `LESSONS_LEARNED.md` → `docs/` → `prompts/` → `schemas/` → `examples/`.

### Status

- Version: `v0.1.0`
- Phase: active development
- Current focus: **operations-side card loop** end-to-end
- License: MIT

### Contributing

PRs welcome — Feishu lesson additions, new dictionary entries, and new example cases are especially appreciated. See [`CONTRIBUTING.md`](CONTRIBUTING.md).
