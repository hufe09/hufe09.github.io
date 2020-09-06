---
layout: post
title: "「SQL For Data Analysis」04 SQL Aggregations"
subtitle: 'SQL 聚合函数'
author: "Hufe"
header-img: "img/post-bg-mysql.jpg"
header-mask: 0.3
tags:
  - SQL For Data Analysis
  - SQL
---

# SQL Aggregations 聚合

## 1. 聚合简介

- **计数 (Count) **是指某一特定列有多少行
- **求和 (Sum) **是对某一特定列内所有值进行求和
- **最小值和最大值 (Min 和 Max) **会返回某一特定列的最低和最高的值
- **平均值 (Average)** 计算某一特定列中所有值的平均值

这些功能针对的是**列**不是行，所以 你可以对纸张的总供应量求和，得到的结果不是几千行内容，而只是一行结果。

此外，CASE，HAVING和DATE函数为您提供了一个令人难以置信的问题解决工具包。

## 2. NULL

**NULL** 是一种数据类型，表示 SQL 中没有数据。它们经常在聚合函数中被忽略了。

在 **WHERE** 条件中表示 **NULL** 时，我们写成 **IS NULL** 或 **IS NOT NULL**。我们不使用 =，因为 **NULL** 在 SQL
中不属于值。但是它是数据的一个属性。

在以下两种常见情况下，可能会遇到 **NULL**：

- 在执行 **LEFT JOIN** 或 **RIGHT JOIN** 时，**NULL** 经常会发生。之前介绍 JOIN 时就见到过，左侧表格中的某些行在做连接时与右侧表格中的行如果不匹配，这些行在结果集中就会包含一些 **NULL** 值。
- **NULL** 也可能是因为数据库中缺失数据。

## 3. COUNT

计算表格中的行数

试着手动数数每个表格的行数。以下是计算 **accounts** 表格中的行数示例：

```sql
SELECT COUNT(*)
 FROM accounts;
```

> 351

我们也可以轻松地选择一列来放置聚合函数：

```sql
SELECT COUNT(accounts.id)
 FROM accounts;
```

>351

上述两个语句是对等的，但是并不始终这样，我们将在后面见到这一例外情况。

```sql
SELECT count(*)
FROM orders
WHERE occurred_at >= "2016-12-01"
  AND occurred_at < "2017-01-01"
```

> 463

COUNT 函数会返回 所有包含非空值数据行的计数，整行都是空值的情况很少见，所以` COUNT(*)` 的结果，一般和表格行数是相等的。

注意：**COUNT** 不会考虑具有 **NULL** 值的行。因此，可以用来快速判断哪些行缺少数据。

```
  SELECT primary_poc
 FROM accounts
 where primary_poc IS NULL;
```

## 4. SUM

与 **COUNT** 不同，只能针对数字列使用 **SUM**。但是，**SUM** 将忽略 **NULL** 值，其他聚合函数也是这样。

重要注意事项：**聚合函数只能垂直聚合，即聚合列的值**。

```sql
SELECT SUM(poster_qty) AS total_poster_sales,
       SUM(standard_qty) AS total_standard_sales,
       SUM(total_amt_usd) AS total_dollar_sales,
       SUM(standard_amt_usd)/SUM(standard_qty) AS standard_price_per_unit
FROM orders;
```

| total_poster_sales | total_standard_sales | total_dollar_sales | standard_price_per_unit |
| ------------------ | -------------------- | ------------------ | ----------------------- |
| 723646             | 1938346              | 23141511.82        | 4.99                    |

```
SELECT standard_qty + gloss_qty AS total_standard_gloss
FROM orders;
```

| total_standard_gloss |
| -------------------- |
| 231                  |
| 132                  |
| 176                  |
| 137                  |
| 145                  |

**此解决方案使用了聚合函数和数学运算符**

## 5. MIN & MAX

1. 最早和最晚的的订单分别下于何时？
``` sql
SELECT MIN(occurred_at),
       MAX(occurred_at)
FROM orders;
```
| MIN(occurred_at)         | MAX(occurred_at)         |
| ------------------------ | ------------------------ |
| 2013-12-04T04:22:44.000Z | 2017-01-02T00:02:40.000Z |

