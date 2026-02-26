# MCP ts-data Tool Routing

Maps MCP `mcp__ts-data__*` tools to underlying Tushare Pro APIs.
Use this to decide: MCP (zero-code, preferred) vs Python tushare (fallback for uncovered APIs).

## MCP Tool -> Tushare API Mapping

| MCP Tool | Tushare API(s) | Notes |
|----------|---------------|-------|
| search_stocks | stock_basic, index_basic, hk_basic, us_basic | Multi-market search |
| search_financial_entity | stock_basic (local DB) | Supports pinyin |
| get_entity_by_code | stock_basic | Exact lookup |
| get_stock_data | daily + daily_basic + fina_indicator + stock_basic | Composite |
| get_latest_daily_close | daily / index_daily / hk_daily / us_daily | Latest single day |
| get_historical_data | daily / index_daily / hk_daily / us_daily | With stats |
| get_moneyflow | moneyflow | A-share only |
| get_financial_indicators | income + balancesheet (core fields) | Simplified |
| get_basic_info | stock_basic / hk_basic / us_basic | Multi-market |
| get_income_statement | income | Single period |
| get_balance_sheet | balancesheet | Single period |
| get_cashflow_statement | cashflow | Single period |
| get_financial_indicator | fina_indicator | ROE/ROA etc |
| get_forecast | forecast | Earnings forecast |
| get_express | express | Flash report |
| get_sector_top_stocks | stock_basic + daily_basic (by industry) | Shenwan classification |
| get_top_list | dragon_tiger (simplified) | Dragon-tiger board |
| get_financial_metrics | daily_basic + fina_indicator + income | Aggregated metrics |
| analyze_price_correlation | daily (multi-stock) | Correlation matrix |
| analyze_stock_performance | daily (multi-stock) | Sharpe, risk metrics |
| calculate_metrics | daily (multi-stock) | Correlation on field |
| get_batch_pct_chg | daily (multi-stock) | Period returns |
| get_market_summary | daily (全市场) | Market-wide stats |
| get_market_extremes | daily (全市场) | Top gainers/losers |
| get_macro_summary | cn_gdp + cn_cpi + cn_ppi + cn_pmi + cn_m + shibor_lpr | Composite |
| get_gdp_data | cn_gdp | GDP quarterly |
| get_cpi_data | cn_cpi | CPI monthly |
| get_pmi_data | cn_pmi | PMI monthly |
| get_money_supply | cn_m | M0/M1/M2 |
| get_interest_rates | shibor_lpr | LPR rates |
| get_ppi_data | cn_ppi | PPI monthly |
| get_index_weight | index_weight | Index components |
| get_index_valuation | index_dailybasic | PE/PB of index |
| get_industry_overview | index_classify + sw_daily | Shenwan sectors |
| analyze_sector | stock_basic + daily + fina_indicator (by sector) | Sector deep-dive |

## APIs NOT Covered by MCP (require Python tushare)

### 行情细分
stk_mins, realtime_mins, weekly, monthly, adj_factor, stk_limit, suspend_d

### 打板专题
dragon_tiger (details), limit_list_d, stk_limit_d, limit_step, limit_sector, ths_index/daily/member, dc_index/member, stk_surv, ths_hot, dc_hot

### 特色数据
cyq_perf, cyq_chips, stk_factor_pro, broker_recommend, stk_auction, magic_nine, ah_comparision

### 两融
margin, margin_detail, margin_target, slb_len_mm

### 参考数据
top10_holders, top10_floatholders, pledge_stat, pledge_detail, repurchase, share_float, block_trade, stk_holdernumber, stk_holdertrade

### 港股财务
hk_income, hk_balancesheet, hk_cashflow, hk_fina_indicator

### 美股财务
us_income, us_balancesheet, us_cashflow, us_fina_indicator

### 期货
fut_basic, fut_daily, fut_holding, fut_wsr, fut_settle, fut_mapping

### 期权
opt_basic, opt_daily

### 债券
cb_basic, cb_issue, cb_daily, cb_rate, cb_call, repo_daily, yc_cb

### 外汇
fx_obasic, fx_daily

### 基金详情
fund_nav, fund_div, fund_portfolio, fund_manager

### 宏观利率
shibor, shibor_quote, libor, hibor, sf_month, us_tycr, us_tbr, us_tltr

### 大模型语料
news, major_news, cctv_news, anns, irm_qa_sh, irm_qa_sz, gov_policy, report_broker

### 分红送股
dividend

### 财务审计
fina_audit, fina_mainbz, disclosure_date
