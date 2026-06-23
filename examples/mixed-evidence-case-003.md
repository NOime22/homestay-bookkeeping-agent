# Example: mixed evidence (screenshot + text)

## Input evidence summary
A user sends a Feishu message with both:

1. A screenshot of a 拼多多 order — wall art, 54.9 元, paid via 支付宝, 2026-02-06.
2. Accompanying text:

```
示例路A弄N号客房挂画 关联订单 BK20260206003 备注：客户特别要求
```

## Expected classification rule
- **screenshot wins for type inference** → `record_type = operations`
- the text supplements only specific fields

## Expected field merge

| Field | Source | Value |
|-------|--------|-------|
| `record_type` | screenshot | `operations` |
| `摘要` | screenshot (concise) | `抽象壁画` |
| `金额` | screenshot | `54.9` |
| `账务日期` | screenshot | `2026-02-06` |
| `交易平台` | screenshot | `拼多多` |
| `支付方式` | screenshot | `支付宝` |
| `收支方向` | inferred | `支出` |
| `数量` | default | `1` |
| `房源` | text | `示例路A弄N号` |
| `订单摘要` | text (BK 前缀识别) | `BK20260206003` |
| `备注` | text (only the user's remark portion) | `客户特别要求` |
| `证据文本` | text | original full text |

## Hard rules
- `订单摘要` / `订单ID` 字段**仅当**文本明确包含 BK 订单号时才填，不要硬塞
- `备注` **仅来自消息文本**，不要把截图里的商品描述塞进备注
- 截图里的商品标题应被压缩进 `摘要`（≤10 字），不进备注
- `结算状态` 不要从截图猜，必须文本明确说"已结算 / 待结算"才填
