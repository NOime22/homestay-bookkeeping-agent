# Example: ambiguous property (boundary case)

## Input evidence summary
A user sends a screenshot showing:
- 停车费 100 元
- 地点："示例新村地下车库"
- 支付方式：微信
- 日期：2026-06-23

The chat is linked to a multi-property base. The base contains 3 properties tagged with "示例新村" as their `地标别名`:

- 示例路A弄N号
- 示例路A弄M号
- 示例路B弄K号

## Expected behavior

**Do NOT auto-pick one property.** Multiple matches → ambiguous.

The agent should:
1. Generate the candidate with **`房源` left blank**
2. Send the operations-side confirmation card
3. In the `房源` dropdown, show all 3 candidate properties as options
4. Let the user pick

## What to avoid

❌ Picking the first match silently
❌ Sending a follow-up question text instead of a card
❌ Forcing the user to retype the address

## What's fine

✅ Pre-fill all other fields (金额、日期、交易平台、支付方式、收支方向、二级科目=停车费)
✅ Card sent immediately with `房源` blank — user resolves on the card

## Tangent rule
If the same `地标别名` matches **exactly one** property in the current base, **auto-fill** that property — no ambiguity, no question. Only the >1 case needs user resolution.
