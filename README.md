# SJTU-PPT (pptx-generator)

基于 PPTX 模板 + 结构化 JSON 内容定义，自动生成排版美观、风格统一且可二次编辑的演示文稿。可作为扣子（Coze）技能安装使用，也可直接命令行运行。

内置 8 套上海交通大学（SJTU）主题模板，覆盖封面 / 目录 / 内页 / 过渡页 / 封底五种布局。

## 功能特性

- **模板驱动**：主题样式（字体、颜色、大小）完全继承模板母版，输出与模板设计风格一致；支持用户自带 `.pptx` 模板
- **8 套内置 SJTU 模板**：basic（默认）、wine、130、crimson、origin、star、blue、global
- **五种布局**：封面 `cover`、目录 `toc`、内页 `content`、过渡页 `section`、封底 `end`
- **页码控制**：顶层 / 单页 `page_numbers` 开关，基于 PowerPoint 原生 SLIDE_NUMBER 占位符，默认仅内页显示
- **双栏排版**：`left` / `right` 字段，模板含"左/右"占位符时自动填充
- **多层级要点**：`{"text": "...", "level": 1}` 嵌套 bullet
- **图片填充**：SJTU-origin 模板支持封面 / 过渡页图片占位符；图片缺失时 warning 不中断
- **中文 JSON 容错**：自动将常见全角标点（引号、冒号、逗号、括号等）转为半角，手写 JSON 不易解析失败
- **回归测试**：`smoke_test.py` 对全部内置模板跑 33 项检查

## 目录结构

```
pptx-generator/
├── SKILL.md                      # 技能说明（Coze 技能入口）
├── assets/                       # 8 套内置 .pptx 模板
│   ├── SJTU-basic.pptx           # 默认模板（蓝灰）
│   ├── SJTU-wine.pptx            # 酒红主题
│   ├── SJTU-130.pptx             # 红色主题
│   ├── SJTU-crimson.pptx         # 深红主题
│   ├── SJTU-origin.pptx          # 经典主题（支持封面/过渡页图片）
│   ├── SJTU-star.pptx            # 星图主题
│   ├── SJTU-blue.pptx            # 蓝色主题
│   └── SJTU-global.pptx          # 蓝色商务主题
├── references/
│   └── content-format.md         # 内容 JSON 完整格式规范
└── scripts/
    ├── generate_pptx.py          # 主生成脚本
    ├── list_templates.py         # 模板索引（布局/占位符信息）
    └── smoke_test.py             # 回归测试（33 项检查）
```

## 快速开始

### 依赖

```bash
pip install python-pptx==0.6.23
```

### 命令行使用

```bash
# 使用内置默认模板（SJTU-basic）
python scripts/generate_pptx.py --content ./content.json --output ./output.pptx

# 指定模板
python scripts/generate_pptx.py --template ./assets/SJTU-wine.pptx --content ./content.json --output ./output.pptx

# 查看所有内置模板的布局与占位符
python scripts/list_templates.py --assets-dir assets

# 生成一个基础空白模板
python scripts/generate_pptx.py --create-template ./my-template.pptx
```

### 作为 Coze 技能安装

将 `pptx-generator/` 目录打包为 `.skill`（zip 格式），在扣子「我的技能」中导入即可；导入后 Agent 可根据文字大纲自动编写 JSON 并生成 PPT。

## 内容 JSON 示例

```json
{
  "title": "年度总结",
  "subtitle": "2026",
  "date": "2026年9月",
  "author": "张三",
  "page_numbers": true,
  "slides": [
    { "layout": "toc", "title": "目录", "content": ["工作回顾", "数据亮点", "明年规划"] },
    { "layout": "section", "title": "工作回顾" },
    {
      "layout": "content",
      "title": "数据亮点",
      "content": [
        "DAU 增长 120%",
        { "text": "其中移动端占比 78%", "level": 1 },
        "NPS 提升至 45"
      ]
    },
    {
      "layout": "content",
      "title": "Timeline & Owners",
      "left": ["Q1: 设计冻结", "Q2: Beta"],
      "right": ["PM: Alice", "QA: Carol"]
    },
    { "layout": "end", "title": "谢谢", "subtitle": "Q&A" }
  ]
}
```

完整字段说明（页码矩阵、图片字段、布局匹配规则等）见 [references/content-format.md](pptx-generator/references/content-format.md)。

## 运行测试

```bash
python scripts/smoke_test.py
# PASS: 33 checks across 8 templates（exit 0 = 全部通过）
```

## 模板作者

basic、origin、130：李一；wine、crimson：徐臻；star：迮佳；blue：沈小丹；global：国际处。
