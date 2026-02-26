---
name: moat-analysis
description: 竞争壁垒分析技能，基于巴菲特投资框架，从收入结构、财务数据、竞争环境三个维度分析公司护城河。输出 Excel 数据记录和 DOCX 分析报告。触发词：竞争壁垒、护城河、moat、竞争优势分析、行业对比、财务分析。
---

# 竞争壁垒分析技能 (Moat Analysis)

## 概述

基于巴菲特"护城河"投资理念，结合 tushare-data MCP 工具，系统性分析上市公司的竞争壁垒。通过收入结构拆解、财务数据对比、竞争环境判断，识别真正的护城河并预测未来发展。

---

## ⚠️ 关键：数据时效性 (CRITICAL)

**数据时效性直接决定分析结论的有效性。必须根据当前日期动态计算应查询的时间段。**

### 动态日期计算规则

```javascript
// 获取当前日期
const today = new Date();  // 例如: 2026-02-03
const currentYear = today.getFullYear();  // 2026
const currentMonth = today.getMonth() + 1;  // 2 (1-12)

// 判断最新可用财报
function getLatestAvailablePeriods() {
  const periods = [];

  // 年报披露时间: 次年 1-4 月 (4月30日前必须披露完毕)
  // 一季报: 4月30日前
  // 中报: 8月31日前
  // 三季报: 10月31日前

  let latestAnnualYear, latestQuarterPeriod;

  if (currentMonth >= 5) {
    // 5月及以后: 上一年年报已披露
    latestAnnualYear = currentYear - 1;
  } else {
    // 1-4月: 上上年年报确定可用，上一年年报可能未披露完
    latestAnnualYear = currentYear - 2;
  }

  // 确定最新季报
  if (currentMonth >= 11) {
    latestQuarterPeriod = `${currentYear}0930`;  // 三季报
  } else if (currentMonth >= 9) {
    latestQuarterPeriod = `${currentYear}0630`;  // 中报
  } else if (currentMonth >= 5) {
    latestQuarterPeriod = `${currentYear}0331`;  // 一季报
  } else {
    latestQuarterPeriod = `${currentYear - 1}0930`;  // 上年三季报
  }

  // 生成年报列表 (最近4年)
  for (let i = 0; i < 4; i++) {
    periods.push(`${latestAnnualYear - i}1231`);
  }

  return {
    annualPeriods: periods.reverse(),  // 从早到晚排序
    latestQuarter: latestQuarterPeriod,
    dataAsOf: `${latestAnnualYear}年报 + ${latestQuarterPeriod.slice(0,4)}年${latestQuarterPeriod.slice(4,6) === '09' ? 'Q3' : latestQuarterPeriod.slice(4,6) === '06' ? 'Q2' : 'Q1'}`
  };
}

// 示例输出 (假设今天是 2026-02-03):
// {
//   annualPeriods: ["20211231", "20221231", "20231231", "20241231"],
//   latestQuarter: "20250930",
//   dataAsOf: "2024年报 + 2025年Q3"
// }
```

### 执行前必检清单

```markdown
□ 步骤1: 确认当前日期 → today = new Date()
□ 步骤2: 计算最新可用年报年份 → latestAnnualYear
□ 步骤3: 计算最新可用季报期间 → latestQuarterPeriod
□ 步骤4: 生成查询期间列表 (动态计算，禁止硬编码)
□ 步骤5: 查询最新季报数据 (获取最及时的业绩变化)
□ 步骤6: 验证 get_stock_data 返回的 end_date，确认数据时效性
```

### ❌ 禁止事项

```
禁止: 使用硬编码的年份，如 periods = ["20201231", "20211231", "20221231", "20231231"]
原因: 这是2024年初的写法，到2026年已过时2年，会遗漏2024、2025年数据

禁止: 忽略 get_stock_data 返回的 end_date 信号
原因: 如果返回 end_date: "20250930"，说明有更新数据可查询

禁止: 用旧数据得出"周期底部"等结论而不验证最新情况
原因: 业绩可能已大幅反转，旧结论会严重误导
```

