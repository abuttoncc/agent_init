# Tushare Pro API Reference: Special & Featured Data

> These APIs cover unique datasets that the MCP `ts-data` tools do **not** provide.
> Call them directly via `tushare.pro_api().query(api_name, **params)`.

```python
import tushare as ts
pro = ts.pro_api()  # token from env TUSHARE_TOKEN
```

---

## 1. 打板专题 (Limit-Up/Down & Hot Money)

### dragon_tiger - 龙虎榜每日明细

Top movers list published by exchanges, showing institutional/hot-money activity.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| trade_date | 交易日期 |
| ts_code | 股票代码 |
| name | 股票名称 |
| close | 收盘价 |
| pct_change | 涨跌幅%（注意：不是 pct_chg） |
| turnover_rate | 换手率% |
| amount | 总成交额 (元) |
| l_sell | 龙虎榜卖出额 (元) |
| l_buy | 龙虎榜买入额 (元) |
| l_amount | 龙虎榜成交额 (元) |
| net_amount | 龙虎榜净买入 (元) |
| net_rate | 净买额占比% |
| amount_rate | 龙虎榜成交额占总成交额% |
| float_values | 流通市值 (元) |
| reason | 上榜原因 |

> **注意**: 实际 API 名称是 `top_list`（非 `dragon_tiger`），涨跌幅字段是 `pct_change`（非 `pct_chg`），金额单位是元（非万元）。

```python
df = pro.top_list(trade_date='20260225')
# 或
df = pro.query('top_list', trade_date='20260225')
```

---

### limit_list_d - 每日涨跌停统计

Daily limit-up / limit-down stock list with seal strength metrics.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| trade_date | str | Y | 交易日期 YYYYMMDD |
| limit_type | str | N | U=涨停, D=跌停 |

**Output**

| Field | Description |
|-------|-------------|
| trade_date | 交易日期 |
| ts_code | 股票代码 |
| name | 股票名称 |
| close | 收盘价 |
| pct_chg | 涨跌幅% |
| fd_amount | 封单金额 |
| first_time | 首次封板时间 HH:MM:SS |
| last_time | 最后封板时间 |
| open_times | 打开次数 |
| strth | 封单强度 |
| limit | D=跌停, U=涨停, Z=炸板 |

```python
# 获取某日涨停股
df = pro.query('limit_list_d', trade_date='20260225', limit_type='U')
```

---

### limit_step - 连板天梯

Consecutive limit-up/down ladder — shows how many days each stock has hit the limit.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| trade_date | str | Y | 交易日期 YYYYMMDD |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| name | 股票名称 |
| close | 收盘价 |
| pct_chg | 涨跌幅% |
| step | 连板数 |
| limit | U=涨停, D=跌停 |

```python
# 查看连板天梯
df = pro.query('limit_step', trade_date='20260225')
top = df.sort_values('step', ascending=False).head(10)
```

---

### ths_index - 同花顺概念/行业指数

THS (10jqka) concept & industry index directory.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| exchange | str | N | A=A股, HK=港股, US=美股 |
| type | str | N | N=概念, I=行业, S=特色, R=地区 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 指数代码 |
| name | 指数名称 |
| count | 成分个数 |
| exchange | 交易所 |
| list_date | 发布日期 |
| type | 类型 |

```python
# 获取所有A股概念指数
concepts = pro.query('ths_index', exchange='A', type='N')
```

---

### ths_daily - 同花顺指数行情

Daily quotes for THS indices.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 指数代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 指数代码 |
| trade_date | 交易日期 |
| close | 收盘 |
| open | 开盘 |
| high | 最高 |
| low | 最低 |
| pre_close | 昨收 |
| avg_price | 均价 |
| change | 涨跌点 |
| pct_chg | 涨跌幅% |
| vol | 成交量 |
| turnover_rate | 换手率% |

```python
df = pro.query('ths_daily', ts_code='885760.TI',
               start_date='20260101', end_date='20260225')
```

---

### ths_member - 同花顺指数成分

Constituent stocks of a THS index.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | Y | 指数代码 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 指数代码 |
| code | 成分股代码 |
| name | 成分股名称 |
| weight | 权重 |
| in_date | 纳入日期 |
| out_date | 剔除日期 |
| is_new | 是否新纳入 |

```python
members = pro.query('ths_member', ts_code='885760.TI')
```

---

## 2. 特色数据 (Featured Data)

### cyq_perf - 每日筹码分布 & 胜率

Chip distribution and win-rate metrics derived from cost-basis analysis.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | Y | 股票代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| his_low | 历史最低价 |
| his_high | 历史最高价 |
| cost_5pct | 5%分位成本价 |
| cost_15pct | 15%分位成本价 |
| cost_50pct | 50%分位成本价 (中位数成本) |
| cost_85pct | 85%分位成本价 |
| cost_95pct | 95%分位成本价 |
| weight_avg | 加权平均成本 |
| winner_rate | 胜率 (获利比例%) |

```python
df = pro.query('cyq_perf', ts_code='000001.SZ',
               start_date='20260101', end_date='20260225')
```

---

### broker_recommend - 券商月度金股

Monthly "golden stock" picks by brokerages.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| month | str | Y | 月份 YYYYMM |

**Output**

| Field | Description |
|-------|-------------|
| month | 月份 |
| broker | 券商名称 |
| ts_code | 股票代码 |
| name | 股票名称 |

