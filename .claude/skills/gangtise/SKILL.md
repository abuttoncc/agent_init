---
name: gangtise
description: |
  Use when the user asks about Chinese equity research, broker reports, analyst opinions,
  industry chain analysis, investment logic, company deep-dives, economic indicators,
  or needs to search financial knowledge bases. Triggers: "研报", "研究报告", "投资逻辑",
  "产业链", "调研提纲", "晨报", "指标查询", "GDP", "销量", "深度研究", "gangtise".
allowed-tools:
  - Bash
  - WebFetch
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - AskUserQuestion
user-invocable: true
---

# Gangtise Ultra — 投研知识库 & AI Agent API

港股/A股研报知识库智能检索 + AI 深度研究 Agent + 经济指标查询。

## Configuration

**Base URL:** `http://39.96.218.64:8009`

**Auth:** All requests require `Authorization: Bearer <token>` header.

### Token Resolution (MUST do before first API call)

Run this bash snippet to resolve the token. It checks multiple sources automatically:

```bash
# 1. Environment variable
TOKEN="${GANGTISE_TOKEN:-}"

# 2. .env in current directory
if [ -z "$TOKEN" ] && [ -f .env ]; then
  TOKEN=$(grep -E '^GANGTISE_TOKEN=' .env | cut -d= -f2- | tr -d '"'"'"' ')
fi

# 3. ~/.env global dotenv
if [ -z "$TOKEN" ] && [ -f ~/.env ]; then
  TOKEN=$(grep -E '^GANGTISE_TOKEN=' ~/.env | cut -d= -f2- | tr -d '"'"'"' ')
fi
```

**If `$TOKEN` is still empty after the above**, use `AskUserQuestion` to ask:
> "Gangtise API 需要 Bearer Token 才能调用。请提供 token，或者告诉我 .env 文件路径。"

Once obtained, export it for the rest of the session:
```bash
export GANGTISE_TOKEN="<the token>"
```

### Verify Connection

```bash
curl -s -o /dev/null -w "%{http_code}" http://39.96.218.64:8009/api/docs \
  -H "Authorization: Bearer $TOKEN"
# 200 = OK, 401 = token invalid
```

## API Quick Reference

| Endpoint | Method | Response | Use Case |
|----------|--------|----------|----------|
| `/api/search` | POST | JSON | 搜索研报/公告/纪要 |
| `/api/search/batch` | POST | JSON | 批量搜索多个主题 |
| `/api/ai/chat` | POST | SSE | AI 深度研究（投资逻辑/产业链/调研提纲） |
| `/api/ai/chat/create-group` | GET | JSON | 创建会话（用于多轮对话） |
| `/api/ai/indicator` | POST | JSON | 经济/行业指标数据查询 |
| `/api/docs` | GET | JSON | 完整接口文档 |

## 1. Knowledge Search — `/api/search`

Search broker reports, analyst opinions, company filings, meeting minutes.

```bash
curl -s http://39.96.218.64:8009/api/search \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "比亚迪",
    "top": 10,
    "time_range": "1m",
    "resource_types": ["BROKER_REPORT"]
  }'
```

**Parameters:**
- `query` (required): Search keywords
- `top`: 1-20, default 10
- `time_range`: `1d` `1w` `1m` `1q` `6m` `1y` `all` (default `1m`)
- `resource_types`: Filter array, options:
  - `BROKER_REPORT` — 券商研报
  - `INTERNAL_REPORT` — 内部研报
  - `ANALYST_OPINION` — 首席观点
  - `COMPANY_NOTICE` — 公司公告
  - `MEETING_MINUTES` — 会议纪要
  - `SURVEY_NOTES` — 调研纪要
  - `WEB_RESOURCE` — 网络资源
  - `INDUSTRY_OFFICIAL` — 产业公众号

**Response:** `{query, total, time_range, results: [{title, source, date, company, content, source_url, source_id}]}`

Each result includes `source_url` for citation. Use `[[n]](source_url)` format in replies.

## 2. Batch Search — `/api/search/batch`

Search multiple topics at once (max 5).

```bash
curl -s http://39.96.218.64:8009/api/search/batch \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "queries": ["比亚迪", "宁德时代", "特斯拉"],
    "top": 5,
    "time_range": "1m"
  }'
```

**Response:** `{total_queries, time_range, responses: [{query, total, results}]}`

