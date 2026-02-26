# Tushare Pro 常用模式速查

> 本文档收录 Tushare Pro 常见使用场景，每个模式标注 MCP 工具可用性，并提供 MCP 调用方式和 Python 回退方案。

---

## 1. 个股基本面速查

**MCP 支持**: ✅ MCP `get_stock_data`

### MCP 方式

调用 `mcp__ts-data__get_stock_data`，传入股票代码，一次性返回基本信息、最新行情、核心财务指标。

### Python 回退

```python
import tushare as ts
pro = ts.pro_api()

ts_code = '600519.SH'

# 每日基本面指标（市盈率、市净率、换手率等）
df_basic = pro.daily_basic(ts_code=ts_code, trade_date='20241231',
                           fields='ts_code,trade_date,pe,pb,ps,turnover_rate,total_mv,circ_mv')

# 财务指标（ROE、毛利率、净利率等）
df_fina = pro.fina_indicator(ts_code=ts_code, period='20241231',
                             fields='ts_code,ann_date,end_date,roe,grossprofit_margin,netprofit_margin,current_ratio')
```

---

## 2. 获取历史行情（含复权）

**MCP 支持**: ✅ MCP `get_historical_data`（不支持复权参数）

### MCP 方式

调用 `mcp__ts-data__get_historical_data`，获取原始未复权行情。MCP 接口不提供 `adj` 参数，返回的是未复权价格。

### Python 回退（需要复权时必须用 Python）

```python
import tushare as ts
pro = ts.pro_api()

# 前复权行情
df_qfq = ts.pro_bar(ts_code='600519.SH',
                     start_date='20240101',
                     end_date='20241231',
                     adj='qfq')

# 后复权行情
df_hfq = ts.pro_bar(ts_code='600519.SH',
                     start_date='20240101',
                     end_date='20241231',
                     adj='hfq')
```

> **注意**: `pro_bar` 是 `ts` 模块的顶层函数，不是 `pro` 对象的方法。复权因子也可单独获取：`pro.adj_factor(ts_code='600519.SH', trade_date='20241231')`。

---

## 3. 批量获取行业股票表现

**MCP 支持**: ✅ MCP `get_sector_top_stocks` + `get_batch_pct_chg`

### MCP 方式

1. 调用 `mcp__ts-data__get_sector_top_stocks` 获取指定行业的股票列表
2. 调用 `mcp__ts-data__get_batch_pct_chg` 批量获取涨跌幅

### Python 回退

```python
import tushare as ts
import pandas as pd
pro = ts.pro_api()

# 获取银行业所有股票
stocks = pro.stock_basic(industry='银行', fields='ts_code,name,industry')

# 逐个获取最近一天行情
results = []
for code in stocks['ts_code']:
    df = pro.daily(ts_code=code, start_date='20241230', end_date='20241231')
    if not df.empty:
        results.append(df.iloc[0])

df_perf = pd.DataFrame(results)
df_perf = df_perf.sort_values('pct_chg', ascending=False)
```

---

## 4. 财务报表对比（多年）

**MCP 支持**: ✅ MCP `get_income_statement`（单期查询，需多次调用）

### MCP 方式

多次调用 `mcp__ts-data__get_income_statement`，每次传入不同报告期，手动汇总对比。

### Python 回退（推荐，效率更高）

```python
import tushare as ts
import pandas as pd
pro = ts.pro_api()

ts_code = '600519.SH'
periods = ['20211231', '20221231', '20231231', '20241231']

# 利润表多年对比
dfs = [pro.income(ts_code=ts_code, period=p, report_type='1') for p in periods]
df_income = pd.concat(dfs, ignore_index=True)

# 资产负债表多年对比
dfs_bs = [pro.balancesheet(ts_code=ts_code, period=p, report_type='1') for p in periods]
df_bs = pd.concat(dfs_bs, ignore_index=True)

# 现金流量表多年对比
dfs_cf = [pro.cashflow(ts_code=ts_code, period=p, report_type='1') for p in periods]
df_cf = pd.concat(dfs_cf, ignore_index=True)
```

> **report_type 说明**: `'1'` = 合并报表，`'2'` = 单季度合并，`'4'` = 调整前合并。

---

## 5. 龙虎榜分析

**MCP 支持**: ❌ 需Python

```python
import tushare as ts
pro = ts.pro_api()

# 获取某日龙虎榜（API 名称是 top_list，不是 dragon_tiger）
df = pro.top_list(trade_date='20260225')
# 返回字段: trade_date, ts_code, name, close, pct_change(注意不是pct_chg),
#           turnover_rate, amount(元), l_sell(元), l_buy(元), l_amount(元),
#           net_amount(净买入元), net_rate(净买额占比%), amount_rate(龙虎榜成交占比%),
#           float_values(流通市值元), reason(上榜原因)

# 筛选净买入前10
df_top = df.sort_values('net_amount', ascending=False).head(10)
print(df_top[['ts_code', 'name', 'close', 'pct_change', 'net_amount', 'reason']])
```