### ✅ 正确做法示例

```javascript
// 正确: 动态计算查询期间
const { annualPeriods, latestQuarter } = getLatestAvailablePeriods();

// 查询年报数据
for (const period of annualPeriods) {
  await get_income_statement(stock_code, period);
  await get_financial_indicator(stock_code, period);
}

// 查询最新季报 (关键!)
await get_income_statement(stock_code, latestQuarter);
await get_financial_indicator(stock_code, latestQuarter);
```

---

## 核心输出

| 输出文件 | 格式 | 内容 |
|---------|------|------|
| **数据记录** | `.xlsx` | 原始数据提取日志、财务指标、同行对比 |
| **分析报告** | `.docx` | 完整的竞争壁垒分析报告 |

## 分析框架 (四步法)

### 第一步：收入结构分析 (从收入入手)

#### 1.1 获取基础数据

```
使用工具获取目标公司数据：
1. mcp__tushare_data__search_financial_entity - 搜索股票代码
2. mcp__tushare_data__get_stock_data - 获取公司基础信息
3. mcp__tushare_data__get_income_statement - 获取利润表 (3-5年)
4. mcp__tushare_data__get_financial_metrics - 获取财务指标增长
```

#### 1.2 收入拆解维度

| 维度 | 分析内容 | 数据来源 |
|-----|---------|---------|
| **收入占比** | 各业务板块收入占比变化 | 利润表 + 年报披露 |
| **量** | 销量/产量/用户数增长 | 行业数据 + 公司披露 |
| **价** | 单价/ASP/ARPU变化 | 收入 ÷ 量 |
| **行业对比** | 公司 vs 行业平均 | analyze_sector |

### 第二步：竞争壁垒识别 (巴菲特框架)

#### 2.1 五种竞争壁垒类型

| 壁垒类型 | 财务特征 | 检测指标 |
|---------|---------|---------|
| **品牌溢价** | 高毛利率、定价权 | 毛利率 > 行业均值 + 10% |
| **规模优势** | 低成本、高周转 | 营收增速 > 成本增速 |
| **网络效应** | 边际成本递减 | 用户增长 vs 成本增长 |
| **转换成本** | 客户粘性高 | 客户留存率、复购率 |
| **特许经营** | 稳定现金流 | 经营现金流/净利润 > 1 |

#### 2.2 获取同行数据对比

```
分析流程：
1. mcp__tushare_data__get_sector_top_stocks - 获取同行业龙头股
2. 对每家公司调用：
   - get_financial_indicator (ROE、毛利率、净利率)
   - get_income_statement (营收、净利润)
   - get_balance_sheet (资产负债率)
   - get_cashflow_statement (经营现金流)
3. 横向对比生成表格
```

### 第三步：竞争环境判断 (恶劣 vs 改善)

#### 3.1 竞争环境评估框架

```markdown
竞争环境评分卡 (每项0-2分，总分10分)：

□ 毛利率趋势：上升(2) / 稳定(1) / 下降(0)
□ ROE趋势：上升(2) / 稳定(1) / 下降(0)
□ 行业集中度：提升(2) / 稳定(1) / 分散(0)
□ 资本开支强度：下降(2) / 稳定(1) / 上升(0)
□ 龙头vs二线盈利差：扩大(2) / 稳定(1) / 收窄(0)

总分评估：
- 8-10分：竞争环境显著改善 (上推估值)
- 5-7分：竞争环境稳定
- 0-4分：竞争环境恶劣 (下推估值)
```

### 第四步：未来预测 (3年展望)

基于竞争壁垒和环境判断，预测未来3年的营收、利润增速。

---

## 输出工作流程

### 工作流程概览