2. 尝试执行和第一个问题一样的查询，但是不使用聚合函数。
最早订单
``` sql
SELECT occurred_at
FROM orders
ORDER BY occurred_at LIMIT 1;
```
最晚订单
``` sql
SELECT occurred_at
FROM orders
ORDER BY occurred_at DESC LIMIT 1;
```
3. 最近的 **web_event** 发生在什么时候？
``` sql
SELECT MAX(occurred_at)
FROM web_events;
```
4. 尝试以另一种方式执行上个问题的查询，不使用聚合函数。
``` sql
SELECT occurred_at
FROM web_events
ORDER BY occurred_at DESC LIMIT 1;
```
5. 算出每个订单在每种纸张上消费的平均(**AVERAGE**) 金额，以及每个订单针对每种纸张购买的平均数量。最终答案应该有 6 个值，每个纸张类型平均销量对应一个值，以及平均数量对应一个值。
``` sql
SELECT AVG(standard_qty) mean_standard,
       AVG(gloss_qty) mean_gloss,
       AVG(poster_qty) mean_poster,
       AVG(standard_amt_usd) mean_standard_usd,
       AVG(gloss_amt_usd) mean_gloss_usd,
       AVG(poster_amt_usd) mean_poster_usd
FROM orders;
```
| mean_standard | mean_gloss | mean_poster | mean_standard_usd | mean_gloss_usd | mean_poster_usd |
| ------------- | ---------- | ----------- | ----------------- | -------------- | --------------- |
| 280.432       | 146.6685   | 104.6942    | 1399.355692       | 1098.54742     | 850.116538      |

6. 请尝试探索这个问题：对于所有订单（orders）数据，其total_usd字段的中位数是多少？
``` sql
SELECT *
FROM
  (SELECT total_amt_usd
   FROM orders
   ORDER BY total_amt_usd LIMIT 3457) AS Table1
ORDER BY total_amt_usd DESC LIMIT 2;
```

| total_amt_usd |
| ------------- |
| 2483.16       |
| 2482.55       |

因为订单一共有6912个，因此我们需要第3456和第3457个订单（按total_amt_usd排序）的total_amt_usd字段的平均值。这样就能得出中位数结果，为**2482.855**。这显然不是一个好办法。如果我们有了新订单，再次计算时就必须修改LIMIT。SQL实际上并不会为我们计算中位数。以上代码使用了一个子查询（SUBQUERY），但你可以使用任何方法找到需要的两个值，然后再求平均即可得到中位数。

## 6. GROUP BY
### GROUP BY Part1

通过 **GROUP BY (分组)** 你可以创建分组，在聚合时相互独立，也就是说 GROUP BY 可以使你只抓取单个账户的数据之和而不是整个数据集。

- **GROUP BY** 可以用来在数据子集中聚合数据。例如，不同客户、不同区域或不同销售代表分组。

- **SELECT** 语句中的任何一列如果不在聚合函数中，则必须在 **GROUP BY** 条件中。

- **GROUP BY** 始终在 **WHERE** 和 **ORDER BY** 之间。

- **ORDER BY** 有点像电子表格软件中的 **SORT**。

  

1. 哪个客户（按照名称）下的订单最早？你的答案应该包含订单的客户名称和日期。
``` sql
SELECT a.name account_name,
       MIN(o.occurred_at)
FROM orders o
JOIN accounts a
  ON o.account_id = a.id
GROUP BY 1
ORDER BY 2;
```
2. 算出每个客户的总销售额（单位是美元）。答案应该包括两列：每个公司的订单总销售额（单位是美元）以及公司名称。
``` sql
SELECT a.name account_name,
       SUM(o.total_amt_usd) total_sales
FROM orders o
JOIN accounts a
  ON a.id = o.account_id
GROUP BY 1
ORDER BY 2 DESC;
```
3. 最近的 web_event 是通过哪个渠道发生的，与此 web_event 相关的客户是哪个？你的查询应该仅返回三个值：日期、渠道和客户名称。
``` sql
SELECT w.channel channel,
       w.occurred_at occurred_at,
       a.name account_name
FROM web_events w
JOIN accounts a
  ON a.id = w.account_id
ORDER BY 2 DESC
LIMIT 1;
```
4. 算出 web_events 中每种渠道的次数。最终表格应该有两列：渠道和渠道的使用次数。
``` sql
SELECT channel,
       COUNT(*) channel_count
FROM web_events
GROUP BY 1
ORDER BY 2 DESC;
```
5. 与最早的 web_event 相关的主要联系人是谁？
``` sql
SELECT a.primary_poc,
       MIN(w.occurred_at)
FROM web_events w
JOIN accounts a
  ON a.id = w.account_id
GROUP BY 1
ORDER BY 2;
```
6. 每个客户所下的最小订单是什么（以总金额（美元）为准）。答案只需两列：客户名称和总金额（美元）。从最小金额到最大金额排序。
``` sql
SELECT a.name account_name,
       MIN(o.total_amt_usd) min_total_sales
FROM accounts a
JOIN orders o
  ON o.account_id = a.id
GROUP BY 1
ORDER BY 2;
```
奇怪的是，很多订单没有美元金额。我们可能需要检查下这些订单。
7. 算出每个区域的销售代表人数。最终表格应该包含两列：区域和 sales_reps 数量。从最少到最多的代表人数排序。
``` sql
SELECT r.name region_name,
       COUNT(s.id) rep_count
FROM region r
JOIN sales_reps s
  ON r.id = s.region_id
GROUP BY 1
ORDER BY 2;
```