---

## 6. 筹码分布分析

**MCP 支持**: ❌ 需Python

```python
import tushare as ts
pro = ts.pro_api()

df = pro.cyq_perf(ts_code='600519.SH',
                  start_date='20241201',
                  end_date='20241231')
# 关键字段:
#   winner_rate  — 获利比例（胜率）
#   cost_5pct    — 5%成本价
#   cost_15pct   — 15%成本价
#   cost_50pct   — 50%成本价（中位成本）
#   cost_85pct   — 85%成本价
#   cost_95pct   — 95%成本价
#   weight_avg   — 加权平均成本
```

---

## 7. 两融数据分析

**MCP 支持**: ❌ 需Python

```python
import tushare as ts
pro = ts.pro_api()

# 市场融资融券汇总
df = pro.margin(trade_date='20241231')
# 关键字段: exchange_id, rzye(融资余额), rqye(融券余额), rzmre(融资买入额)

# 个股融资融券明细
df = pro.margin_detail(ts_code='600519.SH',
                       start_date='20241201',
                       end_date='20241231')
# 关键字段: rzye(融资余额), rzmre(融资买入额), rzche(融资偿还额),
#           rqyl(融券余量), rqmcl(融券卖出量), rqchl(融券偿还量)
```

---

## 8. 可转债筛选

**MCP 支持**: ❌ 需Python

```python
import tushare as ts
pro = ts.pro_api()

# 获取所有可转债基本信息
cb_list = pro.cb_basic()
# 关键字段: ts_code, bond_short_name, stk_code(正股代码), stk_short_name,
#           maturity(期限), par(面值), issue_price, convert_price(转股价)

# 筛选未到期转债
cb_active = cb_list[cb_list['delist_date'].isna()]

# 获取可转债日线行情
cb_daily = pro.cb_daily(ts_code='113008.SH',
                        start_date='20240101',
                        end_date='20241231')
# 关键字段: ts_code, trade_date, close, amount, cb_value(转换价值),
#           cb_over_rate(纯债溢价率)
```

---

## 9. 期货数据获取

**MCP 支持**: ❌ 需Python

```python
import tushare as ts
pro = ts.pro_api()

# 合约基本信息
contracts = pro.fut_basic(exchange='SHFE', fut_type='1')
# exchange 可选: CFFEX(中金所), SHFE(上期所), DCE(大商所), CZCE(郑商所), INE(能源中心)
# fut_type: '1'=期货, '2'=期权

# 日线行情
df = pro.fut_daily(ts_code='CU2501.SHF',
                   start_date='20240101',
                   end_date='20241231')
# 关键字段: ts_code, trade_date, open, high, low, close, vol, amount, oi(持仓量)

# 持仓排名
df = pro.fut_holding(trade_date='20241231',
                     symbol='IF',
                     exchange='CFFEX')
# 关键字段: broker(期货公司), vol(成交量), vol_chg, long_hld(多头持仓),
#           long_chg, short_hld(空头持仓), short_chg
```

---

## 10. 港股/美股数据

**MCP 支持**: ✅ MCP `get_stock_data`（仅基本信息和行情，财务数据需 Python）

### MCP 方式

调用 `mcp__ts-data__get_stock_data` 可获取港股/美股基本信息和最新报价。

### Python 回退（财务数据）

```python
import tushare as ts
pro = ts.pro_api()

# 港股财务 — 利润表
hk_income = pro.hk_income(ts_code='00700.HK', period='20241231')

# 港股财务 — 资产负债表
hk_bs = pro.hk_balancesheet(ts_code='00700.HK', period='20241231')

# 港股财务 — 现金流量表
hk_cf = pro.hk_cashflow(ts_code='00700.HK', period='20241231')

# 美股财务 — 利润表
us_income = pro.us_income(ts_code='AAPL', period='20241231')

# 美股财务 — 资产负债表
us_bs = pro.us_balancesheet(ts_code='AAPL', period='20241231')

# 美股财务 — 现金流量表
us_cf = pro.us_cashflow(ts_code='AAPL', period='20241231')
```

---

## 11. 宏观经济数据

**MCP 支持**: ✅ MCP `get_macro_summary` / `get_gdp_data` / `get_cpi_data` / `get_ppi_data` / `get_pmi_data` / `get_money_supply` / `get_interest_rates`（常用宏观指标）

### MCP 方式

- `mcp__ts-data__get_macro_summary` — 宏观经济综合概览
- `mcp__ts-data__get_gdp_data` — GDP 数据
- `mcp__ts-data__get_cpi_data` — CPI 数据
- `mcp__ts-data__get_ppi_data` — PPI 数据
- `mcp__ts-data__get_pmi_data` — PMI 数据
- `mcp__ts-data__get_money_supply` — 货币供应量
- `mcp__ts-data__get_interest_rates` — 利率数据

### Python 回退（MCP 不覆盖的宏观数据）