```
1. 数据获取 (tushare-data MCP)
      ↓
2. 数据记录到 Excel (实时记录)
      ↓
3. 分析与判断
      ↓
4. 生成 DOCX 报告
      ↓
5. 输出文件给用户
```

---

## Excel 数据记录 (Data Log)

### 文件命名

```
{公司名称}_数据记录_{YYYYMMDD}.xlsx
示例: 山西汾酒_数据记录_20260203.xlsx
```

### Sheet 结构

| Sheet名称 | 内容 | 说明 |
|----------|------|------|
| **基础信息** | 公司概况、行业、市值等 | 第一步获取的数据 |
| **历史财务** | 3-5年财务指标趋势 | 营收、净利润、毛利率、ROE等 |
| **同行对比** | 行业内公司财务对比 | 横向对比表格 |
| **现金流** | 现金流量表数据 | 经营、投资、筹资现金流 |
| **数据日志** | API调用记录 | 时间戳、工具名、参数、结果摘要 |

### Excel 生成代码模板

```python
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side
from datetime import datetime

# 样式定义
header_font = Font(bold=True, color='FFFFFF', size=11)
header_fill = PatternFill('solid', fgColor='1F4E79')
border = Border(
    left=Side(style='thin', color='CCCCCC'),
    right=Side(style='thin', color='CCCCCC'),
    top=Side(style='thin', color='CCCCCC'),
    bottom=Side(style='thin', color='CCCCCC')
)

wb = Workbook()

# Sheet 1: 基础信息
ws1 = wb.active
ws1.title = "基础信息"
ws1.append(["项目", "数值"])
ws1.append(["公司名称", company_name])
ws1.append(["股票代码", stock_code])
ws1.append(["所属行业", industry])
ws1.append(["当前市值(亿)", market_cap])
ws1.append(["PE TTM", pe_ttm])
ws1.append(["PB", pb])
ws1.append(["数据日期", datetime.now().strftime("%Y-%m-%d")])

# Sheet 2: 历史财务
ws2 = wb.create_sheet("历史财务")
ws2.append(["年份", "营收(亿)", "净利润(亿)", "毛利率%", "净利率%", "ROE%"])
for year_data in historical_data:
    ws2.append([year_data['year'], year_data['revenue'], year_data['profit'],
                year_data['gross_margin'], year_data['net_margin'], year_data['roe']])

# Sheet 3: 同行对比
ws3 = wb.create_sheet("同行对比")
ws3.append(["公司", "毛利率%", "净利率%", "ROE%", "资产负债率%", "PE", "PB"])
for peer in peers_data:
    ws3.append([peer['name'], peer['gross_margin'], peer['net_margin'],
                peer['roe'], peer['debt_ratio'], peer['pe'], peer['pb']])

# Sheet 4: 现金流
ws4 = wb.create_sheet("现金流")
ws4.append(["年份", "经营现金流(亿)", "投资现金流(亿)", "筹资现金流(亿)", "现金流/净利润"])
for cf in cashflow_data:
    ws4.append([cf['year'], cf['operating'], cf['investing'], cf['financing'], cf['ratio']])

# Sheet 5: 数据日志
ws5 = wb.create_sheet("数据日志")
ws5.append(["时间", "工具", "参数", "结果摘要"])
for log in api_logs:
    ws5.append([log['time'], log['tool'], log['params'], log['summary']])

# 应用样式到所有 sheet
for ws in wb.worksheets:
    for cell in ws[1]:
        cell.font = header_font
        cell.fill = header_fill
        cell.alignment = Alignment(horizontal='center')
    for row in ws.iter_rows():
        for cell in row:
            cell.border = border
    # 自动列宽
    for col in ws.columns:
        max_length = max(len(str(cell.value or '')) for cell in col)
        ws.column_dimensions[col[0].column_letter].width = min(max_length + 2, 30)

wb.save(f"{company_name}_数据记录_{datetime.now().strftime('%Y%m%d')}.xlsx")
```

