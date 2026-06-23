# Contributing / 贡献指南

欢迎你来贡献！这个仓库是我维护的规范包（Spec），不是运行时实现。我最想要的贡献方向：

## 高欢迎度

### 1. 飞书卡片踩坑反馈
你在飞书 CardKit / 多维表格 / OpenAPI 上踩到了我没写过的坑？欢迎补充到 [`LESSONS_LEARNED.md`](LESSONS_LEARNED.md)。格式见该文件末尾的模板。这是我最珍视的贡献类型——每一条都能帮后来者省几天。

### 2. 新字典词条
你的业务里出现了 [`DICTIONARY_SPECS.md`](DICTIONARY_SPECS.md) 没覆盖的交易平台 / 支付方式 / 渠道？提 PR 加上去，附一条说明它来自什么真实场景。

### 3. 新案例
你跑出来有趣的边界 case（识别错误、字段歧义、文字修改特殊路径），欢迎加到 [`examples/`](examples/) 下。命名格式：`<类别>-case-<编号>.md`。

### 4. 文档勘误 / 翻译
任何错别字、语病、术语不一致，直接提 PR。
英文翻译尤其欢迎——目前 README 是中英双语，其他文档主要是中文。

## 中等欢迎度

### 5. 新 Adapter 文档
为另一个 Agent 宿主（LangChain、Dify、Coze、自研 Bot）写一份 `adapters/<host>.md`，描述这个宿主下如何加载本 spec、如何映射卡片回调。

### 6. 新 Schema
如果你发现某个 candidate 字段在 schema 里漏了，提 PR 补上，附用例说明。

## 暂不接受

- ❌ **运行时实现代码（Python / TS / Go）**——这个仓库我定位为 spec-only，运行时归宿主自己。如果你想做参考实现，建议另开仓库，在你的仓库里依赖本 spec。
- ❌ **宿主特定的部署文档**——如 Docker Compose、K8s yaml、launchd plist——这些属于宿主的事。
- ❌ **大规模重构 spec 命名**——已经发布的字段语义键，稳定优先于美观。

## 提交规范

- 一次 PR 只做一件事（一个坑、一个词条、一个案例）
- Commit message 用中英文都可以，但要描述清楚改了什么
- 涉及 spec 语义的改动（如字段重命名）请在 PR 描述里说明 migration 影响
- 中文术语统一：摘要、订单摘要、运营类、订单类、有文字修改、确认记账（这些都是我已经固化下来的命名，不要替换）

## License

提交即视为同意以 MIT License 许可你的贡献。
