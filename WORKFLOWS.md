# Workflows

## 1. Initial candidate workflow

1. receive screenshot or text
2. determine whether it is bookkeeping
3. classify as order-side or operations-side
4. extract normalized candidate fields
5. determine if a blocking question is necessary
6. if not blocked, send a confirmation card

## 2. Operations-side confirmation loop

### Stage A — initial confirmation card
Send a real interactive card containing:
- text area for `账务日期 / 摘要 / 金额 / 订单摘要 / 备注`
- dropdown area for `房源 / 数量 / 一级科目 / 二级科目 / 交易平台 / 支付方式 / 收支方向 / 结算状态`
- buttons: `有文字修改` and `确认记账`

### Stage B — dropdown edits
When the user changes a dropdown:
- update stored draft state only
- merge into `edited_fields`
- do not write ledger rows yet
- do not spam a full downstream workflow

### Stage C — text-edit request
When the user clicks `有文字修改`:
- send one ultra-short template
- mark the draft as awaiting text reply
- store prompt message id when available

Suggested template:
- 金额：
- 日期：
- 订单摘要：
- 备注：

### Stage D — text-edit merge
When the user replies to the template:
- first try binding by `reply_to_message_id`
- fallback to the single pending draft in the same chat + same actor
- parse only non-empty `字段：值` lines
- merge text edits into stored `edited_fields`
- clear awaiting-text-reply state

### Stage E — refreshed confirmation card
After text-edit merge:
- directly send a refreshed interactive confirmation card
- do not rely on a generic text-response path
- show the merged snapshot clearly enough for final confirmation

### Stage F — final confirmation
When the user clicks `确认记账`:
- merge original candidate + dropdown edits + text edits
- validate required fields
- if valid, write to target table
- send one short success message
- if invalid, send one short blocking recovery message

## 3. State model

Recommended first-release states:
- `candidate_ready`
- `card_sent`
- `card_edited`
- `awaiting_text_reply`
- `confirmed`
- `written`
- `held`
- `rejected`

## 4. Required draft metadata

At minimum store:
- candidate_id
- chat_id
- actor_id / sender_id
- source message id
- target table / ledger
- edited_fields snapshot
- text_edit_prompt_message_id
- awaiting_text_reply
- updated_at

## 5. Success replies

Keep success replies short.
Examples:
- `已记录到 07_运营收支001，金额 237.00，房源：示例路A弄N号。`
- `已记录到 06_订单台账001，订单名称：示例路A弄N号一晚，金额 400.00。`
