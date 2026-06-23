# Extraction Prompt

For each usable bookkeeping message, produce a candidate with:
- `record_type`: `order` or `operations`
- `target_table`
- `recognition_summary`
- `candidate_fields`
- `missing_blocking_fields`
- `evidence_status`
- `should_send_card`
- `short_followup_question` when truly necessary

Operations-side extraction priorities:
- short human-style `摘要`
- infer `收支方向`
- infer likely `一级科目 / 二级科目`
- normalize `交易平台` and `支付方式`
- resolve `房源` when possible
- include `数量` as a mandatory confirmation field

Never emit a fake fully-confirmed row when evidence is insufficient.