### 数据日志记录格式

每次调用 tushare-data 工具时，记录以下信息：

```python
api_logs.append({
    'time': datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
    'tool': 'get_financial_indicator',
    'params': f"stock_code={stock_code}, period={period}",
    'summary': f"ROE={roe}%, 毛利率={gross_margin}%"
})
```

---

## DOCX 报告生成

### 文件命名

```
{公司名称}_竞争壁垒分析报告_{YYYYMMDD}.docx
示例: 山西汾酒_竞争壁垒分析报告_20260203.docx
```

### 报告结构

```
封面/标题
├── 报告日期: YYYY-MM-DD
└── 数据截止: XXXX年报 + XXXX年QX  ← 必须注明!

一、公司概况
二、收入结构分析 (含最新季报)
   2.1 核心财务数据趋势 (4年年报 + 最新季报)
   2.2 增长率分析 (重点关注最新季报同比变化)
三、同行财务对比
   3.1 盈利能力对比
   3.2 关键发现
四、竞争壁垒识别
   4.1 五种壁垒检测
   4.2 核心壁垒详解
   4.3 壁垒强度评分
五、竞争环境判断
   5.1 环境评估评分卡
   5.2 竞争改善信号
六、未来3年预测
   6.1 行业假设
   6.2 公司预测
   6.3 预测依据
七、风险提示
八、结论与总评
```

### DOCX 生成代码模板

**必须先阅读**: `~/.claude/plugins/marketplaces/anthropic-agent-skills/skills/docx/docx-js.md`

