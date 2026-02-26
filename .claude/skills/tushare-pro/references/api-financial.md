# Tushare Pro API Reference: Financial Statements & Indicators

APIs for financial reports (income, balance sheet, cash flow), key indicators, forecasts, and dividends.

---

## income - 利润表

获取上市公司利润表数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | **Y** | 股票代码，如 600519.SH |
| ann_date | str | N | 公告日期 YYYYMMDD |
| f_ann_date | str | N | 实际公告日期 |
| start_date | str | N | 公告开始日期 |
| end_date | str | N | 公告结束日期 |
| period | str | N | 报告期 YYYYMMDD（如 20241231 为年报） |
| report_type | str | N | 报告类型：1 合并报表 / 2 单季合并 / 3 调整单季合并 / 4 调整合并报表 |
| comp_type | str | N | 公司类型：1 一般工商业 / 2 银行 / 3 保险 / 4 券商 |

### Output Fields (Key)

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| f_ann_date | 实际公告日期 |
| end_date | 报告期 |
| report_type | 报告类型 |
| comp_type | 公司类型 |
| basic_eps | 基本每股收益 |
| diluted_eps | 稀释每股收益 |
| total_revenue | 营业总收入 |
| revenue | 营业收入 |
| int_income | 利息收入（银行适用） |
| prem_earned | 已赚保费（保险适用） |
| comm_income | 手续费及佣金收入（券商适用） |
| oper_cost | 营业成本 |
| sell_exp | 销售费用 |
| admin_exp | 管理费用 |
| fin_exp | 财务费用 |
| rd_exp | 研发费用 |
| operate_profit | 营业利润 |
| non_oper_income | 营业外收入 |
| non_oper_exp | 营业外支出 |
| total_profit | 利润总额 |
| income_tax | 所得税费用 |
| n_income | 净利润 |
| n_income_attr_p | 归属母公司股东的净利润 |
| ebit | 息税前利润 |
| ebitda | 息税折旧摊销前利润 |

### Example

```python
# 获取贵州茅台最近几期利润表（合并报表）
df = pro.income(ts_code='600519.SH', report_type='1',
                fields='ts_code,end_date,revenue,operate_profit,n_income_attr_p,basic_eps')

# 获取指定报告期
df = pro.income(ts_code='600519.SH', period='20241231', report_type='1')
```

---

## balancesheet - 资产负债表

获取上市公司资产负债表数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | **Y** | 股票代码 |
| ann_date | str | N | 公告日期 YYYYMMDD |
| f_ann_date | str | N | 实际公告日期 |
| start_date | str | N | 公告开始日期 |
| end_date | str | N | 公告结束日期 |
| period | str | N | 报告期 YYYYMMDD |
| report_type | str | N | 报告类型：1 合并报表 / 2 单季合并 / 3 调整单季 / 4 调整合并 |
| comp_type | str | N | 公司类型：1 一般 / 2 银行 / 3 保险 / 4 券商 |

### Output Fields (Key)

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| report_type | 报告类型 |
| total_assets | 总资产 |
| total_liab | 总负债 |
| total_hldr_eqy_exc_min_int | 股东权益合计（不含少数股东权益） |
| money_cap | 货币资金 |
| notes_receiv | 应收票据 |
| accounts_receiv | 应收账款 |
| inventories | 存货 |
| fix_assets | 固定资产 |
| intan_assets | 无形资产 |
| goodwill | 商誉 |
| lt_borr | 长期借款 |
| st_borr | 短期借款 |
| notes_payable | 应付票据 |
| accounts_payable | 应付账款 |

### Example

```python
# 获取资产负债表核心科目
df = pro.balancesheet(ts_code='000858.SZ', period='20241231', report_type='1',
                      fields='ts_code,end_date,total_assets,total_liab,'
                             'total_hldr_eqy_exc_min_int,money_cap,inventories,goodwill')

# 获取多期数据进行趋势分析
df = pro.balancesheet(ts_code='000858.SZ', report_type='1',
                      fields='ts_code,end_date,total_assets,total_liab')
```

---