### GROUP BY Part2
- 你可以同时按照多列**分组**。可以在大量不同的细分中更好地获得聚合结果。
- **ORDER BY** 条件中列出的列顺序有区别。从左到右让列排序。
- **GROUP BY** 条件中的列名称顺序并不重要，结果还是一样的。如果运行相同的查询并颠倒 **GROUP BY**条件中列名称的顺序，可以看到结果是一样的。
- 和 **ORDER BY** 一样，你可以在 **GROUP BY** 条件中用数字替换列名称。仅当你对大量的列分组时，或者其他原因导致 GROUP BY 条件中的文字过长时，才建议这么做。
- 针对 SELECT 选择出来的列，任何不在聚合函数中的列，必须出现在 GROUP BY 语句中。如果忘记了，可能会遇到错误。但是，即使查询可行，最后的结果可能也不会正确！


1. 对于每个客户，确定他们在订单中购买的每种纸张的平均数额。结果应该有四列：客户**名称**一列，每种纸张类型的平均数额一列。
``` sql
SELECT a.name,
       AVG(o.standard_qty) avg_stand,
       AVG(gloss_qty) avg_gloss,
       AVG(poster_qty) avg_post
FROM accounts a
JOIN orders o
  ON a.id = o.account_id
GROUP BY a.name;
```
2. 对于每个客户，确定在每个订单中针对每个纸张类型的平均消费数额。结果应该有四列：客户**名称**一列，每种纸张类型的平均消费数额一列。
``` sql
SELECT a.name,
       AVG(o.standard_amt_usd) avg_stand,
       AVG(gloss_amt_usd) avg_gloss,
       AVG(poster_amt_usd) avg_post
FROM accounts a
JOIN orders o
  ON a.id = o.account_id
GROUP BY a.name;
```
3. 确定在 **web_events** 表格中每个**销售代表**使用特定**渠道**的次数。最终表格应该有三列：**销售代表的名称**、**渠道**和发生次数。按照最高的发生次数在最上面对表格排序。
``` sql
SELECT s.name,
       w.channel,
       COUNT(*) num_events
FROM accounts a
JOIN web_events w
  ON a.id = w.account_id
JOIN sales_reps s
  ON s.id = a.sales_rep_id
GROUP BY s.name,
         w.channel
ORDER BY num_events DESC;
```
4. 确定在 **web_events** 表格中针对每个**地区**特定**渠道**的使用次数。最终表格应该有三列：**区域名称**、**渠道**和发生次数。按照最高的发生次数在最上面对表格排序。
``` sql
SELECT r.name,
       w.channel,
       COUNT(*) num_events
FROM accounts a
JOIN web_events w
  ON a.id = w.account_id
JOIN sales_reps s
  ON s.id = a.sales_rep_id
JOIN region r
  ON r.id = s.region_id
GROUP BY r.name,
         w.channel
ORDER BY num_events DESC;
```

## 7. DISTINCT

将 **DISTINCT** 看做**仅返回特定列的唯一值**的函数。

1. 使用 **DISTINCT** 检查是否有任何客户与多个区域相关联？

```sql
SELECT DISTINCT a.id,
                r.id,
                a.name,
                r.name
FROM accounts a
JOIN sales_reps s
  ON s.id = a.sales_rep_id
JOIN region r
  ON r.id = s.region_id;
```

``` sql
SELECT DISTINCT id,
                name
FROM accounts;
```
上的两个查询产生了相同的行数（351行），因此我们知道每个客户仅与一个区域相关联。如果每个客户与多个区域相关联，则第一个查询返回的行数应该比第二个查询的多。
2. 有没有**销售代表**要处理多个客户？

