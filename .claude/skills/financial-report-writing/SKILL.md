# 金融研究报告格式化技能

## 概述
本技能**仅负责报告的格式化输出和图表生成**，不包含数据获取和分析逻辑。

输入：结构化的分析结论和数据（由调用方准备好）
输出：符合 GB/T 9704-2012 的 Word 文档、研报级 Excel 图表

## 适用场景
- 将分析结论格式化为标准 Word 研究报告
- 生成符合中国国家标准的公文格式文档
- 创建研报级 Excel 图表（折线图、柱状图、组合图）
- 将 Matplotlib 图表嵌入 Word 文档

## 职责边界

| 本技能负责 | 本技能不负责（由其他技能完成） |
|-----------|---------------------------|
| Word 文档格式化 | 数据获取（MCP ts-data / Tushare） |
| 三线表、页码、标题层级 | 投资分析（基本面/技术面） |
| Excel 原生图表 | 评级打分、目标价计算 |
| Matplotlib 图表嵌入 Word | 行业对比、估值模型 |
| GB/T 9704-2012 排版规范 | 数据清洗和预处理 |

## 使用方式

### 1. ReportGenerator — Word 报告生成

```python
import sys
sys.path.insert(0, ".claude/skills/financial-report-writing/scripts")
from generate_report import ReportGenerator

gen = ReportGenerator()
gen.generate_report(
    title="三一重工投资研究报告",
    organization="XX证券研究所",
    data_source="Tushare",          # 数据来源标注（自动写入注脚）
    sections=[
        {
            "heading": "核心提要",
            "points": [
                "公司 ROE 为 15.2%，盈利能力较强",
                "当前 PE(TTM) 25.3 倍，估值合理",
                "技术面震荡上行，均线多头排列",
                "给予"增持"评级，目标价 35 元",
            ],
        },
        {
            "heading": "一、投资评级",
            "table": {
                "headers": ["投资建议", "目标价格", "有效期", "风险等级"],
                "rows": [["增持", "35.00元", "2027年2月", "中等风险"]],
            },
        },
        {
            "heading": "二、核心观点",
            "paragraphs": [
                "公司基本面健康，盈利能力处于行业前列。",
                "技术面呈震荡上行态势，均线多头排列。",
            ],
        },
        {
            "heading": "三、市场表现回顾",
            "paragraphs": ["近13周股价最高达 35.20 元，最低 28.50 元。"],
            "table": {
                "headers": ["日期", "开盘价", "最高价", "最低价", "收盘价", "涨跌幅"],
                "rows": [
                    ["2026-02-21", "32.10", "33.50", "31.80", "33.20", "3.43%"],
                ],
            },
        },
    ],
    output_path="tmp/session/output/report.docx",
)
```

#### sections 字段说明

每个 section 是一个 dict，可包含以下字段（按需组合）：

| 字段 | 类型 | 说明 |
|------|------|------|
| `heading` | str | 章节标题 |
| `level` | int | 标题级别，1=一级（默认），2=二级 |
| `points` | List[str] | 编号要点（自动加 1. 2. 3.） |
| `paragraphs` | List[str] | 正文段落（自动首行缩进、两端对齐） |
| `table` | dict | 三线表，含 `headers` 和 `rows` |
| `bullets` | List[str] | 项目符号列表 |

### 2. GBT9704Formatter — 底层格式化器

如需更精细的控制，直接使用格式化器：

```python
from document_formatter import GBT9704Formatter
from docx import Document

doc = Document()
fmt = GBT9704Formatter(doc)

fmt.add_cover_page("报告标题", "机构名称", "2026年2月26日")
fmt.add_heading("一、章节标题", level=1)
fmt.add_paragraph("正文内容，自动仿宋四号、首行缩进。")
fmt.add_three_line_table(
    [["数据1", "数据2"]],
    ["列名A", "列名B"],
)
fmt.add_data_source_footnote("数据来源：Tushare")
fmt.add_page_number_footer()

doc.save("output.docx")
```

### 3. chart_utils — Excel 图表生成