## 3. Agent Deep Research — `/api/ai/chat`

AI-powered deep analysis with multi-round thinking. Returns SSE stream.

**Important:** This is an SSE endpoint. Use `curl -N` for streaming. Response can take 30-120 seconds.

```bash
curl -s -N http://39.96.218.64:8009/api/ai/chat \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "比亚迪的投资逻辑",
    "forced_agent": "investment_logic"
  }'
```

**Parameters:**
- `query` (required): Research question
- `forced_agent`: Force a specific agent (default: auto-select)
  - `searcher` — 数据搜索
  - `investigation_outline` — 调研提纲
  - `industry_expert` — 产业链分析
  - `theme_daily_report` — 主题晨报
  - `investment_logic` — 投资逻辑
- `group_id`: Pass to maintain conversation context
- `include_search_types`: Filter sources `[10,20,40,50,60,70,80,90]`
- `web_enable`: Enable web search (default false)

**SSE event format:**
```
data:{"phase":"think","round":1,"title":"识别问题","result":{"delta":"..."}}
data:{"phase":"answer","round":5,"title":"回答","result":{"delta":"..."}}
```

- `phase=think`: Thinking steps (multiple rounds)
- `phase=answer`: Final answer (concatenate all deltas)

**To collect full answer from SSE:**

```bash
curl -s -N http://39.96.218.64:8009/api/ai/chat \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query":"比亚迪的投资逻辑","forced_agent":"investment_logic"}' \
  2>/dev/null | grep '^data:' | while read -r line; do
    echo "${line#data:}"
  done | python3 -c "
import sys, json
thinking, answer = [], []
for line in sys.stdin:
    line = line.strip()
    if not line: continue
    try:
        e = json.loads(line)
        delta = e.get('result',{}).get('delta','')
        if e.get('phase') == 'answer':
            answer.append(delta)
        elif e.get('phase') == 'think':
            thinking.append(delta)
    except: pass
print(json.dumps({'thinking':''.join(thinking),'answer':''.join(answer)}, ensure_ascii=False, indent=2))
"
```

## 4. Create Chat Group — `/api/ai/chat/create-group`

Create a session for multi-turn agent conversations.

```bash
curl -s http://39.96.218.64:8009/api/ai/chat/create-group \
  -H "Authorization: Bearer $TOKEN"
```

**Response:** `{"group_id": 12345}`

Pass `group_id` to subsequent `/api/ai/chat` calls for context continuity.

## 5. Indicator Query — `/api/ai/indicator`

Query macroeconomic data, industry statistics, company operating metrics.

```bash
curl -s http://39.96.218.64:8009/api/ai/indicator \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"query": "比亚迪仰望销量"}'
```

**Response:** `{reasoning_content: "...", content: "..."}`

- `reasoning_content`: Data retrieval process
- `content`: Final result (often markdown tables)

**Good queries:** Company sales, GDP, CPI, PMI, battery installations, EV penetration, commodity prices.

## Workflow Patterns

### Pattern A: Quick Research
User asks about a company or topic → Use `/api/search` with relevant keywords → Synthesize results with citations.

### Pattern B: Deep Analysis
User wants investment logic, industry chain, or comprehensive analysis → Use `/api/ai/chat` with appropriate `forced_agent` → Stream and present the answer.

### Pattern C: Data Query
User asks for specific metrics (sales, GDP, production) → Use `/api/ai/indicator` → Present the data table.

### Pattern D: Comparative Research
User wants to compare multiple companies → Use `/api/search/batch` with company names → Cross-reference results.

### Pattern E: Multi-Turn Research
Create group → ask initial question → follow up with `group_id` for contextual conversation.

## Citation Format

When presenting search results, use this citation format:

**In text:** `比亚迪1月出口增45% [[1]](source_url)`

**At end:**
```
## 参考来源
[1] 券商研报《标题》(日期) [查看原文](source_url)
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Forgetting Bearer token | Always include `Authorization: Bearer $TOKEN` |
| Using WebFetch for SSE | Use `Bash` + `curl -N` for `/api/ai/chat` |
| Setting short timeout for agent_chat | Allow 120s+ for deep research |
| Not passing `group_id` for follow-ups | Create group first, reuse ID |
| Searching with overly broad queries | Be specific: company name + topic |