```sql
SELECT s.id,
       s.name,
       COUNT(*) num_accounts
FROM accounts a
JOIN sales_reps s
  ON s.id = a.sales_rep_id
GROUP BY s.id,
         s.name
ORDER BY num_accounts;
```

```sql
SELECT DISTINCT id,
                name
FROM sales_reps;
```

实际上，所有销售代表都要处理多个客户。销售代表处理的最少客户数量是 3个。有 50 个销售代表，他们都有多个客户。在第二个查询中使用 **DISTINCT** 确保包含了第一个查询中的所有销售代表。

## 8. HAVING

**HAVING** 是过滤被聚合的查询的“整洁”方式，但是通常采用[子查询](https://community.modeanalytics.com/sql/tutorial/sql-subqueries/)的方式来实现。本质上，只要你想对通过聚合创建的查询中的元素执行 **WHERE** 条件，就需要使用 **HAVING**。

经常你会对 **WHERE** 和 **HAVING** 之间的差别感到困惑。关于 **HAVING** 和 **WHERE** 的语句：

- **WHERE** 出现在 **FROM**，**JOIN** 和 **ON** 条件之后，但是在 **GROUP BY** 之后。
- **HAVING** 出现在 **GROUP BY** 条件之后，但是在 **ORDER BY** 条件之前。
- **HAVING** 和 **WHERE** 相似，但是它适合涉及聚合的逻辑语句。

1. 有多少位**销售代表**需要管理超过5 个客户？
``` sql
SELECT s.name,
       s.id,
       COUNT(*) num_accounts
FROM sales_reps s
JOIN accounts a
  ON a.sales_rep_id = s.id
GROUP BY s.name,
         s.id
HAVING num_accounts > 5 
ORDER BY num_accounts;
```
> 34
实际上，我们可以使用 **SUBQUERY** 获得这一结果，如下所示。其他查询也可以使用这一逻辑，下面就不显示了。

``` sql
SELECT COUNT(*) num_reps_above5 FROM
  (SELECT s.id, s.name, COUNT(*) num_accounts
   FROM accounts a
   JOIN sales_reps s
     ON s.id = a.sales_rep_id
   GROUP BY s.id, s.name
   HAVING COUNT(*) > 5
   ORDER BY num_accounts) AS Table1;
```
>34
2. 有多少个**客户**具有超过 20 个订单？
``` sql
SELECT a.id,
       a.name,
       COUNT(*) num_orders
FROM accounts a
JOIN orders o
  ON a.id = o.account_id
GROUP BY a.id,
         a.name
HAVING COUNT(*) > 20
ORDER BY num_orders;
```
> 120
3. 哪个客户的订单最多？
``` sql
SELECT a.id, a.name, COUNT(*) num_orders
FROM accounts a
JOIN orders o
ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY num_orders DESC;
```
| id   | name              | num_orders |
| ---- | ----------------- | ---------- |
| 3411 | Leucadia National | 71         |

4. 有多少个客户在所有订单上消费的总额超过了 30,000 美元？
``` sql
SELECT a.id,
       a.name,
       SUM(o.total_amt_usd) total_spent
FROM accounts a
JOIN orders o
  ON a.id = o.account_id
GROUP BY a.id,
         a.name
HAVING SUM(o.total_amt_usd) > 30000
ORDER BY total_spent;
```
>204
5. 有多少个客户在所有订单上消费的总额不到 1,000 美元？
``` sql
SELECT a.id,
       a.name,
       SUM(o.total_amt_usd) total_spent
FROM accounts a
JOIN orders o
  ON a.id = o.account_id
GROUP BY a.id,
         a.name
HAVING SUM(o.total_amt_usd) < 1000
ORDER BY total_spent;
```
>3
6. 哪个客户消费的最多？
``` sql
SELECT a.id, a.name, SUM(o.total_amt_usd) total_spent
FROM accounts a
JOIN orders o
ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY total_spent DESC
LIMIT 1;
```
| id   | name          | total_spent |
| ---- | ------------- | ----------- |
| 4211 | EOG Resources | 382873.3    |

7. 哪个客户消费的最少？
``` sql
SELECT a.id, a.name, SUM(o.total_amt_usd) total_spent
FROM accounts a
JOIN orders o
ON a.id = o.account_id
GROUP BY a.id, a.name
ORDER BY total_spent
LIMIT 1;
```
| id   | name | total_spent |
| ---- | ---- | ----------- |
| 1901 | Nike | 390.25      |

8. 哪个客户使用 facebook 作为与消费者沟通的**渠道**超过 6 次？
``` sql
SELECT a.id,
       a.name,
       w.channel,
       COUNT(*) use_of_channel
FROM web_events w
JOIN accounts a
  ON a.id = w.account_id
GROUP BY a.id,
         a.name,
         w.channel
HAVING COUNT(*) > 6
AND w.channel = "facebook"
ORDER BY use_of_channel;
```
9. 哪个客户使用 facebook 作为沟通**渠道**的次数最多？
``` sql
SELECT a.id,
       a.name,
       w.channel,
       COUNT(*) use_of_channel
FROM web_events w
JOIN accounts a
  ON a.id = w.account_id
WHERE w.channel = "facebook"
GROUP BY a.id,
         a.name,
         w.channel
ORDER BY use_of_channel DESC;
```
| id   | name            | channel  | use_of_channel |
| ---- | --------------- | -------- | -------------- |
| 1851 | Gilead Sciences | facebook | 16             |

10. 哪个渠道是客户最常用的渠道？
``` sql
SELECT a.id,
       a.name,
       w.channel,
       COUNT(*) use_of_channel
FROM web_events w
JOIN accounts a
  ON a.id = w.account_id
GROUP BY a.id,
         a.name,
         w.channel
ORDER BY use_of_channel DESC
LIMIT 10;
```
| id   | name              | channel | use_of_channel |
| ---- | ----------------- | ------- | -------------- |
| 3411 | Leucadia National | direct  | 52             |

上面的所有 10 列都是 direct。

## 9. CASE

- CASE 语句始终位于 SELECT 条件中。
- CASE 必须包含以下几个部分：WHEN、THEN 和 END。ELSE 是可选组成部分，用来包含不符合上述任一 CASE 条件的情况。
- 可以在 WHEN 和 THEN 之间使用任何条件运算符编写任何条件语句（例如 [WHERE](https://community.modeanalytics.com/sql/tutorial/sql-where/)），包括使用 AND 和      OR 连接多个条件语句。
- 可以再次包含多个 WHEN 语句以及 ELSE 语句，以便处理任何未处理的条件。

```sql
SELECT id,
       account_id,
       poster_amt_usd / poster_qty unit_price
FROM orders LIMIT 5;
```
| id   | account_id | unit_price |
| ---- | ---------- | ---------- |
| 1    | 1001       | 8.12       |
| 2    | 1001       | 8.12       |
| 3    | 1001       |            |
| 4    | 1001       |            |
| 5    | 1001       | 8.12       |

**id为3,4时，unit_price为空，这是因为这两个订单poster_qty为0，计算过程除以了 0。**

 **使用CASE语句时，可以让此查询不会出现这种情况。**

```sql
SELECT account_id,
       CASE
           WHEN poster_qty = 0
                OR poster_qty IS NULL THEN 0
           ELSE poster_amt_usd/poster_qty
       END AS unit_price
FROM orders LIMIT 5;
```

| account_id | unit_price |
| ---------- | ---------- |
| 1001       | 8.12       |
| 1001       | 8.12       |
| 1001       | 0          |
| 1001       | 0          |
| 1001       | 8.12       |

该语句的第一部分将捕获任何分母为 0 并导致错误的情况，其他部分将按照常规步骤相除。你将发现对于海报纸，所有客户的单价是8.12 美元。这样比较合理，不会波动，并且比在之前向分母上加 1 来暂时解决错误这一方法更准确。

### CASE 与聚合

作为一名 Parch & Posey 的销售运营经理，将订单进行分组是一项很有用的练习，对这些新分类进行聚合，能更容易地向领导汇报并采取相应行动，进行组内计数的最简单的办法，是创建一个按照期望方式分类的列。
接下来，创建另外一个列 进行组的计数。我们使用 CASE 将订单分为总销售额超过 500和 500 及以下的分组，对此查询最好在 GROUP BY 子句中，用数字替代整列计算，因为在 GROUP BY 中重复 CASE 语句，会让查询变得冗长。


```sql
SELECT CASE
           WHEN total>500 THEN "over 500"
           ELSE "500 or under"
       END AS total_group,
       COUNT(*) AS order_count
FROM orders
GROUP BY 1;
```

| total_group  | order_count |
| ------------ | ----------- |
| 500 or under | 3716        |
| over 500     | 3196        |

**为什么我不用 WHERE 子句过滤掉那些我不想计数的行？**

```sql
SELECT COUNT(1) AS orders_over_500_units
FROM orders
WHERE total > 500;
```

> 3196

虽然可以这样做，结果可能是这样，不幸的是 WHERE 子句只允许一次计算一个条件，如果我们有大量的不同案例 内容会特别冗长，我们可能需要为每个案例编写不同的查询。

### Practice: CASE

1. 我们想要根据相关的消费量了解三组不同的客户。最高的一组是终身价值（所有订单的总销售额）大于      200,000 美元的客户。第二组是在 200,000 到 100,000 美元之间的客户。最低的一组是低于 under      100,000 美元的客户。请提供一个表格，其中包含与每个**客户**相关的**级别**。你应该提供客户的**名称**、**所有订单的总销售额**和**级别**。消费最高的客户列在最上面。
``` sql
SELECT a.name,
       CASE
           WHEN SUM(total_amt_usd) >= 200000 THEN "top"
           WHEN SUM(total_amt_usd) <200000
                AND SUM(total_amt_usd) >=100000 THEN "middle"
           WHEN SUM(total_amt_usd) <100000 THEN "low"
       END AS account_value
FROM orders o
JOIN accounts a
  ON a.id = o.account_id
GROUP BY a.name;
```
2. 现在我们想要执行和第一个问题相似的计算过程，但是我们想要获取在 2016 年和 2017 年客户的总消费数额。级别和上一个问题保持一样。消费最高的客户列在最上面。


``` sql
SELECT a.name,
       CASE
           WHEN SUM(total_amt_usd) >= 200000 THEN "top"
           WHEN SUM(total_amt_usd) <200000
                AND SUM(total_amt_usd) >=100000 THEN "middle"
           WHEN SUM(total_amt_usd) <100000 THEN "low"
       END AS account_value
FROM orders o
JOIN accounts a
  ON a.id = o.account_id
WHERE DATE_FORMAT(o.occurred_at, "%Y") BETWEEN 2016 AND 2017
GROUP BY a.name;
```
3. 我们想要找出绩效最高的**销售代表**，也就是有超过 200 个订单的销售代表。创建一个包含以下列的表格：**销售代表名称**、订单总量和标为 top 或 not 的列（取决于是否拥有超过200 个订单）。销售量最高的销售代表列在最上面。
``` sql
SELECT s.name,
       COUNT(o.id) sum_of_rep_orders,
       CASE
           WHEN COUNT(o.id)> 200 THEN "top"
           ELSE "no"
       END AS KPI
FROM sales_reps s
JOIN accounts a
  ON a.sales_rep_id = s.id
JOIN orders o
  ON o.account_id = a.id
GROUP BY s.name
ORDER BY sum_of_rep_orders DESC;
```
4. 之前的问题没有考虑中间水平的销售代表或销售额。管理层决定也要看看这些数据。我们想要找出绩效很高的**销售代表**，也就是有超过 200 个订单或总销售额超过 750000 美元的销售代表。中间级别是指有超过 150      个订单或销售额超过 500000 美元的销售代表。创建一个包含以下列的表格：**销售代表名称**、总订单量、所有订单的总销售额，以及标为 top、middle 或 low 的列（取决于上述条件）。在最终表格中将销售额最高的销售代表列在最上面。根据上述标准，你可能会见到几个表现很差的销售代表！
``` sql
SELECT s.name,
       COUNT(o.id) sum_of_rep_orders,
       SUM(o.total_amt_usd) total_sales,
       CASE
           WHEN COUNT(o.id)> 200
                OR SUM(o.total_amt_usd) > 750000 THEN "top"
           WHEN COUNT(o.id) BETWEEN 150 AND 200
                OR SUM(o.total_amt_usd) BETWEEN 500000 AND 750000 THEN "middle"
           ELSE "low"
       END AS KPI
FROM sales_reps s
JOIN accounts a
  ON a.sales_rep_id = s.id
JOIN orders o
  ON o.account_id = a.id
GROUP BY s.name
ORDER BY 3 DESC;
```

## 10. DATE

首先，我们要介绍的日期函数是 **DATE_TRUNC**。

**DATE_TRUNC** 使你能够将日期截取到日期时间列的特定部分。常见的截取依据包括日期、月份 和 年份。[这是一篇](https://blog.modeanalytics.com/date-trunc-sql-timestamp-function-count-on/) **MODE** 发表的精彩博文，介绍了关于此函数的强大功能。

**DATE_PART** 可以用来获取日期的特定部分，但是注意获取 month 或 dow 意味着无法让年份按顺序排列。而是按照特定的部分分组，无论它们属于哪个年份。

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/ySVzsnrLOhuEJoc.png)

要了解其他日期函数，请参阅[这篇](https://www.postgresql.org/docs/9.1/static/functions-datetime.html)文档，但是上面介绍的函数绝对够你入门了！

### MySQL DATE

[**MySQL Date 函数**](http://www.w3school.com.cn/sql/sql_dates.asp)

下面的表格列出了 MySQL 中最重要的内建日期函数：

| **函数**                                                     | **描述**                            |
| ------------------------------------------------------------ | ----------------------------------- |
| [NOW()](http://www.w3school.com.cn/sql/func_now.asp)         | 返回当前的日期和时间                |
| [CURDATE()](http://www.w3school.com.cn/sql/func_curdate.asp) | 返回当前的日期                      |
| [CURTIME()](http://www.w3school.com.cn/sql/func_curtime.asp) | 返回当前的时间                      |
| [DATE()](http://www.w3school.com.cn/sql/func_date.asp)       | 提取日期或日期/时间表达式的日期部分 |
| [EXTRACT()](http://www.w3school.com.cn/sql/func_extract.asp) | 返回日期/时间按的单独部分           |
| [DATE_ADD()](http://www.w3school.com.cn/sql/func_date_add.asp) | 给日期添加指定的时间间隔            |
| [DATE_SUB()](http://www.w3school.com.cn/sql/func_date_sub.asp) | 从日期减去指定的时间间隔            |
| [DATEDIFF()](http://www.w3school.com.cn/sql/func_datediff_mysql.asp) | 返回两个日期之间的天数              |
| [DATE_FORMAT()](http://www.w3school.com.cn/sql/func_date_format.asp) | 用不同的格式显示日期/时间           |

### MySQL DATE_FORMAT() 函数

**定义和用法**

`DATE_FORMAT() `函数用于以不同的格式显示日期/时间数据。

**语法**

`DATE_FORMAT(date,format)`

*date* 参数是合法的日期。*format* 规定日期/时间的输出格式。

可以使用的格式有：

| **格式** | **描述**                                         |
| -------- | ------------------------------------------------ |
| %a       | 缩写星期名                                       |
| %b       | 缩写月名                                         |
| %c       | 月，数值                                         |
| %D       | 带有英文前缀的月中的天                           |
| %d       | 月的天，数值(00-31)                              |
| %e       | 月的天，数值(0-31)                               |
| %f       | 微秒                                             |
| %H       | 小时   (00-23)                                   |
| %h       | 小时   (01-12)                                   |
| %I       | 小时   (01-12)                                   |
| %i       | 分钟，数值(00-59)                                |
| %j       | 年的天   (001-366)                               |
| %k       | 小时 (0-23)                                      |
| %l       | 小时 (1-12)                                      |
| %M       | 月名                                             |
| %m       | 月，数值(00-12)                                  |
| %p       | AM 或 PM                                         |
| %r       | 时间，12-小时（hh:mm:ss   AM 或 PM）             |
| %S       | 秒(00-59)                                        |
| %s       | 秒(00-59)                                        |
| %T       | 时间, 24-小时   (hh:mm:ss)                       |
| %U       | 周 (00-53)   星期日是一周的第一天                |
| %u       | 周 (00-53)   星期一是一周的第一天                |
| %V       | 周 (01-53)   星期日是一周的第一天，与 %X 使用    |
| %v       | 周 (01-53)   星期一是一周的第一天，与 %x 使用    |
| %W       | 星期名                                           |
| %w       | 周的天   （0=星期日, 6=星期六）                  |
| %X       | 年，其中的星期日是周的第一天，4   位，与 %V 使用 |
| %x       | 年，其中的星期一是周的第一天，4   位，与 %v 使用 |
| %Y       | 年，4 位                                         |
| %y       | 年，2 位                                         |

**实例**

下面的脚本使用 DATE_FORMAT() 函数来显示不同的格式。我们使用 NOW() 来获得当前的日期/时间：

```
DATE_FORMAT(NOW(),'%b %d %Y %h:%i %p')
 DATE_FORMAT(NOW(),'%m-%d-%Y')
 DATE_FORMAT(NOW(),'%d %b %y')
 DATE_FORMAT(NOW(),'%d %b %Y %T:%f')
```

结果类似：

```
Dec 29 2008 11:45 PM
12-29-2008
29 Dec 08
29 Dec 2008 16:25:46.635
```

### Practice DATE

1. Parch & Posey 在哪一年的总销售额最高？数据集中的所有年份保持均匀分布吗？
``` sql
SELECT DATE_FORMAT(occurred_at, "%Y") ord_year,
       SUM(total_amt_usd) total_spent
FROM orders
GROUP BY 1
ORDER BY 2 DESC;
```
| ord_year | total_spent |
| -------- | ----------- |
| 2016     | 12864917.91 |
| 2015     | 5752004.94  |
| 2014     | 4069106.55  |
| 2013     | 377331      |
| 2017     | 78151.43    |

>对于 2013 年和 2017 年来说，每一年只有一个月的销量（2013 年为 12，2017 年为 1）。 因此，二者都不是均匀分布。销量一年比一年高，2016 年是到目前为止最高的一年。按照这个速度，我们预计 2017 年可能是最高销量的一年。

2. Parch & Posey 在哪一个月的总销售额最高？数据集中的所有月份保持均匀分布吗？
   为了保持公平，我们应该删掉 2013 年和 2017 年的销量。原因如上。
``` sql
SELECT DATE_FORMAT(occurred_at, "%m") ord_month,
       SUM(total_amt_usd) total_spent
FROM orders
WHERE occurred_at BETWEEN '2014-01-01' AND '2017-01-01'
GROUP BY 1
ORDER BY 2 DESC;
```
| ord_month | total_spent |
| --------- | ----------- |
| 12        | 2752080.97  |
| 10        | 2427505.97  |
| 11        | 2390033.75  |
| 09        | 2017216.88  |
| 07        | 1978731.15  |
| 08        | 1918107.22  |
| 06        | 1871118.52  |
| 03        | 1659987.88  |
| 04        | 1562037.74  |
| 05        | 1537082.23  |
| 02        | 1312616.64  |
| 01        | 1259510.44  |

>12 月的销量最高。
3. Parch & Posey 在哪一年的总订单量最多？数据集中的所有年份保持均匀分布吗？
``` sql
SELECT DATE_FORMAT(occurred_at, "%Y") ord_year,
       COUNT(*) total_sales
FROM orders
GROUP BY 1
ORDER BY 2 DESC;
```
| ord_year | total_sales |
| -------- | ----------- |
| 2016     | 3757        |
| 2015     | 1725        |
| 2014     | 1306        |
| 2013     | 99          |
| 2017     | 25          |

>同样，到目前为止，2016 年的订单量最多，但是与数据集中的其他年份相比，2013 年和 2017 年的分布不均匀。
4. Parch & Posey 在哪一个月的总订单量最多？数据集中的所有月份保均匀分布吗？
``` sql
SELECT DATE_FORMAT(occurred_at, "%m") ord_month,
       COUNT(*) total_sales
FROM orders
WHERE occurred_at BETWEEN '2014-01-01' AND '2017-01-01'
GROUP BY 1
ORDER BY 2 DESC;
```
| ord_month | total_sales |
| --------- | ----------- |
| 12        | 783         |
| 11        | 713         |
| 10        | 675         |
| 08        | 603         |
| 09        | 602         |
| 07        | 571         |
| 06        | 527         |
| 05        | 518         |
| 03        | 482         |
| 04        | 472         |
| 01        | 433         |
| 02        | 409         |

>12 月依然是销量最多的月份，但是有趣的是，11 月是销量第二多的月份。为了保持公平，删掉了 2017 年和 2013 年的数据。
5. Walmart 在哪一年的哪一个月在铜版纸上的消费最多？
``` sql
SELECT DATE_FORMAT(o.occurred_at, "%Y-%m") ord_month,
       a.name,
       SUM(o.gloss_amt_usd) total_gloss_spent
FROM orders o
JOIN accounts a
  ON o.account_id = a.id
WHERE a.name = "Walmart"
GROUP BY 1,
         2
ORDER BY 3 DESC;
```
| ord_month | name    | total_gloss_spent |
| --------- | ------- | ----------------- |
| 2016-05   | Walmart | 9257.64           |
| 2016-01   | Walmart | 5070.73           |
| ...       |         |                   |

> 在 2016 年 5 月，Walmart 在铜版纸上的消费做多。
