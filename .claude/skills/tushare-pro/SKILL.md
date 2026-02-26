---
name: tushare-pro
description: |
  通过 Tushare Pro Python 库获取中国金融市场数据。覆盖 A股/港股/美股行情、
  财务报表、ETF/指数/基金、期货期权、可转债、两融、龙虎榜、筹码分布、
  宏观经济等 200+ API。触发词：tushare、股票数据、行情、财务、龙虎榜、
  筹码、两融、可转债、期货、ETF、基金、宏观数据。
user-invocable: true
triggers:
  - tushare
  - 股票数据
  - A股数据
  - 财务数据查询
  - 行情数据
  - 日线数据
  - 龙虎榜
  - 筹码分布
  - 两融数据
  - 资金流向
  - 指数成分
  - 期货数据
  - 可转债
  - 港股数据
  - 美股数据
  - 宏观数据
  - 分钟线
  - tick数据
  - 复权因子
  - 股东数据
  - 质押数据
  - 大宗交易
  - ETF
  - 基金数据
---

# Tushare Pro Data Retrieval

通过 Python `tushare` 库调用 Tushare Pro 200+ API，覆盖中国金融市场全品类数据。

## API 覆盖范围

| 类别 | 核心 API | 详细文档 |
|------|---------|---------|
| A股行情 | `daily`, `weekly`, `monthly`, `pro_bar`, `stk_mins`, `adj_factor`, `daily_basic` | `references/api-stock.md` |
| 财务报表 | `income`, `balancesheet`, `cashflow`, `fina_indicator`, `forecast`, `express`, `dividend` | `references/api-financial.md` |
| ETF | `fund_basic(market='E')`, `fund_daily`, `fund_adj`, `fund_share`, `fund_mins` | `references/api-index-fund.md` |
| 指数 | `index_basic`, `index_daily`, `index_weight`, `index_dailybasic`, `index_classify`, `sw_daily` | `references/api-index-fund.md` |
| 公募基金 | `fund_basic`, `fund_nav`, `fund_portfolio`, `fund_div`, `fund_manager` | `references/api-index-fund.md` |
| 龙虎榜/打板 | `top_list`, `limit_list_d`, `limit_step`, `ths_index`, `ths_daily`, `ths_member` | `references/api-special.md` |
| 特色数据 | `cyq_perf`(筹码), `broker_recommend`(金股), `stk_auction`(集合竞价) | `references/api-special.md` |
| 两融 | `margin`, `margin_detail`, `margin_target` | `references/api-special.md` |
| 参考数据 | `top10_holders`, `pledge_stat`, `block_trade`, `share_float`, `stk_holdernumber` | `references/api-special.md` |
| 资金流向 | `moneyflow`, `moneyflow_ths`, `moneyflow_ind_ths` | `references/api-special.md` |
| 港股 | `hk_basic`, `hk_daily`, `hk_income`, `hk_balancesheet`, `hk_cashflow` | `references/api-global.md` |
| 美股 | `us_basic`, `us_daily`, `us_income`, `us_balancesheet`, `us_cashflow` | `references/api-global.md` |
| 期货 | `fut_basic`, `fut_daily`, `fut_holding`, `fut_mapping` | `references/api-global.md` |
| 期权 | `opt_basic`, `opt_daily` | `references/api-global.md` |
| 债券/可转债 | `cb_basic`, `cb_daily`, `yc_cb`, `repo_daily` | `references/api-global.md` |
| 外汇 | `fx_obasic`, `fx_daily` | `references/api-global.md` |
| 宏观经济 | `cn_gdp`, `cn_cpi`, `cn_ppi`, `cn_pmi`, `cn_m`, `shibor`, `shibor_lpr` | `references/api-macro.md` |
| 大模型语料 | `news`, `major_news`, `anns`, `cctv_news` | `references/api-global.md` |

完整 API 索引: `references/api-catalog.md` (158+ API)

## 调用方式

```python
import os
import tushare as ts

pro = ts.pro_api(os.environ.get('TUSHARE_TOKEN'))
df = pro.api_name(param1='value1', param2='value2')
```

备用方式 — HTTP POST（无需安装库）:

```python
import requests, os

resp = requests.post('http://api.tushare.pro', json={
    "api_name": "daily",
    "token": os.environ.get('TUSHARE_TOKEN'),
    "params": {"ts_code": "600519.SH", "start_date": "20250101"},
    "fields": "ts_code,trade_date,open,high,low,close,vol"
})
data = resp.json()
```

详见 `references/calling-convention.md`。

## 数据格式约定

| 项目 | 规范 |
|------|------|
| 日期格式 | `YYYYMMDD` (字符串), 如 `"20260226"` |
| 股票代码 | ts_code 带交易所后缀: `.SH`(沪) `.SZ`(深) `.BJ`(北) `.HK`(港) |
| Token | `os.environ.get('TUSHARE_TOKEN')` — 不要硬编码 |
| 安装 | `pip install tushare --break-system-packages` |
| 财报周期 | `period='20241231'`(年报) `'20240930'`(Q3) `'20240630'`(中报) `'20240331'`(Q1) |

## 文件放置

按项目规范，所有生成脚本和数据放临时工作区:

| 类型 | 位置 |
|------|------|
| 一次性脚本 | `tmp/session/*.py` |
| 中间数据 | `tmp/session/data/` |
| 最终产物 | `tmp/session/output/` |

## Reference 文件

| 文件 | 内容 |
|------|------|
| `api-catalog.md` | 全量 API 索引 (按类别分组) |
| `api-stock.md` | 股票行情 API (日线/周线/分钟线/复权) |
| `api-financial.md` | 财务报表与指标 API |
| `api-index-fund.md` | ETF / 指数 / 基金 API |
| `api-special.md` | 龙虎榜 / 两融 / 筹码 / 质押 / 大宗交易 |
| `api-macro.md` | 宏观经济数据 API |
| `api-global.md` | 港股 / 美股 / 期货 / 期权 / 债券 / 外汇 / 语料 |
| `calling-convention.md` | Python/HTTP 调用规范与错误处理 |

使用示例: `examples/common-patterns.md`
