# Tushare Pro API Reference - ETF, Index & Fund Data

---

## ETF 专题

### fund_basic - ETF基本信息

> 注意：ETF 和公募基金共用 `fund_basic` 接口，通过 `market='E'` 筛选场内 ETF。

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| market | str | N | E 场内（ETF）/ O 场外 |
| status | str | N | D 摘牌 / I 发行中 / L 上市中 |
| ts_code | str | N | 基金代码 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | 基金代码 |
| name | 简称 |
| management | 管理人 |
| custodian | 托管人 |
| fund_type | 基金类型 |
| found_date | 成立日期 |
| list_date | 上市日期 |
| delist_date | 退市日期 |
| issue_amount | 发行份额 (亿份) |
| m_fee | 管理费 |
| c_fee | 托管费 |
| benchmark | 业绩比较基准 |
| status | 存续状态 |
| invest_type | 投资风格 |

```python
# 获取所有上市ETF
df = pro.fund_basic(market='E', status='L')
```

### fund_daily - ETF日线行情

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | 基金代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | 基金代码 |
| trade_date | 交易日期 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| pre_close | 昨收盘价 |
| change | 涨跌额 |
| pct_chg | 涨跌幅% |
| vol | 成交量 (手) |
| amount | 成交额 (千元) |

```python
# 获取沪深300ETF日线
df = pro.fund_daily(ts_code='510300.SH', start_date='20260101', end_date='20260226')
```

### fund_adj - ETF复权因子

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | 基金代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | 基金代码 |
| trade_date | 交易日期 |
| adj_factor | 复权因子 |

```python
df = pro.fund_adj(ts_code='510300.SH', start_date='20260101', end_date='20260226')
```

### fund_share - ETF规模数据

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | 基金代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | 基金代码 |
| trade_date | 交易日期 |
| fd_share | 基金份额 (万份) |

```python
# 获取ETF份额变动
df = pro.fund_share(ts_code='510300.SH', start_date='20260101', end_date='20260226')
```

### fund_mins - ETF历史分钟线 (5000积分)

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | 基金代码 |
| freq | str | Y | 频率: 1min/5min/15min/30min/60min |
| start_date | str | N | 开始时间 YYYY-MM-DD HH:MM:SS |
| end_date | str | N | 结束时间 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | 基金代码 |
| trade_time | 交易时间 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| vol | 成交量 |
| amount | 成交额 |

```python
df = pro.fund_mins(ts_code='510300.SH', freq='5min',
                   start_date='2026-02-25 09:30:00', end_date='2026-02-25 15:00:00')
```

---

## 指数数据

### index_basic - 指数基本信息

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| market | str | N | 市场: MSCI/CSI/SSE/SZSE/CICC/SW/OTH |
| publisher | str | N | 发布商 |
| category | str | N | 指数类别 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| name | 简称 |
| fullname | 指数全称 |
| market | 市场 |
| publisher | 发布商 |
| index_type | 指数风格 |
| category | 指数类别 |
| base_date | 基期 |
| base_point | 基点 |
| list_date | 发布日期 |
| weight_rule | 加权方式 |
| desc | 描述 |
| exp_date | 终止日期 |

### index_daily - 指数日线行情

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
| close | 收盘点位 |
| open | 开盘点位 |
| high | 最高点位 |
| low | 最低点位 |
| pre_close | 昨收点位 |
| change | 涨跌点 |
| pct_chg | 涨跌幅% |
| vol | 成交量 |
| amount | 成交额 |

### index_weight - 指数成分和权重

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| index_code | str | Y | 指数代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| index_code | 指数代码 |
| con_code | 成分股代码 |
| trade_date | 交易日期 |
| weight | 权重% |

### index_dailybasic - 大盘指数每日指标

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| trade_date | str | N | 交易日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| trade_date | 交易日期 |
| total_mv | 总市值 |
| float_mv | 流通市值 |
| total_share | 总股本 |
| float_share | 流通股本 |
| free_share | 自由流通股本 |
| turnover_rate | 换手率% |
| turnover_rate_f | 自由流通换手率% |
| pe | 市盈率 |
| pe_ttm | 市盈率TTM |
| pb | 市净率 |

