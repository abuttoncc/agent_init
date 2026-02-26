# Tushare Pro API Reference: Stock Market

Core APIs for A-share stock quotes, trading data, and market information.

---

## stock_basic - 股票列表

Get the full list of stocks with basic information.

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码，如 000001.SZ |
| name | str | N | 股票名称 |
| market | str | N | 市场类别：主板/创业板/科创板/CDR/北交所 |
| list_status | str | N | 上市状态：L 上市 / D 退市 / P 暂停上市 / G 未上市 |
| exchange | str | N | 交易所：SSE 上交所 / SZSE 深交所 / BSE 北交所 |
| is_hs | str | N | 是否沪深港通：N 否 / H 沪股通 / S 深股通 |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码（带后缀，如 000001.SZ） |
| symbol | 股票代码（纯数字） |
| name | 股票名称 |
| area | 地域 |
| industry | 所属行业 |
| fullname | 股票全称 |
| enname | 英文全称 |
| cnspell | 拼音缩写 |
| market | 市场类别 |
| exchange | 交易所代码 |
| curr_type | 交易货币 |
| list_status | 上市状态 |
| list_date | 上市日期（YYYYMMDD） |
| delist_date | 退市日期 |
| is_hs | 是否沪深港通 |
| act_name | 实控人名称 |
| act_ent_type | 实控人企业性质 |

### Example

```python
# 获取所有上市状态的主板股票
df = pro.stock_basic(list_status='L', market='主板',
                     fields='ts_code,name,industry,list_date')

# 查询沪股通标的
df = pro.stock_basic(is_hs='H', fields='ts_code,name,industry')
```

---

## daily - A股日线行情

获取 A 股未复权日线行情数据。停牌期间无数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码，如 600519.SH（ts_code 和 trade_date 至少一个） |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 YYYYMMDD |
| end_date | str | N | 结束日期 YYYYMMDD |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| pre_close | 昨收价 |
| change | 涨跌额 |
| pct_chg | 涨跌幅（%） |
| vol | 成交量（手） |
| amount | 成交额（千元） |

> **Note:** 返回的是未复权行情。如需复权数据，请使用 `pro_bar` 接口并设置 `adj` 参数。

### Example

```python
# 获取贵州茅台近期日线
df = pro.daily(ts_code='600519.SH', start_date='20250101', end_date='20250228')

# 获取某一天全市场行情
df = pro.daily(trade_date='20250226')
```

---

## daily_basic - 每日指标

获取股票每日估值、换手率等核心指标。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| close | 当日收盘价 |
| turnover_rate | 换手率（%） |
| turnover_rate_f | 自由流通换手率（%） |
| volume_ratio | 量比 |
| pe | 市盈率（动态） |
| pe_ttm | 市盈率（TTM） |
| pb | 市净率 |
| ps | 市销率 |
| ps_ttm | 市销率（TTM） |
| dv_ratio | 股息率（%） |
| dv_ttm | 股息率（TTM，%） |
| total_share | 总股本（万股） |
| float_share | 流通股本（万股） |
| free_share | 自由流通股本（万股） |
| total_mv | 总市值（万元） |
| circ_mv | 流通市值（万元） |

### Example

```python
# 获取某日全市场估值指标
df = pro.daily_basic(trade_date='20250226',
                     fields='ts_code,trade_date,pe_ttm,pb,total_mv,circ_mv')

# 获取单只股票历史估值
df = pro.daily_basic(ts_code='000858.SZ', start_date='20240101', end_date='20250226')
```

---

## weekly - 周线行情

获取 A 股周线行情数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| close | 周收盘价 |
| open | 周开盘价 |
| high | 周最高价 |
| low | 周最低价 |
| pre_close | 上周收盘价 |
| change | 周涨跌额 |
| pct_chg | 周涨跌幅（%） |
| vol | 周成交量（手） |
| amount | 周成交额（千元） |

### Example

```python
df = pro.weekly(ts_code='600519.SH', start_date='20240101', end_date='20250226')
```

---

## monthly - 月线行情

获取 A 股月线行情数据。字段结构与 weekly 相同。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

### Output Fields

Same as weekly (ts_code, trade_date, close, open, high, low, pre_close, change, pct_chg, vol, amount).

### Example

```python
df = pro.monthly(ts_code='600519.SH', start_date='20230101', end_date='20250226')
```

---

## adj_factor - 复权因子

获取股票复权因子，用于手动计算前/后复权价格。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | **Y** | 股票代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| adj_factor | 复权因子 |

> **计算方法:** 前复权价 = 收盘价 × 当日复权因子 / 最新复权因子；后复权价 = 收盘价 × 当日复权因子。

### Example

```python
df = pro.adj_factor(ts_code='000001.SZ', start_date='20250101', end_date='20250226')
```

---

## pro_bar - 通用行情接口

最通用的行情获取接口，支持股票、指数、基金、期货、期权、可转债，支持复权和均线计算。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | **Y** | 证券代码 |
| start_date | str | N | 开始日期 YYYYMMDD |
| end_date | str | N | 结束日期 YYYYMMDD |
| asset | str | N | 资产类别：E 股票（默认）/ I 指数 / FT 期货 / FD 基金 / O 期权 / CB 可转债 |
| adj | str | N | 复权类型：qfq 前复权 / hfq 后复权 / None 不复权（默认） |
| freq | str | N | 数据频度：D 日线（默认）/ W 周线 / M 月线 |
| ma | list | N | 均线周期列表，如 [5, 20, 60] |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 证券代码 |
| trade_date | 交易日期 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| pre_close | 昨收价 |
| change | 涨跌额 |
| pct_chg | 涨跌幅（%） |
| vol | 成交量（手） |
| amount | 成交额（千元） |
| ma5 / ma20 / ma60 ... | 均线值（仅当 ma 参数指定时返回） |
| ma_v_5 / ma_v_20 ... | 成交量均线（仅当 ma 参数指定时返回） |

