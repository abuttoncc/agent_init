# Tushare Pro Calling Conventions

## Python SDK

```python
import tushare as ts
import os

pro = ts.pro_api(os.environ.get('TUSHARE_TOKEN'))

# Method 1: Direct call
df = pro.daily(ts_code='000001.SZ', start_date='20240101', end_date='20241231')

# Method 2: Query method
df = pro.query('daily', ts_code='000001.SZ', start_date='20240101', end_date='20241231')
```

## HTTP API

```
POST http://api.tushare.pro
Content-Type: application/json

{
  "api_name": "daily",
  "token": "your_token",
  "params": {
    "ts_code": "000001.SZ",
    "start_date": "20240101",
    "end_date": "20241231"
  },
  "fields": "ts_code,trade_date,open,high,low,close,vol,amount"
}
```

Response format:
```json
{"code": 0, "msg": "", "data": {"fields": [...], "items": [[...], ...]}}
```

## Conventions

- **Date format**: YYYYMMDD (string), e.g. `'20241231'`
- **Stock codes**: ts_code with exchange suffix
  - `.SH` -- Shanghai (e.g. `600000.SH`)
  - `.SZ` -- Shenzhen (e.g. `000001.SZ`)
  - `.BJ` -- Beijing
  - `.HK` -- Hong Kong (e.g. `00700.HK`)
- **Returns**: pandas DataFrame (SDK) or JSON (HTTP)
- **Rate limits**: Varies by tier, typically 200-500 calls/min
- **Points system**: APIs require minimum accumulated points (2000-5000)
- **Token**: Environment variable `TUSHARE_TOKEN`
- **Install**: `pip install tushare --break-system-packages`
- **Scripts location**: `tmp/session/` per project convention
- **Data output**: `tmp/session/data/`

## Common Patterns

### Multiple stocks
```python
df = pro.daily(ts_code='000001.SZ,600000.SH', start_date='20240101', end_date='20241231')
```

### All stocks on a single date
```python
df = pro.daily(trade_date='20241231')
```

### Date range
```python
df = pro.daily(ts_code='000001.SZ', start_date='20240101', end_date='20241231')
```

### Select specific fields
```python
df = pro.daily(ts_code='000001.SZ', trade_date='20241231', fields='ts_code,trade_date,close,vol')
```

### Financial periods
```python
# Annual report
df = pro.income(ts_code='000001.SZ', period='20241231')
# Q3 report
df = pro.income(ts_code='000001.SZ', period='20240930')
# Q2 (mid-year) report
df = pro.income(ts_code='000001.SZ', period='20240630')
# Q1 report
df = pro.income(ts_code='000001.SZ', period='20240331')
```

### Report types
```python
# report_type values:
# '1' -- 合并报表 (consolidated, default)
# '2' -- 单季合并 (single-quarter consolidated)
# '3' -- 调整单季 (adjusted single-quarter)
# '4' -- 调整合并 (adjusted consolidated)
df = pro.income(ts_code='000001.SZ', period='20241231', report_type='1')
```
