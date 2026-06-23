# Changelog

本仓库遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/) 格式。
版本号遵循 [SemVer](https://semver.org/lang/zh-CN/)。

## [0.1.0] — 2026-06-23

首次公开发布。

### Added — 新增
- 核心规范文档：`AGENT.md` / `RULES.md` / `WORKFLOWS.md` / `CARD_CONTRACTS.md` / `DICTIONARY_SPECS.md`
- 提示词包：`prompts/system.md` / `prompts/extraction.md` / `prompts/confirmation.md` / `prompts/text-edit-merge.md`
- JSON Schema：`schemas/operations-ledger.schema.json` / `schemas/order-ledger.schema.json` / `schemas/card-action-payload.schema.json`
- 案例集：`examples/operations-case-001.md` / `examples/order-case-002.md` / `examples/mixed-evidence-case-003.md` / `examples/ambiguous-property-case-004.md` / `examples/text-edit-reply-example.md`
- 宿主适配文档：`adapters/feishu.md` / `adapters/hermes.md`
- 飞书踩坑笔记：`LESSONS_LEARNED.md`
- 多维表格建表规范：`docs/feishu-base-setup.md`
- 运行时架构说明：`docs/runtime-architecture.md`
- 贡献指南：`CONTRIBUTING.md`
- MIT License

### Frozen — 当前固化点
- 运营类确认卡布局：5 个文本字段 + 8 个下拉字段 + 2 个按钮
- 字段语义键命名（不可随意改名）
- 二元分类规则：运营类 / 订单类，无第三类
- 截图证据优先于文本判型
- 摘要 ≤10 字、备注默认留空
- 文字修改路径仅覆盖：金额、日期、订单摘要、备注

### Notes — 说明
- 这是 spec-only 包，不含运行时代码
- 订单类 UI 优化排期在运营类全流程稳定之后