```javascript
const { Document, Packer, Paragraph, TextRun, Table, TableRow, TableCell,
        AlignmentType, HeadingLevel, BorderStyle, WidthType, LevelFormat,
        Header, Footer, PageNumber, ShadingType, VerticalAlign } = require('docx');
const fs = require('fs');

// 表格边框配置
const tableBorder = { style: BorderStyle.SINGLE, size: 1, color: "CCCCCC" };
const cellBorders = { top: tableBorder, bottom: tableBorder, left: tableBorder, right: tableBorder };

// 辅助函数: 创建单元格
const createCell = (text, opts = {}) => new TableCell({
  borders: cellBorders,
  width: { size: opts.width || 2000, type: WidthType.DXA },
  shading: opts.header ? { fill: "1F4E79", type: ShadingType.CLEAR } : undefined,
  verticalAlign: VerticalAlign.CENTER,
  children: [new Paragraph({
    alignment: opts.center ? AlignmentType.CENTER : AlignmentType.LEFT,
    children: [new TextRun({
      text, bold: opts.bold || opts.header,
      color: opts.header ? "FFFFFF" : "000000",
      size: opts.size || 20
    })]
  })]
});

// 辅助函数: 创建表格行
const createTableRow = (cells, opts = {}) => new TableRow({
  tableHeader: opts.header,
  children: cells.map((text, i) => createCell(text, {
    ...opts,
    width: opts.widths ? opts.widths[i] : 1800,
    center: opts.center || (i > 0 && !opts.leftAlign)
  }))
});

// 辅助函数: 创建完整表格
const createTable = (headers, rows, widths) => new Table({
  columnWidths: widths,
  rows: [
    createTableRow(headers, { header: true, widths }),
    ...rows.map(row => createTableRow(row, { widths }))
  ]
});

// 辅助函数: 标题段落
const h1 = (text) => new Paragraph({
  heading: HeadingLevel.HEADING_1,
  spacing: { before: 400, after: 200 },
  children: [new TextRun(text)]
});

const h2 = (text) => new Paragraph({
  heading: HeadingLevel.HEADING_2,
  spacing: { before: 300, after: 150 },
  children: [new TextRun(text)]
});

// 辅助函数: 普通段落
const para = (text, opts = {}) => new Paragraph({
  spacing: { after: 120 },
  alignment: opts.center ? AlignmentType.CENTER : AlignmentType.LEFT,
  children: [new TextRun({
    text, bold: opts.bold,
    size: opts.size || 22,
    color: opts.color || "000000"
  })]
});

// 创建文档
const doc = new Document({
  styles: {
    default: { document: { run: { font: "Arial", size: 22 } } },
    paragraphStyles: [
      { id: "Title", name: "Title", basedOn: "Normal",
        run: { size: 48, bold: true, color: "1F4E79", font: "Arial" },
        paragraph: { spacing: { before: 0, after: 200 }, alignment: AlignmentType.CENTER } },
      { id: "Heading1", name: "Heading 1", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 32, bold: true, color: "1F4E79", font: "Arial" },
        paragraph: { spacing: { before: 400, after: 200 }, outlineLevel: 0 } },
      { id: "Heading2", name: "Heading 2", basedOn: "Normal", next: "Normal", quickFormat: true,
        run: { size: 26, bold: true, color: "2E75B6", font: "Arial" },
        paragraph: { spacing: { before: 300, after: 150 }, outlineLevel: 1 } }
    ]
  },
  numbering: {
    config: [{
      reference: "bullet-list",
      levels: [{ level: 0, format: LevelFormat.BULLET, text: "•", alignment: AlignmentType.LEFT,
        style: { paragraph: { indent: { left: 720, hanging: 360 } } } }]
    }]
  },
  sections: [{
    properties: { page: { margin: { top: 1440, right: 1440, bottom: 1440, left: 1440 } } },
    headers: {
      default: new Header({
        children: [new Paragraph({
          alignment: AlignmentType.RIGHT,
          children: [new TextRun({ text: "竞争壁垒分析报告", size: 18, color: "808080" })]
        })]
      })
    },
    footers: {
      default: new Footer({
        children: [new Paragraph({
          alignment: AlignmentType.CENTER,
          children: [
            new TextRun({ text: "第 ", size: 18 }),
            new TextRun({ children: [PageNumber.CURRENT], size: 18 }),
            new TextRun({ text: " 页", size: 18 })
          ]
        })]
      })
    },
    children: [
      // === 标题 ===
      new Paragraph({ heading: HeadingLevel.TITLE, children: [new TextRun(`${companyName} 竞争壁垒分析报告`)] }),
      para(""),
      // ... 添加报告内容 ...

      // === 一、公司概况 ===
      h1("一、公司概况"),
      para(companyOverview),
      createTable(
        ["指标", "数值"],
        [["股票代码", stockCode], ["行业", industry], ["市值", marketCap], ["PE", pe], ["PB", pb]],
        [4680, 4680]
      ),

      // === 二、收入结构分析 ===
      h1("二、收入结构分析"),
      h2("2.1 核心财务数据趋势"),
      createTable(
        ["年份", "营收(亿)", "净利润(亿)", "毛利率", "净利率", "ROE"],
        historicalData.map(d => [d.year, d.revenue, d.profit, d.grossMargin, d.netMargin, d.roe]),
        [1560, 1560, 1560, 1560, 1560, 1560]
      ),

      // === 三、同行对比 ===
      h1("三、同行财务对比"),
      createTable(
        ["公司", "毛利率", "净利率", "ROE", "行业地位"],
        peersData.map(p => [p.name, p.grossMargin, p.netMargin, p.roe, p.position]),
        [1872, 1872, 1872, 1872, 1872]
      ),

      // === 四、竞争壁垒识别 ===
      h1("四、竞争壁垒识别"),
      h2("4.1 五种壁垒检测"),
      createTable(
        ["壁垒类型", "评分", "证据分析"],
        moatAnalysis.map(m => [m.type, m.score, m.evidence]),
        [2340, 1560, 5460]
      ),

      // === 五、竞争环境判断 ===
      h1("五、竞争环境判断"),
      createTable(
        ["维度", "评分", "说明"],
        environmentScores.map(e => [e.dimension, e.score, e.description]),
        [3120, 1560, 4680]
      ),
      para(`总分: ${totalScore}/10 → ${environmentConclusion}`, { bold: true, color: "1F4E79" }),

      // === 六、未来预测 ===
      h1("六、未来3年预测"),
      createTable(
        ["年份", "营收增速", "净利润增速", "净利率", "份额变化"],
        predictions.map(p => [p.year, p.revenueGrowth, p.profitGrowth, p.netMargin, p.shareChange]),
        [1872, 1872, 1872, 1872, 1872]
      ),

      // === 七、风险提示 ===
      h1("七、风险提示"),
      ...risks.map(r => new Paragraph({
        numbering: { reference: "bullet-list", level: 0 },
        children: [new TextRun(r)]
      })),

      // === 八、结论 ===
      h1("八、结论与总评"),
      createTable(
        ["维度", "评估结果"],
        [["护城河类型", moatType], ["护城河宽度", moatWidth], ["护城河趋势", moatTrend], ["竞争环境", environment]],
        [4680, 4680]
      ),
      para(""),
      para(finalConclusion, { bold: true, color: "1F4E79" }),
      para(""),
      para("本分析基于公开财务数据，仅供参考，不构成投资建议", { center: true, color: "808080", size: 18 })
    ]
  }]
});

// 保存文档
Packer.toBuffer(doc).then(buffer => {
  fs.writeFileSync(`${companyName}_竞争壁垒分析报告_${date}.docx`, buffer);
});
```

