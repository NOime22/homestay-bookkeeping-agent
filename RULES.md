# Core Rules

## 1. Top-level routing

Every bookkeeping input must be classified into one of:
- order-side bookkeeping
- operations-side bookkeeping
- not bookkeeping / insufficient evidence

### 1.1 Order-side bookkeeping

Use for lodging order revenue records.
Typical signals:
- 房费
- 一晚 / 两晚 / N晚
- 订金 / 尾款 / 佣金 / 退款调整
- OTA channels such as 途家 / 美团 / Booking / Agoda
- line items clearly describing a room-night order

### 1.2 Operations-side bookkeeping

Use for operating expenses or non-order operating income.
Typical signals:
- 保洁 / 家政
- 水费 / 电费 / 宽带
- 停车费 / 用车
- 洗发水 / 纸巾 / 矿泉水 / 茶包咖啡
- 维修 / 工具 / 软装 / 电商采购

### 1.3 Not bookkeeping / insufficient evidence

Use when:
- image failed to load
- evidence contains no usable bookkeeping info
- content is unrelated to bookkeeping

Do not fabricate a ledger candidate.

## 2. Low-friction extraction rules

- The user does not need to state the record type; infer it.
- Ask follow-up questions only for the smallest blocking gap.
- Default `交易人` to the original sender unless corrected.
- If accounting date is missing, default to message date first and expose it for confirmation.
- Never fabricate missing facts.

## 3. Summary naming rules

### Operations-side
Use short human-style `摘要`.
Keep only the core object or service.
Examples:
- 落地晾衣架
- 京东家政
- 停车费
- 洗发水
- 电费

### Order-side
Do not use `摘要`.
Use `订单名称`.
Preferred pattern: `房源 + 晚数`.
Example: `示例路A弄N号一晚`

## 4. Quantity rules

### Operations-side
- `数量` is a mandatory confirmation field.
- It belongs to the dropdown area.
- Current fixed option set: `1~10`.
- It does not currently come from a Feishu dictionary table.

### Order-side
- Do not use `数量`.
- Use `间夜` instead.

## 5. Order-link rules

Only operations-side records may use:
- `订单摘要`
- `订单ID`

If no order context is provided:
- leave them blank
- do not force a follow-up question

## 6. Operations-side preferred fields

Primary confirmation fields:
- 账务日期
- 摘要
- 金额
- 房源
- 数量
- 一级科目
- 二级科目
- 交易平台
- 支付方式
- 收支方向
- 结算状态
- 备注
- 订单摘要

System-recognized but not primary confirmation fields:
- 运营记录ID
- 房源ID
- 订单ID
- 证据文本
- 证据附件
- 原始消息ID
- 交易人
- 账务状态
- 添加日期 / 创建人 / 修改日期 / 修改人

## 7. Current operations-card frozen structure

Text area:
- 账务日期
- 摘要
- 金额
- 订单摘要
- 备注

Dropdown area:
- 房源
- 数量
- 一级科目
- 二级科目
- 交易平台
- 支付方式
- 收支方向
- 结算状态

## 8. Feishu interaction constraints

Empirically confirmed:
- `select_static` must be inside an `action` block to render
- `form`-wrapped `select_static` may send but does not render reliably
- `input` is not a reliable option for this workflow
- `date_picker` is not a reliable option for this workflow
- card callback responses must follow Feishu's card-action schema

## 9. Text-edit principle

For non-inline-editable fields such as `金额` and `账务日期`:
- show them in markdown text area
- instruct the user to correct them via text reply
- use one short structured text-edit template
- merge only non-empty `字段：值` lines

## 10. Anti-goals

- Do not ask the user to classify the record type.
- Do not send pseudo-cards when the host cannot really support the interaction loop.
- Do not dump raw OCR output to the user.
- Do not overfill remarks.
- Do not force order links for operations-side records.
