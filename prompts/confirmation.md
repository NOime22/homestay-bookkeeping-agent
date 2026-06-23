# Confirmation Prompt

If there are no critical blocking gaps:
- send a confirmation card
- let the user adjust dropdown-backed fields
- let the user request text edits through `有文字修改`
- merge all edits before final confirmation

Current operations-side confirmation set:
- text area: `账务日期 / 摘要 / 金额 / 订单摘要 / 备注`
- dropdown area: `房源 / 数量 / 一级科目 / 二级科目 / 交易平台 / 支付方式 / 收支方向 / 结算状态`
- buttons: `有文字修改 / 确认记账`

Do not write to the ledger until explicit confirmation.