## cashflow - 现金流量表

获取上市公司现金流量表数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | **Y** | 股票代码 |
| ann_date | str | N | 公告日期 YYYYMMDD |
| f_ann_date | str | N | 实际公告日期 |
| start_date | str | N | 公告开始日期 |
| end_date | str | N | 公告结束日期 |
| period | str | N | 报告期 YYYYMMDD |
| report_type | str | N | 报告类型：1 合并报表 / 2 单季合并 / 3 调整单季 / 4 调整合并 |
| comp_type | str | N | 公司类型：1 一般 / 2 银行 / 3 保险 / 4 券商 |

### Output Fields (Key)

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| report_type | 报告类型 |
| n_cashflow_act | 经营活动产生的现金流量净额 |
| n_cashflow_inv_act | 投资活动产生的现金流量净额 |
| n_cash_flows_fnc_act | 筹资活动产生的现金流量净额 |
| c_fr_sale_sg | 销售商品、提供劳务收到的现金 |
| c_paid_goods_s | 购买商品、接受劳务支付的现金 |
| c_paid_to_for_empl | 支付给职工以及为职工支付的现金 |
| c_pay_acq_const_finan_a | 购建固定资产、无形资产和其他长期资产支付的现金 |
| c_recp_disp_finan_a | 处置固定资产、无形资产和其他长期资产收回的现金净额 |
| free_cashflow | 企业自由现金流量 |

### Example

```python
# 获取现金流量表关键项
df = pro.cashflow(ts_code='600519.SH', period='20241231', report_type='1',
                  fields='ts_code,end_date,n_cashflow_act,n_cashflow_inv_act,'
                         'n_cash_flows_fnc_act,free_cashflow')

# 多期现金流趋势
df = pro.cashflow(ts_code='600519.SH', report_type='1',
                  fields='ts_code,end_date,n_cashflow_act,free_cashflow')
```

---

## fina_indicator - 财务指标数据

获取上市公司主要财务指标，包含盈利、偿债、运营等综合指标。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | **Y** | 股票代码 |
| ann_date | str | N | 公告日期 YYYYMMDD |
| start_date | str | N | 公告开始日期 |
| end_date | str | N | 公告结束日期 |
| period | str | N | 报告期 YYYYMMDD |

### Output Fields (Key)

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| **盈利能力** | |
| eps | 基本每股收益 |
| dt_eps | 稀释每股收益 |
| bps | 每股净资产 |
| roe | 净资产收益率（%） |
| roe_waa | 加权平均净资产收益率（%） |
| roa | 总资产报酬率（%） |
| grossprofit_margin | 销售毛利率（%） |
| netprofit_margin | 销售净利率（%） |
| profit_to_gr | 净利润/营业总收入（%） |
| **偿债能力** | |
| debt_to_assets | 资产负债率（%） |
| current_ratio | 流动比率 |
| quick_ratio | 速动比率 |
| **现金流** | |
| ocf_to_profit | 经营现金流/营业利润（%） |
| **成长能力** | |
| op_yoy | 营业利润同比增长率（%） |
| **单季度指标** | |
| q_roe | 单季度 ROE（%） |
| q_sales_yoy | 单季度营收同比增长率（%） |
| q_profit_yoy | 单季度净利润同比增长率（%） |

### Example

```python
# 获取核心财务指标
df = pro.fina_indicator(ts_code='600519.SH',
                        fields='ts_code,end_date,roe,grossprofit_margin,'
                               'netprofit_margin,debt_to_assets,current_ratio')

# 获取指定报告期的指标
df = pro.fina_indicator(ts_code='600519.SH', period='20241231')

# 获取单季度增长指标
df = pro.fina_indicator(ts_code='000858.SZ',
                        fields='ts_code,end_date,q_sales_yoy,q_profit_yoy,q_roe')
```

---

## forecast - 业绩预告

