# Dictionary Specifications

## 1. Frozen-first dictionaries

### 交易平台
- 支付宝
- 淘宝
- 京东
- 拼多多
- 微信
- 途家
- 美团
- Booking
- Agoda
- 线下
- 叮咚买菜
- 天猫超市
- 闲鱼

Definition:
The business-facing platform where the transaction happened.

### 支付方式
- 支付宝
- 微信
- 现金
- 银行卡
- PayPal
- Wise
- 途家
- 美团
- Booking
- Agoda

Definition:
The final actual payment/receipt route that should remain in the ledger.

### 渠道
- 途家
- 美团
- Booking
- Agoda
- 线下直销

Definition:
The booking / revenue acquisition channel.
Mandatory for order-side records.
Usually blank for operations-side records.

## 2. Fixed non-table enumeration

### 数量
- 1
- 2
- 3
- 4
- 5
- 6
- 7
- 8
- 9
- 10

Current rule:
- mandatory confirmation field for operations-side cards
- dropdown-backed
- not currently sourced from a Feishu dictionary table

## 3. Source table mapping (reference multi-base layout)

- 房源 -> `01_房源信息.房源`
- 渠道 -> `02_渠道信息.渠道`
- 交易平台 -> `03_交易平台信息.交易平台`
- 支付方式 -> `04_支付方式信息.支付方式`
- 一级科目 / 二级科目 -> `05_分类规则`

## 4. Expansion policy

When a real case produces a legitimate new option:
1. do not hallucinate
2. confirm with the user if needed
3. record the new option as a candidate
4. let operators decide whether to add it to the dictionary source