```python
import tushare as ts
pro = ts.pro_api()

# 社会融资规模（月度）
df_sf = pro.sf_month(start_m='202401', end_m='202412')
# 关键字段: month, tl(社融增量合计), gov_bond(政府债券), corp_bond(企业债券)

# Shibor 利率
df_shibor = pro.shibor(start_date='20240101', end_date='20241231')
# 关键字段: date, on(隔夜), 1w, 2w, 1m, 3m, 6m, 9m, 1y

# 美国国债收益率
df_ustycr = pro.us_tycr(start_date='20240101', end_date='20241231')
# 关键字段: date, y2(2年期), y5(5年期), y10(10年期), y30(30年期)

# LPR 利率
df_lpr = pro.shibor_lpr(start_date='20240101', end_date='20241231')
# 关键字段: date, 1y(1年期LPR), 5y(5年期LPR)
```

---

## 12. 新闻/公告/研报

**MCP 支持**: ❌ 需Python

```python
import tushare as ts
pro = ts.pro_api()

# 新闻快讯
df_news = pro.news(start_date='2024-12-31 00:00:00',
                   end_date='2024-12-31 23:59:59')
# 关键字段: datetime, title, content, channels

# 上市公司公告
df_anns = pro.anns(ts_code='600519.SH',
                   start_date='20240101',
                   end_date='20241231')
# 关键字段: ts_code, ann_date, title, url

# 券商研报
df_report = pro.report_broker(start_date='20241201',
                              end_date='20241231')
# 关键字段: org_name(机构名), title, pub_date, author, abstract
```

---

## 13. 同花顺概念/行业板块

**MCP 支持**: ❌ 需Python

```python
import tushare as ts
pro = ts.pro_api()

# 获取所有同花顺概念指数
concepts = pro.ths_index(exchange='A', type='N')
# type: 'N'=概念指数, 'I'=行业指数, 'S'=特色指数
# 关键字段: ts_code, name, count(成分股数量), list_date

# 获取某概念的成分股
members = pro.ths_member(ts_code='885766.TI')  # 概念指数代码
# 关键字段: ts_code(成分股代码), code, name

# 获取概念指数行情
ths_daily = pro.ths_daily(ts_code='885766.TI',
                          start_date='20240101',
                          end_date='20241231')
# 关键字段: ts_code, trade_date, close, pct_change, vol, turnover_rate
```

---

## 14. 完整脚本模板

以下是一个可直接使用的 Tushare 查询脚本模板：

```python
#!/usr/bin/env python3
"""Tushare data query template"""
import os
import sys
import tushare as ts
import pandas as pd

# --------------------------------------------------
# Initialize
# --------------------------------------------------
token = os.environ.get('TUSHARE_TOKEN')
if not token:
    print("Error: TUSHARE_TOKEN not set")
    sys.exit(1)

pro = ts.pro_api(token)

# --------------------------------------------------
# Query
# --------------------------------------------------
ts_code = '600519.SH'
start_date = '20240101'
end_date = '20241231'

df = pro.daily(ts_code=ts_code,
               start_date=start_date,
               end_date=end_date)

if df.empty:
    print("No data returned")
    sys.exit(0)

print(f"Retrieved {len(df)} rows")
print(df.head())

# --------------------------------------------------
# Save
# --------------------------------------------------
output_dir = 'tmp/session/data'
os.makedirs(output_dir, exist_ok=True)

output_file = f'{output_dir}/result.csv'
df.to_csv(output_file, index=False, encoding='utf-8-sig')
print(f"Saved {len(df)} rows to {output_file}")
```

---

## MCP 覆盖范围速查表

| 场景 | MCP 工具 | MCP 覆盖 |
|------|----------|----------|
| 个股基本面 | `get_stock_data` | ✅ |
| 历史行情（未复权） | `get_historical_data` | ✅ |
| 历史行情（复权） | — | ❌ 需Python |
| 行业股票列表 | `get_sector_top_stocks` | ✅ |
| 批量涨跌幅 | `get_batch_pct_chg` | ✅ |
| 利润表 | `get_income_statement` | ✅ |
| 资产负债表 | `get_balance_sheet` | ✅ |
| 现金流量表 | `get_cashflow_statement` | ✅ |
| 财务指标 | `get_financial_indicators` | ✅ |
| 资金流向 | `get_moneyflow` | ✅ |
| 宏观经济 | `get_macro_summary` 等 | ✅ 部分 |
| 龙虎榜 | — | ❌ 需Python |
| 筹码分布 | — | ❌ 需Python |
| 两融数据 | — | ❌ 需Python |
| 可转债 | — | ❌ 需Python |
| 期货 | — | ❌ 需Python |
| 港股/美股财务 | — | ❌ 需Python |
| 新闻/公告/研报 | — | ❌ 需Python |
| 同花顺概念板块 | — | ❌ 需Python |
| 社融/Shibor/LPR | — | ❌ 需Python |