```python
df = pro.query('broker_recommend', month='202602')
# 统计被推荐最多的股票
df.groupby(['ts_code','name']).size().sort_values(ascending=False).head(10)
```

---

### stk_auction - 开盘集合竞价

Opening call auction data.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| open | 开盘价 |
| vol | 成交量 |
| amount | 成交额 |

```python
df = pro.query('stk_auction', trade_date='20260225')
```

---

## 3. 两融数据 (Margin Trading)

### margin - 融资融券交易汇总

Exchange-level margin trading summary.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| trade_date | str | N | 交易日期 |
| exchange_id | str | N | SSE=上交所, SZSE=深交所 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| trade_date | 交易日期 |
| exchange_id | 交易所 |
| rzye | 融资余额 (万元) |
| rzmre | 融资买入额 (万元) |
| rzche | 融资偿还额 (万元) |
| rqye | 融券余额 (万元) |
| rqmcl | 融券卖出量 |
| rzrqye | 融资融券余额 (万元) |

```python
df = pro.query('margin', trade_date='20260225')
```

---

### margin_detail - 融资融券交易明细

Per-stock margin trading detail.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| trade_date | str | N | 交易日期 |
| ts_code | str | N | 股票代码 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| trade_date | 交易日期 |
| ts_code | 股票代码 |
| name | 股票名称 |
| rzye | 融资余额 |
| rzmre | 融资买入额 |
| rzche | 融资偿还额 |
| rqye | 融券余额 |
| rqmcl | 融券卖出量 |
| rqyl | 融券余量 |
| rqchl | 融券偿还量 |
| rqjml | 融券净卖出 |
| rzrqye | 融资融券余额 |

```python
# 单只股票的两融趋势
df = pro.query('margin_detail', ts_code='600519.SH',
               start_date='20260101', end_date='20260225')
```

---

## 4. 参考数据 (Reference Data)

### top10_holders - 前十大股东

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | Y | 股票代码 |
| period | str | N | 报告期 YYYYMMDD |
| ann_date | str | N | 公告日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| end_date | 报告期 |
| holder_name | 股东名称 |
| hold_amount | 持股数量 |
| hold_ratio | 持股比例% |

```python
df = pro.query('top10_holders', ts_code='600519.SH', period='20250930')
```

---

### top10_floatholders - 前十大流通股东

Same interface as `top10_holders`, but for tradable (float) shares only.

```python
df = pro.query('top10_floatholders', ts_code='600519.SH', period='20250930')
```

---

### pledge_stat - 股权质押统计

Share pledge statistics.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | Y | 股票代码 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| end_date | 截止日期 |
| pledge_count | 质押次数 |
| unrest_pledge | 无限售质押数量 (万股) |
| rest_pledge | 限售质押数量 (万股) |
| total_share | 总股本 (万股) |
| pledge_ratio | 质押比例% |

```python
df = pro.query('pledge_stat', ts_code='000001.SZ')
```

---

### block_trade - 大宗交易

Block (negotiated) trades.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| price | 成交价 |
| vol | 成交量 (万股) |
| amount | 成交金额 (万元) |
| buyer | 买方营业部 |
| seller | 卖方营业部 |

```python
df = pro.query('block_trade', trade_date='20260225')
```

---

### share_float - 限售股解禁

Restricted share unlock schedule.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| ann_date | str | N | 公告日期 |
| float_date | str | N | 解禁日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| float_date | 解禁日期 |
| float_share | 流通股份 (万股) |
| float_ratio | 流通股份占总股本比% |
| holder_name | 股东名称 |
| share_type | 股份类型 |

```python
# 未来一周解禁
df = pro.query('share_float', start_date='20260226', end_date='20260305')
```

---

### stk_holdernumber - 股东人数

Shareholder count trend — useful for detecting concentration/distribution.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | Y | 股票代码 |
| enddate | str | N | 截止日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| ann_date | 公告日期 |
| end_date | 截止日期 |
| holder_num | 股东户数 |

```python
df = pro.query('stk_holdernumber', ts_code='000001.SZ',
               start_date='20250101', end_date='20260225')
```

---

## 5. 资金流向 (Money Flow) — MCP 未完全覆盖

### moneyflow_ths - 个股资金流向 (同花顺)

Per-stock money flow by order size tiers.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| ts_code | str | N | 股票代码 |
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| ts_code | 股票代码 |
| trade_date | 交易日期 |
| name | 股票名称 |
| pct_chg | 涨跌幅% |
| net_amount | 资金净流入 |
| buy_elg_amount | 特大单买入 |
| sell_elg_amount | 特大单卖出 |
| buy_lg_amount | 大单买入 |
| sell_lg_amount | 大单卖出 |
| buy_md_amount | 中单买入 |
| sell_md_amount | 中单卖出 |
| buy_sm_amount | 小单买入 |
| sell_sm_amount | 小单卖出 |

```python
df = pro.query('moneyflow_ths', ts_code='000001.SZ',
               start_date='20260201', end_date='20260225')
```

---

### moneyflow_ind_ths - 板块资金流向 (同花顺)

Sector/concept-level money flow aggregation.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| trade_date | str | N | 交易日期 |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |
| ts_code | str | N | 板块代码 |

**Output**

Same structure as `moneyflow_ths` but at sector level.

```python
df = pro.query('moneyflow_ind_ths', trade_date='20260225')
# 净流入最多的板块
df.sort_values('net_amount', ascending=False).head(10)
```
