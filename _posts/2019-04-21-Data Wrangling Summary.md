---
layout: post
title: "「数据分析」Data Wrangling Summary"
subtitle: 'Data Analysis : Data Wrangling'
author: "Hufe"
header-img: "img/post-bg-dataset.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Data Analysis
---

# Data Wrangling Summary
# 数据整理摘要

## Gather 收集 
- 根据数据来源及其所处的格式，收集数据的步骤会有所不同。
- 高级收集过程：获取数据（从Internet下载文件，抓取网页，查询API等）并将该数据导入您的编程环境（例如，Jupyter Notebook）。

## Assess 评估
- 评估以下数据：
    - 质量（Quality）：内容问题。低质量数据也称为脏数据（dirty data）。
    - 整洁度（Tidiness）：结构问题妨碍简单分析。不整洁的数据也称为凌乱的数据。整洁的数据要求：
        1. 每个变量形成一列。
        2. 每个观察形成一排。
        3. 每种类型的观察单位形成一个表格。
- 评估类型：
    - 视觉评估：滚动首选软件应用程序中的数据（Google表格，Excel，文本编辑器等）。
    - 编程评价：使用代码来查看特定的部分和数据的概要（例如:pandas `head`，`tail` and `info`方法）。

## Clean 清洗 
- 清洗类型：
    - 手动（除非问题是单次出现，否则不推荐）
    - 编程
- 程序化数据清洗流程：
    1. Define 定义：将我们的评估转换为定义的清洁任务。这些定义也可作为指令列表，以便其他人（或将来自己）可以查看您的工作并重现它。
    2. Code 代码：将这些定义转换为代码并运行该代码。
    3. Test 测试：通过视觉或代码测试数据集，以确保您的清洗操作有效。
- 在清洗之前，请务必复制原始数据！

### Reassess and Iterate 

### 重新评估并重复

- 清洗后，如有必要，请务必重新评估并迭代任何数据争用步骤。

### Store (Optional) 

### 保存（可选）

- 例如，如果您将来需要使用数据，请将数据存储在文件或数据库中。
