# Tushare Pro API Reference - Global Markets, Futures, Options, Bonds & FX

## 港股数据

### hk_basic - 港股基本信息

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| list_status | str | N | 上市状态: L上市/D退市/P暂停上市 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| name | 股票名称 |
| fullname | 公司全称 |
| enname | 英文名称 |
| cn_spell | 拼音缩写 |
| market | 市场类别 |
| list_status | 上市状态 |
| list_date | 上市日期 |
| delist_date | 退市日期 |
| trade_unit | 交易单位 |
| isin | ISIN代码 |
| curr_type | 货币类型 |

### hk_daily - 港股日线行情

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| trade_date | 交易日期 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| pre_close | 昨收价 |
| change | 涨跌额 |
| pct_chg | 涨跌幅% |
| vol | 成交量 |
| amount | 成交额 |

### hk_income - 港股利润表 (5000积分)

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | TS代码 |
| period | str | N | 报告期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |
| report_type | str | N | 报告类型 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| report_type | 报告类型 |
| total_revenue | 营业总收入 |
| revenue | 营业收入 |
| oper_cost | 营业成本 |
| sell_exp | 销售费用 |
| admin_exp | 管理费用 |
| fin_exp | 财务费用 |
| operate_profit | 营业利润 |
| total_profit | 利润总额 |
| income_tax | 所得税 |
| n_income | 净利润 |
| n_income_attr_p | 归属母公司净利润 |

### hk_balancesheet - 港股资产负债表 (5000积分)

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | TS代码 |
| period | str | N | 报告期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |
| report_type | str | N | 报告类型 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| total_assets | 总资产 |
| total_liab | 总负债 |
| total_hldr_eqy_exc_min_int | 股东权益(不含少数股东) |
| money_cap | 货币资金 |
| accounts_receiv | 应收账款 |
| inventories | 存货 |
| fix_assets | 固定资产 |
| goodwill | 商誉 |
| lt_borr | 长期借款 |
| st_borr | 短期借款 |

### hk_cashflow - 港股现金流量表 (5000积分)

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | TS代码 |
| period | str | N | 报告期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |
| report_type | str | N | 报告类型 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| n_cashflow_act | 经营活动现金流净额 |
| n_cashflow_inv_act | 投资活动现金流净额 |
| n_cash_flows_fnc_act | 筹资活动现金流净额 |

### hk_fina_indicator - 港股财务指标 (5000积分)

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | TS代码 |
| period | str | N | 报告期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| eps | 每股收益 |
| bps | 每股净资产 |
| roe | 净资产收益率% |
| roa | 总资产收益率% |
| grossprofit_margin | 毛利率% |
| netprofit_margin | 净利率% |
| debt_to_assets | 资产负债率% |

---

## 美股数据

### us_basic - 美股基本信息

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| classify | str | N | 交易所: NYSE/NASDAQ/AMEX |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| name | 股票名称 |
| enname | 英文名称 |
| classify | 交易所分类 |
| list_date | 上市日期 |
| delist_date | 退市日期 |

### us_daily - 美股日线行情

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| trade_date | 交易日期 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| pre_close | 昨收价 |
| change | 涨跌额 |
| pct_chg | 涨跌幅% |
| vol | 成交量 |
| amount | 成交额 |

### us_income / us_balancesheet / us_cashflow / us_fina_indicator

与港股对应接口结构一致, ts_code 为必选参数, period 为可选参数。输出字段参考 hk_income / hk_balancesheet / hk_cashflow / hk_fina_indicator。

---

## 期货数据

### fut_basic - 期货合约信息

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| exchange | str | Y | 交易所: CFFEX/SHFE/CZCE/DCE/INE |
| fut_type | str | N | 合约类型: 1普通合约/2主力与连续 |
| fut_code | str | N | 合约品种代码 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| symbol | 合约代码 |
| exchange | 交易所 |
| name | 合约名称 |
| fut_code | 品种代码 |
| multiplier | 合约乘数 |
| trade_unit | 交易计量单位 |
| per_unit | 交易单位 |
| quote_unit | 报价单位 |
| d_mode | 交割方式 |
| list_date | 上市日期 |
| delist_date | 退市日期 |
| d_month | 交割月份 |
| last_ddate | 最后交割日 |
| trade_time_desc | 交易时间说明 |

### fut_daily - 期货日线行情

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| trade_date | str | N | 交易日期 |
| exchange | str | N | 交易所 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| trade_date | 交易日期 |
| pre_close | 昨收价 |
| pre_settle | 昨结算价 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| settle | 结算价 |
| change1 | 涨跌1 (收盘价-昨结算价) |
| change2 | 涨跌2 (收盘价-昨收盘价) |
| vol | 成交量 (手) |
| amount | 成交金额 (万元) |
| oi | 持仓量 (手) |
| oi_chg | 持仓变化 |

### fut_holding - 每日成交持仓排名

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| trade_date | str | Y | 交易日期 |
| symbol | str | N | 品种代码 (如IF) |
| exchange | str | N | 交易所 |