> **Note:** 这是最常用的行情接口。需要复权数据或均线时优先使用此接口。

### Example

```python
import tushare as ts

# 获取前复权日线 + 5/20/60 日均线
df = ts.pro_bar(ts_code='600519.SH', adj='qfq',
                start_date='20240101', end_date='20250226',
                ma=[5, 20, 60])

# 获取指数周线
df = ts.pro_bar(ts_code='000300.SH', asset='I', freq='W',
                start_date='20240101', end_date='20250226')

# 获取可转债日线
df = ts.pro_bar(ts_code='113009.SH', asset='CB',
                start_date='20250101', end_date='20250226')
```

---

## stk_mins - 历史分钟线

获取 A 股历史分钟级行情数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | **Y** | 股票代码 |
| freq | str | **Y** | 分钟频度：1min / 5min / 15min / 30min / 60min |
| start_date | str | N | 开始时间 YYYY-MM-DD HH:MM:SS |
| end_date | str | N | 结束时间 YYYY-MM-DD HH:MM:SS |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| trade_time | 交易时间 |
| open | 开盘价 |
| high | 最高价 |
| low | 最低价 |
| close | 收盘价 |
| vol | 成交量 |
| amount | 成交额 |

### Example

```python
df = pro.stk_mins(ts_code='000001.SZ', freq='5min',
                  start_date='2025-02-26 09:30:00',
                  end_date='2025-02-26 15:00:00')
```

---

## stk_limit - 每日涨跌停价格

获取每日涨停价和跌停价。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| pre_close | 昨日收盘价 |
| up_limit | 涨停价 |
| down_limit | 跌停价 |

### Example

```python
df = pro.stk_limit(trade_date='20250226')
```

---

## suspend_d - 每日停复牌信息

获取每日停牌和复牌数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| suspend_type | str | N | 停复牌类型：S 停牌 / R 复牌 |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| suspend_timing | 停复牌时间 |
| suspend_type | 停复牌类型 |

### Example

```python
# 查询某日所有停牌股票
df = pro.suspend_d(trade_date='20250226', suspend_type='S')
```

---

## hsgt_top10 - 沪深股通十大成交股

获取沪深股通每日十大成交股数据。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |
| market_type | str | N | 市场类型：SH 沪股通 / SZ 深股通 |

### Output Fields

| Name | Description |
|------|-------------|
| trade_date | 交易日期 |
| ts_code | 股票代码 |
| name | 股票名称 |
| close | 收盘价 |
| change | 涨跌额 |
| rank | 资金排名 |
| market_type | 市场类型 |
| amount | 成交金额（万元） |
| net_amount | 净买入金额（万元） |
| buy | 买入金额（万元） |
| sell | 卖出金额（万元） |

### Example

```python
# 获取某日沪股通十大成交股
df = pro.hsgt_top10(trade_date='20250226', market_type='SH')
```

---

## moneyflow - 个股资金流向

获取个股每日资金流向数据，按小单/中单/大单/特大单拆分。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

### Output Fields

| Name | Description |
|------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| buy_sm_vol | 小单买入量（手） |
| buy_sm_amount | 小单买入金额（万元） |
| sell_sm_vol | 小单卖出量（手） |
| sell_sm_amount | 小单卖出金额（万元） |
| buy_md_vol | 中单买入量（手） |
| buy_md_amount | 中单买入金额（万元） |
| sell_md_vol | 中单卖出量（手） |
| sell_md_amount | 中单卖出金额（万元） |
| buy_lg_vol | 大单买入量（手） |
| buy_lg_amount | 大单买入金额（万元） |
| sell_lg_vol | 大单卖出量（手） |
| sell_lg_amount | 大单卖出金额（万元） |
| buy_elg_vol | 特大单买入量（手） |
| buy_elg_amount | 特大单买入金额（万元） |
| sell_elg_vol | 特大单卖出量（手） |
| sell_elg_amount | 特大单卖出金额（万元） |
| net_mf_vol | 主力净流入量（手） |
| net_mf_amount | 主力净流入金额（万元） |

> **Note:** sm = 小单, md = 中单, lg = 大单, elg = 特大单, net_mf = 主力净流入（大单 + 特大单）。

### Example

```python
# 获取贵州茅台近期资金流向
df = pro.moneyflow(ts_code='600519.SH', start_date='20250201', end_date='20250226')

# 获取某日全市场资金流向
df = pro.moneyflow(trade_date='20250226')
```

---

## trade_cal - 交易日历

获取各大交易所的交易日历。

### Input Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| exchange | str | N | 交易所：SSE 上交所 / SZSE 深交所 / CFFEX 中金所 / SHFE 上期所 / CZCE 郑商所 / DCE 大商所 / INE 上海国际能源交易中心 |
| start_date | str | N | 开始日期 YYYYMMDD |
| end_date | str | N | 结束日期 YYYYMMDD |
| is_open | str | N | 是否交易日：0 休市 / 1 交易日 |

### Output Fields

| Name | Description |
|------|-------------|
| exchange | 交易所代码 |
| cal_date | 日历日期 |
| is_open | 是否交易日 |
| pretrade_date | 上一个交易日 |

### Example

```python
# 获取2025年上交所交易日历
df = pro.trade_cal(exchange='SSE', start_date='20250101', end_date='20251231')

# 仅获取交易日
df = pro.trade_cal(exchange='SSE', start_date='20250101', end_date='20250331', is_open='1')
```