```python
from chart_utils import create_research_chart, create_price_volume_chart
import pandas as pd

df = pd.DataFrame({
    "date": ["2026-01", "2026-02", "2026-03"],
    "close": [10.5, 11.2, 10.8],
    "volume": [1500000, 2000000, 1800000],
})

# 折线图
create_research_chart(
    df,
    chart_type="line",
    title="股价走势",
    x_col="date",
    y_cols="close",
    y_axis_title="价格（元）",
    output_path="tmp/session/output/chart.xlsx",
)

# 股价+成交量双Y轴组合图
create_price_volume_chart(
    df,
    title="股价与成交量",
    output_path="tmp/session/output/combo.xlsx",
)
```

#### create_research_chart 参数

| 参数 | 说明 | 默认值 |
|------|------|--------|
| `df` | DataFrame 数据源 | 必填 |
| `chart_type` | "line" 或 "bar" | "line" |
| `x_col` | X轴列名 | 必填 |
| `y_cols` | Y轴列名（支持列表） | 必填 |
| `y_axis_title` | Y轴标题（建议带单位） | "" |
| `y_format` | 数字格式 | "0.00" |
| `show_last_label_only` | 仅标注最后一个值 | False |
| `add_mean_line` | 添加均值参考线 | False |
| `color_up` / `color_down` | 涨跌颜色 | FF0000 / 00B050 |
| `tick_skip` | X轴标签间隔 | 5 |

## GB/T 9704-2012 格式规范速查

### 页面设置
- 上边距 37mm / 下 35mm / 左 28mm / 右 26mm

### 字体字号
| 用途 | 字体 | 字号 |
|------|------|------|
| 标题 | 黑体 | 三号（16pt） |
| 正文 | 仿宋 | 四号（14pt） |
| 表格 | 仿宋 | 小四（12pt） |
| 注脚 | 仿宋 | 小四（12pt） |

### 段落格式
- 正文行距：固定值 28pt
- 小标题行距：固定值 25pt
- 首行缩进：2字符（28pt）
- 对齐方式：两端对齐

### 三线表
- 顶底边框：1.5pt（sz=18）
- 内部横线：0.75pt（sz=9）
- 无左右边框、无竖线

### 页脚页码
- 格式：— 1 —
- 字体：仿宋 12pt，居中

## Excel 图表规范

### A股颜色标准
- 上涨：红色（FF0000）
- 下跌：绿色（00B050）
- 中性/主线：蓝色（4472C4）

### 图表检查清单
| 检查项 | 要求 |
|--------|------|
| 坐标轴标题 | 必须包含单位 |
| 网格线 | 仅水平主网格线或隐藏 |
| X轴标签 | 设置 tick_skip 防重叠 |
| 数据来源 | 图表下方标注 |
| 数值精度 | 价格2位、百分比1-2位 |

## 图表插入 Word（Matplotlib 方案）

```python
import matplotlib.pyplot as plt
from docx.shared import Inches

plt.rcParams['font.sans-serif'] = ['SimHei', 'Microsoft YaHei']
plt.rcParams['axes.unicode_minus'] = False

fig, ax = plt.subplots(figsize=(8, 4.5))
ax.plot(dates, prices, color='#4472C4', linewidth=1.5)
ax.set_title('股价走势', fontsize=12, fontweight='bold')
ax.set_ylabel('价格（元）', fontsize=10)
ax.grid(True, alpha=0.3)

fig.savefig('chart.png', dpi=150, bbox_inches='tight')
plt.close()

doc.add_picture('chart.png', width=Inches(6))
```

## 常见问题

### 字体显示问题
必须同时设置中文字体属性：
```python
run._element.rPr.rFonts.set(qn('w:eastAsia'), font_name)
```

### 表格单元格格式
Cell 没有 `paragraph_format`，需通过 `cell.paragraphs[0].paragraph_format`。

### 行距单位
使用 `Pt()` 设置固定行距，`line_spacing_rule = 2` 表示固定值。

## 文件结构

```
scripts/
├── generate_report.py      # 报告生成器（接收结构化数据 → Word）
├── document_formatter.py   # GB/T 9704 格式化器（底层）
└── chart_utils.py          # Excel 图表工具函数
references/
└── best_practices.md       # 格式化最佳实践
```

## 依赖
- `python-docx` — Word 文档操作
- `openpyxl` — Excel 文件和图表
- `pandas` — 数据传入格式
- `matplotlib`（可选）— 图表图片嵌入 Word
