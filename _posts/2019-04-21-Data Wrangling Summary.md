---
layout: post
title: "「数据争论」Data Wrangling Summary"
subtitle: 'Data Analysis : Data Wrangling'
author: "Hufe"
header-img: "img/post-bg-datas.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Data Analysis
  - Data Wrangling
---

# Data Wrangling Summary
# 数据争论摘要

Wrangling是个奇怪的词。让我们检查一下定义。
- 作为不及物动词，指争吵，或愤怒而吵闹地争论。
- 作为及物动词，放马或放牧。

我们需要为好的结果争论我们的数据，否则可能会有后果。如果我们在争论之前分析、可视化或建模我们的数据，我们的后果可能是犯错误、错失冷静的洞察力和浪费时间。所以最佳实践就是争吵。
>**Data wrangling**, sometimes referred to as data munging, is the process of transforming and mapping data from one "raw" data form into another format with the intent of making it more appropriate and valuable for a variety of downstream purposes such as analytics. A data wrangler is a person who performs these transformation operations.    --**wikipedia**
>
>**数据争论**，有时也称为数据管理，是将数据从一种“原始”数据形式转换和映射到另一种格式的过程，目的是使其更适合于各种下游目的，如分析。数据管理员是执行这些转换操作的人。

这可能包括进一步管理、数据可视化、数据聚合、训练统计模型以及许多其他潜在用途。作为一个过程，数据争论通常遵循一组一般步骤，这些步骤从数据源提取原始形式的数据开始，使用算法(例如排序)对原始数据进行“管理”，或者将数据解析成预定义的数据结构，最后将结果内容存放到数据接收器中以供存储和将来使用。

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