---

## 完整执行流程

### 步骤1：初始化与数据获取

```
用户输入: 公司名称或股票代码

执行:
1. search_financial_entity 确认股票代码
2. 初始化 api_logs = []
3. 并行获取并记录:
   - get_stock_data → 记录到 api_logs
   - get_sector_top_stocks → 记录到 api_logs
```

### 步骤2：历史财务数据 (动态日期计算)

```javascript
// ⚠️ 关键: 必须动态计算，禁止硬编码年份！
const today = new Date();
const currentYear = today.getFullYear();
const currentMonth = today.getMonth() + 1;

// 计算最新可用年报年份
const latestAnnualYear = currentMonth >= 5 ? currentYear - 1 : currentYear - 2;

// 生成年报期间 (最近4年)
const annualPeriods = [
  `${latestAnnualYear - 3}1231`,
  `${latestAnnualYear - 2}1231`,
  `${latestAnnualYear - 1}1231`,
  `${latestAnnualYear}1231`
];

// 计算最新季报期间
let latestQuarter;
if (currentMonth >= 11) latestQuarter = `${currentYear}0930`;
else if (currentMonth >= 9) latestQuarter = `${currentYear}0630`;
else if (currentMonth >= 5) latestQuarter = `${currentYear}0331`;
else latestQuarter = `${currentYear - 1}0930`;

// 示例: 2026-02-03 → annualPeriods = ["20211231", "20221231", "20231231", "20241231"]
//                    latestQuarter = "20250930"
```

```
执行查询:

1. 查询年报数据 (4年)
for period in annualPeriods:
    result = get_income_statement(stock_code, period)
    记录到 api_logs
    result = get_financial_indicator(stock_code, period)
    记录到 api_logs

2. 查询最新季报数据 (关键!)
result = get_income_statement(stock_code, latestQuarter)
记录到 api_logs
result = get_financial_indicator(stock_code, latestQuarter)
记录到 api_logs  // 捕捉最新业绩变化

整理为 historical_data 列表 (包含最新季报)
```

### 步骤3：同行对比数据

```
peers = get_sector_top_stocks(行业, limit=10)

for peer in peers[:5]:  # 取前5家
    result = get_financial_indicator(peer)
    记录到 api_logs

整理为 peers_data 列表
```