获取上市公司业绩预告数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| ann_date | str | N | 公告日期 YYYYMMDD |
| start_date | str | N | 公告开始日期 |
| end_date | str | N | 公告结束日期 |
| period | str | N | 报告期 YYYYMMDD |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| type | 业绩变动类型：预增 / 预减 / 扭亏 / 首亏 / 续亏 / 续盈 / 略增 / 略减 |
| p_change_min | 预告净利润变动幅度下限（%） |
| p_change_max | 预告净利润变动幅度上限（%） |
| net_profit_min | 预告净利润下限（万元） |
| net_profit_max | 预告净利润上限（万元） |
| summary | 业绩预告摘要 |

### Example

```python
# 获取某季度所有业绩预告
df = pro.forecast(period='20241231',
                  fields='ts_code,ann_date,end_date,type,p_change_min,p_change_max,'
                         'net_profit_min,net_profit_max')

# 查看预增类型公司
df = df[df['type'] == '预增']

# 获取单只股票历史业绩预告
df = pro.forecast(ts_code='000858.SZ')
```

---

## express - 业绩快报

获取上市公司业绩快报数据，比正式报告更早披露。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| ann_date | str | N | 公告日期 YYYYMMDD |
| start_date | str | N | 公告开始日期 |
| end_date | str | N | 公告结束日期 |
| period | str | N | 报告期 YYYYMMDD |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| revenue | 营业收入（万元） |
| operate_profit | 营业利润（万元） |
| total_profit | 利润总额（万元） |
| n_income | 净利润（万元） |
| total_assets | 总资产（万元） |
| total_hldr_eqy_exc_min_int | 净资产（万元） |
| roe | 净资产收益率（%） |
| eps | 每股收益 |
| bps | 每股净资产 |
| yoy_net_profit | 去年同期净利润增长率（%） |
| yoy_sales | 营收同比增长率（%） |

### Example

```python
# 获取某季度所有业绩快报
df = pro.express(period='20241231',
                 fields='ts_code,ann_date,end_date,revenue,n_income,roe,yoy_net_profit,yoy_sales')

# 获取单只股票业绩快报
df = pro.express(ts_code='600519.SH', period='20241231')
```

---

## dividend - 分红送股

获取上市公司分红送股数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| ann_date | str | N | 公告日期 YYYYMMDD |
| record_date | str | N | 股权登记日 YYYYMMDD |
| ex_date | str | N | 除权除息日 YYYYMMDD |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| end_date | 分红年度 |
| ann_date | 公告日期 |
| div_proc | 实施进度 |
| stk_div | 每股送转股（合计） |
| stk_bo_rate | 每股送股比例 |
| stk_co_rate | 每股转增比例 |
| cash_div | 每股分红（税前，元） |
| cash_div_tax | 每股分红（税后，元） |
| record_date | 股权登记日 |
| ex_date | 除权除息日 |
| pay_date | 派息日 |

### Example

```python
# 获取贵州茅台历史分红记录
df = pro.dividend(ts_code='600519.SH',
                  fields='ts_code,end_date,ann_date,div_proc,cash_div,stk_div,'
                         'record_date,ex_date,pay_date')

# 获取某一天除权除息的股票
df = pro.dividend(ex_date='20250515')
```

---

## fina_mainbz - 主营业务构成

获取上市公司主营业务构成，可按产品或地区拆分。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | **Y** | 股票代码 |
| period | str | N | 报告期 YYYYMMDD |
| type | str | N | 类型：P 按产品（默认）/ D 按地区 |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| end_date | 报告期 |
| bz_item | 主营业务来源（产品名或地区名） |
| bz_sales | 主营业务收入（万元） |
| bz_profit | 主营业务利润（万元） |
| bz_cost | 主营业务成本（万元） |
| curr_type | 货币类型 |
| update_flag | 是否更新 |

### Example

```python
# 按产品查看主营构成
df = pro.fina_mainbz(ts_code='600519.SH', period='20241231', type='P')

# 按地区查看营收分布
df = pro.fina_mainbz(ts_code='600519.SH', period='20241231', type='D')

# 多期产品构成对比
periods = ['20221231', '20231231', '20241231']
dfs = [pro.fina_mainbz(ts_code='600519.SH', period=p, type='P') for p in periods]
```
