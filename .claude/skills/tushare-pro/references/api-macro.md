# Tushare Pro API Reference: Macroeconomic Data

> China & global macro indicators accessible via `tushare.pro_api().query()`.

```python
import tushare as ts
pro = ts.pro_api()  # token from env TUSHARE_TOKEN
```

---

## 1. GDP & Output

### cn_gdp - 国内生产总值

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| q | str | N | 季度, e.g. `2025Q4` |
| start_q | str | N | 开始季度 |
| end_q | str | N | 结束季度 |

**Output**

| Field | Description |
|-------|-------------|
| quarter | 季度 |
| gdp | GDP累计值 (亿元) |
| gdp_yoy | GDP同比增长% |
| pi | 第一产业累计值 (亿元) |
| si | 第二产业累计值 (亿元) |
| ti | 第三产业累计值 (亿元) |
| pi_yoy | 第一产业同比% |
| si_yoy | 第二产业同比% |
| ti_yoy | 第三产业同比% |

```python
df = pro.query('cn_gdp', start_q='2024Q1', end_q='2025Q4')
```

---

## 2. Price Indices

### cn_cpi - 居民消费价格指数

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| m | str | N | 月份, e.g. `202512` |
| start_m | str | N | 开始月份 |
| end_m | str | N | 结束月份 |

**Output**

| Field | Description |
|-------|-------------|
| month | 月份 |
| nt_val | 全国当月值 |
| nt_yoy | 全国同比% |
| nt_mom | 全国环比% |
| nt_accu | 全国累计% |
| town_val | 城市当月值 |
| town_yoy | 城市同比% |
| town_mom | 城市环比% |
| town_accu | 城市累计% |
| cnt_val | 农村当月值 |
| cnt_yoy | 农村同比% |
| cnt_mom | 农村环比% |
| cnt_accu | 农村累计% |

```python
df = pro.query('cn_cpi', start_m='202501', end_m='202601')
```

---

### cn_ppi - 工业品出厂价格指数

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| m | str | N | 月份 YYYYMM |
| start_m | str | N | 开始月份 |
| end_m | str | N | 结束月份 |

**Output**

| Field | Description |
|-------|-------------|
| month | 月份 |
| ppi_yoy | PPI同比% |
| ppi_mp_yoy | 生产资料PPI同比% |
| ppi_cg_yoy | 生活资料PPI同比% |

```python
df = pro.query('cn_ppi', start_m='202501', end_m='202601')
```

---

## 3. PMI

### cn_pmi - 采购经理指数

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| m | str | N | 月份 YYYYMM |
| start_m | str | N | 开始月份 |
| end_m | str | N | 结束月份 |

**Output**

| Field | Description |
|-------|-------------|
| month | 月份 |
| pmi | 制造业PMI |
| pmi_mp | 生产 |
| pmi_no | 新订单 |
| pmi_em | 从业人员 |
| pmi_dl | 供应商配送时间 |
| pmi_rmmc | 原材料库存 |
| pmi_mip | 主要原材料购进价格 |

```python
df = pro.query('cn_pmi', start_m='202501', end_m='202601')
# PMI > 50 = expansion, < 50 = contraction
```

---

## 4. Money & Credit

### cn_m - 货币供应量

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| m | str | N | 月份 YYYYMM |
| start_m | str | N | 开始月份 |
| end_m | str | N | 结束月份 |

**Output**

| Field | Description |
|-------|-------------|
| month | 月份 |
| m0 | M0 (流通中现金, 亿元) |
| m0_yoy | M0同比% |
| m1 | M1 (狭义货币, 亿元) |
| m1_yoy | M1同比% |
| m2 | M2 (广义货币, 亿元) |
| m2_yoy | M2同比% |

```python
df = pro.query('cn_m', start_m='202401', end_m='202601')
```

---

### sf_month - 社会融资数据 (月度)

Aggregate social financing — the broadest measure of credit creation in China.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| m | str | N | 月份 YYYYMM |
| start_m | str | N | 开始月份 |
| end_m | str | N | 结束月份 |

**Output**

| Field | Description |
|-------|-------------|
| month | 月份 |
| tl | 社融规模合计 (万亿) |
| rmb_loan | 人民币贷款 |
| fx_loan | 外币贷款 |
| undiscounted_ba | 未贴现银行承兑汇票 |
| trust_loan | 信托贷款 |
| entrusted_loan | 委托贷款 |
| corporate_bond | 企业债券 |
| equity_financing | 非金融企业境内股票融资 |
| gov_bond | 政府债券 |

```python
df = pro.query('sf_month', start_m='202401', end_m='202601')
```

---

## 5. Interest Rates

### shibor - Shibor拆放利率

Shanghai Interbank Offered Rate.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| date | str | N | 日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| date | 日期 |
| on | 隔夜% |
| 1w | 1周% |
| 2w | 2周% |
| 1m | 1月% |
| 3m | 3月% |
| 6m | 6月% |
| 9m | 9月% |
| 1y | 1年% |

```python
df = pro.query('shibor', start_date='20260101', end_date='20260225')
```

---

### shibor_lpr - LPR利率

Loan Prime Rate — the benchmark lending rate in China.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| date | str | N | 日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| date | 日期 |
| 1y | 1年期LPR% |
| 5y | 5年期LPR% |

```python
df = pro.query('shibor_lpr', start_date='20240101', end_date='20260225')
```

---

### libor - Libor利率

London Interbank Offered Rate (multi-currency).

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| date | str | N | 日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |
| curr_type | str | N | USD, GBP, EUR, JPY, CHF |

**Output**

| Field | Description |
|-------|-------------|
| date | 日期 |
| curr_type | 货币类型 |
| on | 隔夜% |
| 1w | 1周% |
| 1m | 1月% |
| 2m | 2月% |
| 3m | 3月% |
| 6m | 6月% |
| 12m | 12月% |

```python
df = pro.query('libor', curr_type='USD',
               start_date='20260101', end_date='20260225')
```

---

### us_tycr - 美国国债收益率曲线

US Treasury yield curve — essential for global macro analysis.

**Parameters**

| Name | Type | Required | Description |
|------|------|----------|-------------|
| date | str | N | 日期 YYYYMMDD |
| start_date | str | N | 开始日期 |
| end_date | str | N | 结束日期 |

**Output**

| Field | Description |
|-------|-------------|
| date | 日期 |
| 1m | 1月期收益率% |
| 2m | 2月期% |
| 3m | 3月期% |
| 6m | 6月期% |
| 1y | 1年期% |
| 2y | 2年期% |
| 3y | 3年期% |
| 5y | 5年期% |
| 7y | 7年期% |
| 10y | 10年期% |
| 20y | 20年期% |
| 30y | 30年期% |

```python
df = pro.query('us_tycr', start_date='20260101', end_date='20260225')
# 10y-2y spread (yield curve inversion check)
df['spread_10y_2y'] = df['10y'] - df['2y']
```
