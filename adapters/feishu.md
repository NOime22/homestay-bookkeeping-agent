# Feishu Adapter Notes

## Purpose

Describe how the independent agent package maps into Feishu-specific interaction behavior.

## Confirmed constraints

- `select_static` must be placed inside an `action` block to render
- `form`-wrapped `select_static` may send but does not render reliably
- `input` is not reliable for this workflow
- `date_picker` is not reliable for this workflow
- callback responses must follow Feishu's card-action schema

## Recommended runtime behavior

### Initial card
The host should send one real interactive card.

### Dropdown callback
- store draft change only
- do not write ledger rows
- do not trigger full downstream processing

### Text-edit request
- send one short text template
- record `text_edit_prompt_message_id`
- set `awaiting_text_reply=true`

### Text-edit reply binding
Preferred path:
- use `reply_to_message_id` / parent linkage

Fallback path:
- if Feishu reply metadata is missing and there is exactly one pending draft for the same chat + actor, bind to that draft

### Refresh behavior
After text-edit merge, the Feishu adapter should directly send a refreshed interactive confirmation card, rather than relying on a generic agent text-reply path.
