# Example: operations-side purchase

## Input evidence summary
A shopping-order screenshot shows:
- 拼多多 order
- wall art purchase
- price 54.9
- payment by 支付宝
- order date 2026-02-06

## Expected classification
- `record_type = operations`
- target ledger = `07_运营收支001` in the reference multi-base layout (see `docs/feishu-base-setup.md`)

## Expected candidate sketch
- 摘要: 抽象壁画
- 金额: 54.9
- 账务日期: 2026-02-06
- 收支方向: 支出
- 交易平台: 拼多多
- 支付方式: 支付宝
- 数量: 1
- 一级科目 / 二级科目: pending confirmation if needed

## Expected confirmation behavior
- send operations-side confirmation card
- user may change dropdowns
- user may click `有文字修改` for text corrections
- system merges edits before final confirm