### index_classify - 申万行业分类

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| level | str | N | 行业级别: L1/L2/L3 |
| src | str | N | 指数版本: SW2014/SW2021 |

| 输出字段 | 说明 |
|----------|------|
| index_code | 指数代码 |
| industry_name | 行业名称 |
| level | 行业级别 |
| industry_code | 行业代码 |
| is_pub | 是否发布指数 |
| parent_code | 父级代码 |

### index_member - 申万行业成分

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| index_code | str | N | 指数代码 |
| ts_code | str | N | 股票代码 |
| is_new | str | N | 是否最新: Y最新 |

| 输出字段 | 说明 |
|----------|------|
| index_code | 指数代码 |
| index_name | 指数名称 |
| con_code | 成分股代码 |
| con_name | 成分股名称 |
| in_date | 纳入日期 |
| out_date | 剔除日期 |
| is_new | 是否最新 |

### sw_daily - 申万行业日线行情

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
| name | 指数名称 |
| open | 开盘点位 |
| close | 收盘点位 |
| high | 最高点位 |
| low | 最低点位 |
| pre_close | 昨收点位 |
| change | 涨跌点 |
| pct_chg | 涨跌幅% |
| vol | 成交量 |
| amount | 成交额 |

### index_global - 全球主要指数

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
| open | 开盘点位 |
| close | 收盘点位 |
| high | 最高点位 |
| low | 最低点位 |
| pre_close | 昨收点位 |
| change | 涨跌点 |
| pct_chg | 涨跌幅% |
| vol | 成交量 |

---

## 基金数据

### fund_basic - 基金列表

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| market | str | N | 市场: E场内/O场外 |
| status | str | N | 状态: D摘牌/I发行/L上市中 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| name | 简称 |
| management | 管理人 |
| custodian | 托管人 |
| fund_type | 基金类型 |
| found_date | 成立日期 |
| due_date | 到期日 |
| list_date | 上市日期 |
| issue_date | 发行日期 |
| delist_date | 退市日期 |
| issue_amount | 发行份额 (亿份) |
| m_fee | 管理费 |
| c_fee | 托管费 |
| duration_year | 存续期 |
| p_value | 面值 |
| min_amount | 起点金额 (万元) |
| exp_return | 预期收益率 |
| benchmark | 业绩比较基准 |
| status | 状态 |
| invest_type | 投资风格 |

### fund_nav - 基金净值

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | TS代码 |
| nav_date | str | N | 净值日期 |
| market | str | N | 市场 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| nav_date | 净值日期 |
| unit_nav | 单位净值 |
| accum_nav | 累计净值 |
| accum_div | 累计分红 |
| net_asset | 资产净值 |
| total_netasset | 合计资产净值 |
| adj_nav | 复权单位净值 |

### fund_portfolio - 基金持仓

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | Y | TS代码 |
| ann_date | str | N | 公告日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| symbol | 股票代码 |
| mkv | 市值 (元) |
| amount | 持有股票数量 (股) |
| stk_mkv_ratio | 占股票市值比% |
| stk_float_ratio | 占流通股比% |

### fund_div - 基金分红

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| ann_date | str | N | 公告日期 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| imp_anndate | 实施公告日 |
| base_date | 基准日 |
| div_proc | 方案进度 |
| div_mode | 分红方式 |
| div_cash | 每份分红现金 |
| base_unit | 基准基金份额 (万份) |
| earn_distr | 可分配收益 (万元) |
| ex_date | 除息日 |
| pay_date | 派息日 |
| pay_finish_date | 派息完成日 |

### fund_manager - 基金经理

| 参数 | 类型 | 必选 | 说明 |
|------|------|------|------|
| ts_code | str | N | TS代码 |
| ann_date | str | N | 公告日期 |
| name | str | N | 经理姓名 |
| fund_code | str | N | 基金代码 |

| 输出字段 | 说明 |
|----------|------|
| ts_code | TS代码 |
| ann_date | 公告日期 |
| name | 经理姓名 |
| gender | 性别 |
| birth_year | 出生年份 |
| edu | 学历 |
| nationality | 国籍 |
| begin_date | 任职日期 |
| end_date | 离任日期 |
| resume | 简历 |