### 步骤4：生成 Excel 数据记录

```python
# 生成 Excel 文件
wb = Workbook()
# ... 按上述模板填充数据 ...
wb.save(f"{company_name}_数据记录_{date}.xlsx")
```

### 步骤5：分析与判断

```
基于数据进行:
1. 收入结构分析
2. 竞争壁垒识别 (五种类型)
3. 竞争环境评分 (10分制)
4. 未来3年预测

整理分析结果
```

### 步骤6：生成 DOCX 报告

```javascript
// 生成 DOCX 文件
const doc = new Document({ ... });
Packer.toBuffer(doc).then(buffer => {
  fs.writeFileSync(`${companyName}_竞争壁垒分析报告_${date}.docx`, buffer);
});
```

### 步骤7：输出给用户

```
输出文件:
1. {公司名称}_数据记录_{日期}.xlsx
2. {公司名称}_竞争壁垒分析报告_{日期}.docx

告知用户文件路径
```

---

## 工具调用参考

### tushare-data MCP 工具

| 工具 | 用途 | 参数 |
|-----|------|-----|
| `search_financial_entity` | 搜索股票 | keyword |
| `get_stock_data` | 综合数据 | stock_code |
| `get_income_statement` | 利润表 | stock_code, period |
| `get_balance_sheet` | 资产负债表 | stock_code, period |
| `get_cashflow_statement` | 现金流量表 | stock_code, period |
| `get_financial_indicator` | 财务指标 | stock_code, period |
| `get_sector_top_stocks` | 行业龙头 | sector_name, limit |

### 时间周期参考

```
财报期末格式：
- 年报期末：YYYY1231
- 中报期末：YYYY0630
- 一季报：  YYYY0331
- 三季报：  YYYY0930

财报披露时间 (A股规则)：
- 年报：次年 4月30日 前
- 一季报：4月30日 前
- 中报：8月31日 前
- 三季报：10月31日 前
```

**⚠️ 禁止硬编码年份！必须动态计算：**

```javascript
// 根据当前日期计算应查询的期间
const today = new Date();
const year = today.getFullYear();
const month = today.getMonth() + 1;

// 最新可用年报
const latestAnnual = month >= 5 ? year - 1 : year - 2;

// 最新可用季报
const latestQuarter = month >= 11 ? `${year}0930` :
                      month >= 9  ? `${year}0630` :
                      month >= 5  ? `${year}0331` :
                                    `${year-1}0930`;

// 生成4年年报 + 最新季报
const periods = [
  `${latestAnnual-3}1231`, `${latestAnnual-2}1231`,
  `${latestAnnual-1}1231`, `${latestAnnual}1231`,
  latestQuarter  // 关键: 必须包含最新季报!
];
```

---

## 重要原则

### DO (必须做)
- **⚠️ 动态计算查询日期，获取最新可用数据 (年报 + 最新季报)**
- **⚠️ 验证 get_stock_data 返回的 end_date，确认数据时效性**
- 使用真实财务数据，不臆测
- 实时记录每次 API 调用到 Excel
- 与同行对比，而非绝对值判断
- 明确假设和风险
- 同时输出 Excel 和 DOCX 两个文件
- 报告中注明数据截止日期 (如 "数据截至: 2024年报 + 2025年Q3")

### DON'T (禁止做)
- **❌ 禁止硬编码年份 (如 "20201231", "20231231")**
- **❌ 禁止忽略最新季报数据**
- **❌ 禁止用过时数据得出周期判断结论而不验证最新情况**
- 不基于单一指标下结论
- 不忽视竞争环境变化
- 不做超过3年的远期预测
- 不提供具体买卖建议
- 不跳过数据记录步骤

---

## 语言说明

- 使用中文进行分析和报告
- 专业术语保留英文简称 (如 ROE, CAGR, PE)
- Excel 和 DOCX 文件名使用中文
