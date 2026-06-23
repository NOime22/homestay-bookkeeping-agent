# Example: order-side lodging revenue

## Input evidence summary
A Feishu chat message with no screenshot, plain text:

```
途家 BK20260615001 示例路A弄N号 一晚 入住 2026-06-15 离店 2026-06-16 房费 400 已收
```

## Expected classification
- `record_type = order`
- target ledger = `06_订单台账001`

## Expected candidate sketch
- 订单ID: `BK20260615001`
- 订单名称: `示例路A弄N号一晚`
- 账务日期: `2026-06-15`
- 入住日期: `2026-06-15`
- 离店日期: `2026-06-16`
- 间夜: `1`
- 金额: `400`
- 房源: `示例路A弄N号`
- 渠道: `途家`
- 结算状态: `已结算`

## Expected confirmation behavior
- send order-side confirmation card
- user may correct any field via `有文字修改`
- do **not** ask the user to classify type — image-free text was already enough
- do **not** add an `订单摘要` or `订单ID` link in the order ledger row (those are operations-side only)
