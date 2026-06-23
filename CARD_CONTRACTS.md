# Card Contracts

## 1. Operations-side initial card

### Header
- title: `运营收支确认`
- template: red or other clearly attention-worthy template

### Body sections
1. intro markdown: `请确认以下信息：`
2. compact summary line (optional)
3. divider
4. text-area markdown block
5. divider
6. labeled dropdown controls
7. final button row

### Text-area fields
Display as markdown, not inline editable controls:
- 账务日期
- 摘要
- 金额
- 订单摘要
- 备注
- 交易人 (read-only display is allowed)

### Dropdown fields
Each dropdown must have a visible label immediately above it.
Current operations-side order:
1. 房源
2. 数量
3. 一级科目
4. 二级科目
5. 交易平台
6. 支付方式
7. 收支方向
8. 结算状态

`结算状态` notes:
- mixed evidence (image + text) — only fill when the same text message explicitly states it
- otherwise leave blank for user confirmation

### Buttons
- secondary: `有文字修改`
- primary: `确认记账`

## 2. Refreshed confirmation card

Current practical behavior may use a lighter refreshed card after text edits.
At minimum it must:
- reflect merged edits clearly
- preserve `有文字修改`
- preserve `确认记账`
- remain a real interactive card, not plain text pretending to be one

## 3. Dropdown option source rules

Dropdown values must come from data sources, not hallucinated values.

Current frozen source mapping:
- 房源 -> `01_房源信息.房源`
- 渠道 -> `02_渠道信息.渠道`
- 交易平台 -> `03_交易平台信息.交易平台`
- 支付方式 -> `04_支付方式信息.支付方式`
- 一级科目 / 二级科目 -> `05_分类规则`
- 数量 -> fixed list `1..10`

## 4. Callback payload contract

### select_static payload
Must carry enough information to update draft state deterministically.
Recommended keys:
- `candidate_id`
- `field`
- `source`
- `ledger`
- `option`

### button payload
Recommended keys:
- `candidate_id`
- `action`
- `source`
- `ledger`

Supported actions for current loop:
- `request_text_edits`
- `confirm_bookkeeping`
- optionally `hold`
- optionally `reject`

## 5. Text-edit template contract

The text-edit prompt should be extremely short and structured.
Only non-empty `字段：值` lines should be applied.
Blank or malformed lines should be ignored.

Current practical template:
- 金额：
- 日期：
- 订单摘要：
- 备注：

## 6. Failure contract

If card callback succeeds but final validation fails:
- keep the candidate draft
- send only one short blocking message
- do not dump raw internal payloads