| 输出字段 | 说明 |
|----------|------|
| trade_date | 交易日期 |
| symbol | 品种代码 |
| broker | 期货公司 |
| vol | 成交量 |
| vol_chg | 成交量变化 |
| long_hld | 持买仓量 |
| long_chg | 持买仓变化 |
| short_hld | 持卖仓量 |
| short_chg | 持卖仓变化 |

### fut_mapping - 主力/连续合约映射

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | TS代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| trade_date | 交易日期 |
| mapping_ts_code | 对应合约代码 |

---

## 期权数据

### opt_basic - 期权合约信息

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| exchange | str | Y | 交易所: SSE/SZSE/CFFEX/SHFE/CZCE/DCE |
| call_put | str | N | 期权类型: C认购/P认沽 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| exchange | 交易所 |
| name | 合约名称 |
| per_unit | 合约单位 |
| opt_code | 期权代码 |
| opt_type | 期权类型 (认购/认沽) |
| call_put | C认购/P认沽 |
| exercise_type | 行权方式: A美式/E欧式 |
| exercise_price | 行权价 |
| s_month | 结算月 |
| maturity_date | 到期日 |
| list_price | 挂牌基准价 |
| list_date | 上市日期 |
| delist_date | 退市日期 |

### opt_daily - 期权日线行情

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| trade_date | str | N | 交易日期 |
| exchange | str | N | 交易所 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| trade_date | 交易日期 |
| exchange | 交易所 |
| pre_settle | 昨结算价 |
| pre_close | 昨收价 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| settle | 结算价 |
| vol | 成交量 |
| amount | 成交额 |
| oi | 持仓量 |

---

## 债券数据

### cb_basic - 可转债基本信息

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| list_date | str | N | 上市日期 |
| exchange | str | N | 交易所: SSE/SZSE |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| bond_full_name | 债券全称 |
| bond_short_name | 债券简称 |
| cb_code | 转债代码 |
| stk_code | 正股代码 |
| stk_short_name | 正股简称 |
| maturity | 期限 (年) |
| par | 面值 |
| issue_price | 发行价格 |
| issue_size | 发行总额 (亿元) |
| remain_size | 债券余额 (亿元) |
| value_date | 起息日期 |
| maturity_date | 到期日期 |
| rate_type | 利率类型 |
| coupon_rate | 票面利率 |
| list_date | 上市日期 |
| delist_date | 摘牌日期 |
| conv_start_date | 转股起始日 |
| conv_end_date | 转股截止日 |
| conv_price | 初始转股价 |

### cb_daily - 可转债行情

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| trade_date | 交易日期 |
| pre_close | 昨收价 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| change | 涨跌额 |
| pct_chg | 涨跌幅% |
| vol | 成交量 |
| amount | 成交额 |

### yc_cb - 国债收益率曲线

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| curve_type | str | Y | 曲线类型: 0国债/1央票/7固定利率金融债 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| trade_date | 交易日期 |
| curve_type | 曲线类型 |
| curve_term | 期限 (1M/3M/6M/1Y/3Y/5Y/10Y/30Y) |
| yield | 收益率% |

---

## 外汇数据

### fx_obasic - 外汇基本信息

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| exchange | str | N | 交易所 |
| classify | str | N | 分类 |
| ts_code | str | N | TS代码 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| name | 名称 |
| classify | 分类 |
| exchange | 交易所 |
| min_unit | 最小交易单位 |
| max_unit | 最大交易单位 |
| pip | 点 |
| pip_cost | 点值 |
| traget_spread | 目标点差 |
| min_stop_distance | 最小止损距离 |
| trading_hours | 交易时间 |
| break_time | 休市时间 |

### fx_daily - 外汇日线行情

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |
| exchange | str | N | 交易所 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| trade_date | 交易日期 |
| bid_open | 买入开盘价 |
| bid_close | 买入收盘价 |
| bid_high | 买入最高价 |
| bid_low | 买入最低价 |
| ask_open | 卖出开盘价 |
| ask_close | 卖出收盘价 |
| ask_high | 卖出最高价 |
| ask_low | 卖出最低价 |
| tick_qty | 成交笔数 |

---

## 大模型语料

### news - 新闻快讯

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| start_date | str | N | 开始日期 (YYYY-MM-DD HH:MM:SS) |
| end_date | str | N | 结束日期 |
| src | str | N | 来源 |

| 输出字段 | 说明 |
|----------|------|
| datetime | 发布时间 |
| content | 内容 |
| title | 标题 |
| channels | 频道 |
| src | 来源 |

### major_news - 长篇资讯

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |
| src | str | N | 来源 |
| fields | str | N | 指定字段 |

| 输出字段 | 说明 |
|----------|------|
| title | 标题 |
| content | 内容 |
| pub_time | 发布时间 |
| src | 来源 |

### anns - 上市公司公告

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| ann_date | str | N | 公告日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| ann_type | 公告类型 |
| title | 标题 |
| content | 内容 |
| url | 链接 |

### cctv_news - 新闻联播

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| date | str | N | 日期 |

| 输出字段 | 说明 |
|----------|------|
| date | 日期 |
| title | 标题 |
| content | 内容 |
