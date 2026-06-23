# Text-edit reply example

When the user clicks `有文字修改`, the host may send:

金额：
日期：
订单摘要：
备注：

Example user reply:

金额：66
日期：2028-02-08
备注：test

Merge rule:
- apply only non-empty lines
- ignore blank lines
- ignore malformed lines
