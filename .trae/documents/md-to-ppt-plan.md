# 计划：将 ReadCode MD 文件转换为 PPT

## 摘要

将 `/workspace/ReadCode/` 下的 21 个 Markdown 分析文章分别转换为 PowerPoint 演示文稿，保存到 `/workspace/ReadCode/PPT/` 目录。

## 当前状态分析

- 21 个 MD 文件位于 `/workspace/ReadCode/`，每篇遵循"总—分—总"结构
- 每篇包含 5-7 个 Mermaid 图表、代码片段、表格
- `python-pptx` 库已安装可用
- 无 pandoc/marp 等其他 PPT 工具

## 实施方案

### 技术选择：Python 脚本 + python-pptx

编写一个 Python 脚本 `generate_ppts.py`，自动解析每个 MD 文件并生成对应的 PPT。

**核心逻辑**：
1. 解析 MD 文件，按 `##` / `###` 标题拆分为幻灯片
2. 每个一级/二级标题 → 一张幻灯片
3. Mermaid 代码块 → 在幻灯片中以文本框展示（标注"Mermaid 图表：图X-Y"）
4. 代码块 → 等宽字体文本框
5. 表格 → PPT 表格
6. 普通段落 → 项目符号列表

**PPT 设计规范**：
- 尺寸：16:9 宽屏
- 配色：深蓝标题栏 + 白色背景 + 灰色正文
- 字体：标题用黑体/微软雅黑，正文用等线/微软雅黑
- 每张幻灯片：顶部标题 + 内容区
- 首页：文章标题 + 副标题

### 文件清单

| 操作 | 文件路径 | 说明 |
|------|---------|------|
| 创建 | `/workspace/ReadCode/PPT/` | PPT 输出目录 |
| 创建 | `/workspace/ReadCode/PPT/generate_ppts.py` | PPT 生成脚本 |
| 生成 | `/workspace/ReadCode/PPT/01-项目总览与架构.pptx` | 第01篇 PPT |
| 生成 | `/workspace/ReadCode/PPT/02-核心压缩流水线.pptx` | 第02篇 PPT |
| ... | ... | 共21个 PPTX 文件 |
| 生成 | `/workspace/ReadCode/PPT/21-设计理念与演进.pptx` | 第21篇 PPT |

### 脚本设计

```python
# generate_ppts.py 核心流程
1. 遍历 /workspace/ReadCode/*.md
2. 对每个 MD 文件：
   a. 解析 Markdown：按 ## 标题拆分章节
   b. 创建 Presentation 对象（16:9）
   c. 生成封面页（标题 + 副标题）
   d. 逐章节生成幻灯片：
      - 标题 → 幻灯片标题
      - Mermaid 块 → 居中文本框（标注图表编号）
      - 代码块 → 等宽字体文本框（深色背景）
      - 表格 → PPT 表格
      - 列表项 → 项目符号
      - 普通段落 → 文本框
   e. 保存到 /workspace/ReadCode/PPT/{filename}.pptx
```

### 幻灯片拆分规则

- `# 标题` → 封面页
- `## 总（概述）` / `## 分（详细分析）` / `## 总（总结）` → 章节分隔页
- `### 子标题` → 内容幻灯片
- `#### 更小标题` → 合并到上一张幻灯片或新建幻灯片（视内容量）
- Mermaid 代码块 → 独立一张幻灯片（标注图表名）
- 代码块超过 15 行 → 独立一张幻灯片

## 假设与决策

1. **Mermaid 图表**：无法在 PPT 中直接渲染为图形，以格式化文本展示并标注图表编号
2. **代码片段**：使用等宽字体 + 浅灰背景，保持可读性
3. **长内容**：单张幻灯片内容过多时自动拆分为多张
4. **表格**：直接转换为 PPT 原生表格
5. **链接**：保留为蓝色下划线文本

## 验证步骤

1. 运行脚本，确认 21 个 PPTX 文件全部生成
2. 抽查 2-3 个 PPTX 文件，确认：
   - 封面页标题正确
   - 章节结构完整（总—分—总）
   - Mermaid 图表以文本形式呈现
   - 代码块格式正确
   - 表格渲染正常
