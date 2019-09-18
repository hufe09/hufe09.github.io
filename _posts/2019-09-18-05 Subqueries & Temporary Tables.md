---
layout: post
title: "「SQL For Data Analysis」05 Subqueries & Temporary Tables"
subtitle: 'SQL 子查询和临时表'
author: "Hufe"
header-img: "img/post-bg-mysql.jpg"
header-mask: 0.3
tags:
  - SQL For Data Analysis
  - SQL
---

# Subqueries & Temporary Tables

使用嵌套查询方法（也称为子查询）和临时表来回答更复杂的业务问题。

## 1. Subqueries 子查询

每当我们需要使用现有表格创建新的表格，然后需要再次查询时，就表明我们需要使用某种**子查询**。

### 首个子查询

我们想要算出每个渠道每天的平均事件次数。第一个表格将提供每个渠道每天的事件次数，然后我们将使用第二个查询对这些值求平均。

**Soultion**

1. 首先，我们需要按照日期和渠道分组。然后按事件数（第三列）排序，这样可以快速得出第一个问题的答案。

``` sql
SELECT DATE_FORMAT(occurred_at, "%Y-%s-%d") event_date,
       channel,
       COUNT(*) num_evnnts
FROM web_events
GROUP BY 1,
         2
ORDER BY 3;
```
2. 可以看出，要获得这一结果，提供了整个原始表格。查询的附加部分包括 *，并且我们需要为表格设置别名。此外，是在 **SELECT** 语句中（而不是 **FROM**）中提供表格。
``` sql
SELECT *
FROM
  (SELECT DATE_FORMAT(occurred_at, "%Y-%s-%d") event_date,
          channel,
          COUNT(*) num_evnnts
   FROM web_events
   GROUP BY 1,
            2
   ORDER BY 3) sub;
```
3. 最后，我们在以下语句中能够获得显示每个渠道一天的平均事件数的表格。
``` sql
SELECT channel,
       AVG(num_evnnts) avg_events
FROM
  (SELECT DATE_FORMAT(occurred_at, "%Y-%s-%d") event_date,
          channel,
          COUNT(*) num_evnnts
   FROM web_events
   GROUP BY 1,
            2
   ORDER BY 3) sub
GROUP BY 1
ORDER BY 2 DESC;
```

## 2. 子查询格式

在编写**子查询**时，查询很容易就看起来很复杂。为了便于阅读，其实日后经常只是你自己要阅读：

要记住的重要事项是，在使用子查询时，要让读者能够轻松地判断查询的哪个部分将一起执行。大部分人的做法是按照某种方式缩进子查询，上一页面的解决方案就是这么做的。

这节课的示例缩进很明显，一直到小括号。如果你嵌套了很多的子查询，则不适用，一般法则就是思考下如何以便于阅读的方式编写查询。下面给出了以多种方式编写同一查询的示例。你会发现，某些示例明显比其他的容易阅读。

### 格式糟糕的查询

虽然这些格式糟糕的查询和格式清晰的查询一样会执行，但是却不容易让人理解查询的作用！

- 第一个示例，根本无法判断查询的作用：

``` sql
SELECT * FROM (SELECT DATE_TRUNC('day',occurred_at) AS day, channel, COUNT(*) as events FROM web_events GROUP BY 1,2 ORDER BY 3 DESC) sub;
```

- 第二个示例不是太糟糕，但是你会发现最后一个示例依然更容易读懂。

``` sql
SELECT *
FROM (
SELECT DATE_TRUNC('day',occurred_at) AS day,
channel, COUNT(*) as events
FROM web_events
GROUP BY 1,2
ORDER BY 3 DESC) sub;
```

### 格式清晰的查询

与之前的示例相比，在这个格式清晰的示例中，我们很容易就看出要从哪个表格中获取数据。此外，如果在子查询后面有 **GROUP BY**、**ORDER BY**、**WHERE**、**HAVING** 或任何其他语句，则按照外部查询的同一级别缩进，正如最后一个示例所显示的，它是**首个子查询**的最后一个解决方案。
``` sql
SELECT *
FROM (SELECT DATE_TRUNC('day',occurred_at) AS day,
                channel, COUNT(*) as events
      FROM web_events 
      GROUP BY 1,2
      ORDER BY 3 DESC) sub;
```

下面的查询很相似，但是向外部查询应用了其他逻辑，因此按照外部查询的级别缩进。而内部查询逻辑的缩进级别与内部表格匹配。
``` sql
SELECT *
FROM (SELECT DATE_TRUNC('day',occurred_at) AS day,
                channel, COUNT(*) as events
      FROM web_events 
      GROUP BY 1,2
      ORDER BY 3 DESC) sub
GROUP BY channel
ORDER BY 2 DESC;
```
最后两个查询容易读懂多了！

## 3. 子查询（第二部分）

在你写的第一个子查询中，你编写了一个子查询来创建表格，然后可以在 **FROM** 语句中查询该表格。但是，如果只返回一个值，则可以在逻辑语句中使用该值，例如 **WHERE**、**HAVING**，甚至 **SELECT**，该值可以嵌套在 **CASE** 语句中。

**问题：**

第一个订单是在哪年/月下产生的。

在**orders表**中第一个月下单时所出售的标准纸的平均数量。

在**orders表**中第一个月下单时所出售的铜版纸的平均数量。

在**orders表**中第一个月下单时所出售的海报纸的平均数量。

在**orders表**中第一个月下单时所出售的所有订单总消费。



1. 以下是从 orders 表格中获取第一个订单的年/月信息的查询。
``` sql
SELECT DATE_FORMAT(MIN(occurred_at), "%Y-%m") 
FROM orders;
```
>2013-12


2. 然后，为了获取每个订单的平均值，我们可以在一个查询中执行所有的任务。

``` sql
SELECT AVG(standard_qty) avg_std,
       AVG(gloss_qty) avg_gls,
       AVG(poster_qty) avg_pst,
       SUM(total_amt_usd) total_spent
FROM orders
WHERE DATE_FORMAT(occurred_at, "%Y-%m") =
    (SELECT DATE_FORMAT(MIN(occurred_at), "%Y-%m")
     FROM orders);
```

```sql
SELECT DATE_FORMAT(occurred_at, "%Y-%m") ord_date,
       AVG(standard_qty) avg_std,
       AVG(gloss_qty) avg_gls,
       AVG(poster_qty) avg_pst,
       SUM(total_amt_usd) total_spent
FROM orders
WHERE DATE_FORMAT(occurred_at, "%Y-%m") =
    (SELECT DATE_FORMAT(MIN(occurred_at), "%Y-%m")
     FROM orders)
GROUP BY 1;
```

## Practice: Subqueries 

1. 提供每个**区域**拥有最高销售额 (**total_amt_usd**) 的**销售代表**的**姓名**。

``` sql
SELECT r.name region_name,
       s.name rep_name,
       SUM(o.total_amt_usd) total_count_orders
FROM region r
JOIN sales_reps s
  ON s.region_id = r.id
JOIN accounts a
  ON a.sales_rep_id =s.id
JOIN orders o
  ON o.account_id = a.id
GROUP BY 1,
         2;
```
``` sql
SELECT DISTINCT region_name,
                MAX(total_count_orders) max_orders
FROM
  (SELECT r.name region_name,
          s.name rep_name,
          SUM(o.total_amt_usd) total_count_orders
   FROM region r
   JOIN sales_reps s
     ON s.region_id = r.id
   JOIN accounts a
     ON a.sales_rep_id =s.id
   JOIN orders o
     ON o.account_id = a.id
   GROUP BY 1,
            2) sub
GROUP BY 1
ORDER BY 2 DESC;
```
2. 对于具有最高销售额 (**total_amt_usd**) 的区域，总共下了多少个订单 （**total** count orders） ？

``` sql
SELECT region_name,
       MAX(total_count_orders) max_orders,
       num_orders
FROM
  (SELECT r.name region_name,
          SUM(o.total_amt_usd) total_count_orders,
          COUNT(o.id) num_orders
   FROM region r
   JOIN sales_reps s
     ON s.region_id = r.id
   JOIN accounts a
     ON a.sales_rep_id =s.id
   JOIN orders o
     ON o.account_id = a.id
   GROUP BY 1) sub
GROUP BY 1
ORDER BY 2 DESC;
```
3. 对于购买标准纸张数量 (**standard_qty**) 最多的客户（在作为客户的整个时期内），**有多少客户**的购买总数依然更多？


``` sql
SELECT a.name,
       SUM(o.standard_qty) total_std,
       SUM(o.total) total_sales
FROM orders o
JOIN accounts a
  ON a.id = o.account_id
GROUP BY 1
ORDER BY 2 DESC LIMIT 1;
```

``` sql
SELECT COUNT(*)
FROM
  (SELECT a.name
   FROM orders o
   JOIN accounts a
     ON a.id = o.account_id
   GROUP BY 1
   HAVING SUM(o.total) >
     (SELECT total_sales
      FROM
        (SELECT a.name,
                SUM(o.standard_qty) total_std,
                SUM(o.total) total_sales
         FROM orders o
         JOIN accounts a
           ON a.id = o.account_id
         GROUP BY 1
         ORDER BY 2 DESC LIMIT 1) inner_tab)) counter_table ;
```
4. 对于（在作为客户的整个时期内）总消费 (**total_amt_usd**) 最多的客户，他们在每个渠道上有多少 **web_events**？
``` sql
SELECT a.name account_name,
       w.channel,
       COUNT(*) num_event
FROM accounts a
JOIN web_events w
  ON w.account_id = a.id
GROUP BY 1,
         2
HAVING a.name =
  (SELECT name
   FROM
     (SELECT a.id,
             a.name,
             SUM(o.total_amt_usd) total_spend
      FROM orders o
      JOIN accounts a
        ON a.id = o.account_id
      GROUP BY 1,
               2
      ORDER BY 3 DESC LIMIT 1) inner_tab)
ORDER BY 3 DESC;
```
5. 对于总消费前十名的客户，他们的平均终身消费 (**total_amt_usd**) 是多少?
``` sql
SELECT AVG(total_spend) avg_total_spend
FROM
  (SELECT a.id,
          a.name,
          SUM(o.total_amt_usd) total_spend
   FROM orders o
   JOIN accounts a
     ON a.id = o.account_id
   GROUP BY 1,
            2
   ORDER BY 3 DESC LIMIT 10) inner_table;
```
>304846.969193
6. 比所有客户的平均消费高的企业平均终身消费 (**total_amt_usd**) 是多少？
``` sql
SELECT AVG(avg_amt)
FROM
  (SELECT o.account_id,
          AVG(o.total_amt_usd) avg_amt
   FROM orders o
   GROUP BY 1
   HAVING AVG(o.total_amt_usd) >
     (SELECT AVG(o.total_amt_usd) avg_all
      FROM orders o
      JOIN accounts a
        ON a.id = o.account_id)) temp_table;
```
>4721.1397406754

## 4. Common Table Expressions: WITH

**WITH** 语句经常称为**公用表表达式**（简称 **CTE**）。虽然这些表达式和子查询的目的完全一样，但是实际更常用，因为对未来的读者来说，更容易看懂其中的逻辑。

如果你有一个运行时间很长的公用表表达式或子查询，你可能需要将其作为完全独立的查询运行，将其写入数据库，然后你可以像查询其它表一样查询新表，以完成你的计算的内容，这种方法的最大好处是可以提升你的查询速度，假设编写一个子查询需要一个小时，那么你也会有一个编写用时少的外层查询，如果每次你想要对外层查询进行微调时 都要运行它们，你会觉得很烦躁，相反 你可以运行该内层查询一次 并将其写入表中，然后对外层查询进行迭代 这样会变得又快又容易，现在我们将重点介绍如何将子查询和公用表表达式，作为新表写入数据库。

子查询的一个问题，是它们让你的查询冗长且难以阅读，公用表表达式 (CTE)，可以将查询分解成单独的各小部分 使查询逻辑更易于阅读，你可以自己阅读子查询逻辑，然后轻松理解最终的查询逻辑。

我们回顾一下前面的例子，通过这个例子 我们了解了每个营销渠道每天的平均事件数量。

``` sql
SELECT channel,
       AVG(num_evnnts) avg_events
FROM
  (SELECT DATE_FORMAT(occurred_at, "%Y-%s-%d") event_date,
          channel,
          COUNT(*) num_evnnts
   FROM web_events
   GROUP BY 1,
            2
   ORDER BY 3) sub
GROUP BY 1
ORDER BY 2 DESC;
```

将子查询分解成它自己的 CTE，CTE 的创建将使用 WITH 指令，这里我们只是定义了这一个 CTE，但理论上我们可以写尽可能多的 CTE，我们需要在查询的开头定义所有，底部的最终查询会使用的CTE，每个 CTE 都像一个子查询一样得到一个别名，这里的别名是 `events`。

```sql
WITH events AS
  (SELECT DATE_FORMAT(occurred_at, "%Y-%s-%d") event_date,
          channel,
          COUNT(*) num_evnnts
   FROM web_events
   GROUP BY 1,
            2
   ORDER BY 3)
   
SELECT channel,
       AVG(num_evnnts) avg_events
FROM events
GROUP BY 1
ORDER BY 2 DESC;
```

### MySQL 使用 CTE

“公用表表达式”功能在MySQL 8.0版本以前不可用，因此要创建 VIEW（视图）或临时表来解决。存储过程也可实现此功能。

Mysql8.0版本支持[Common Table Expressions in MySQL (CTEs)](https://dev.mysql.com/doc/refman/8.0/en/with.html)。


- VIEW实现

``` sql
CREATE OR REPLACE VIEW events AS
  (SELECT DATE_FORMAT(occurred_at, "%Y-%s-%d") event_date,
          channel,
          COUNT(*) num_evnnts
   FROM web_events
   GROUP BY 1,
            2
   ORDER BY 3);

   
SELECT channel,
       AVG(num_evnnts) avg_events
FROM events
GROUP BY 1
ORDER BY 2 DESC;
```

### 使用 WITH 创建多个表格

``` sql
WITH table1 AS
    (SELECT *
   FROM web_events),
     table2 AS
  (SELECT *
   FROM accounts)


SELECT *
FROM table1
JOIN table2
  ON table1.account_id = table2.id;
```

在使用 WITH 创建多个表格时，需要每个表格后面加一个逗号，但是在引向最终查询的最后一个表格构面不需要添加。

新表格名称始终使用 `table_name AS` 设置别名，后面是位于小括号中的查询。

### Practice: WITH

1. 提供每个**区域**拥有最高销售额 (**total_amt_usd**) 的**销售代表**的**姓名**。
``` sql
 WITH t1 AS
    (SELECT s.name rep_name,
            r.name region_name,
            SUM(o.total_amt_usd) total_amt
   FROM sales_reps s
   JOIN accounts a
     ON a.sales_rep_id = s.id
   JOIN orders o
     ON o.account_id = a.id
   JOIN region r
     ON r.id = s.region_id
   GROUP BY 1,
            2
   ORDER BY 3 DESC),
      t2 AS
  (SELECT region_name,
          MAX(total_amt) total_amt
   FROM t1
   GROUP BY 1)
SELECT t1.rep_name,
       t1.region_name,
       t1.total_amt
FROM t1
JOIN t2
  ON t1.region_name = t2.region_name
  AND t1.total_amt = t2.total_amt;
```
2. 对于具有最高销售额 (**total_amt_usd**) 的区域，总共下了多少个订单 （**total** count orders） ？
``` sql
WITH t1 AS
  (SELECT r.name region_name,
          SUM(o.total_amt_usd) total_amt
   FROM sales_reps s
   JOIN accounts a
     ON a.sales_rep_id = s.id
   JOIN orders o
     ON o.account_id = a.id
   JOIN region r
     ON r.id = s.region_id
   GROUP BY r.name),
     t2 AS
  (SELECT MAX(total_amt)
   FROM t1)
SELECT r.name,
       SUM(o.total) total_orders
FROM sales_reps s
JOIN accounts a
  ON a.sales_rep_id = s.id
JOIN orders o
  ON o.account_id = a.id
JOIN region r
  ON r.id = s.region_id
GROUP BY r.name
HAVING SUM(o.total_amt_usd) =
  (SELECT *
   FROM t2);
```
3. 对于购买标准纸张数量 (**standard_qty**) 最多的客户（在作为客户的整个时期内），**有多少客户**的购买总数依然更多？
``` sql
 WITH t1 AS
  (SELECT a.name account_name,
          SUM(o.standard_qty) total_std,
          SUM(o.total) total
   FROM accounts a
   JOIN orders o
     ON o.account_id = a.id
   GROUP BY 1
   ORDER BY 2 DESC LIMIT 1),
      t2 AS
  (SELECT a.name
   FROM orders o
   JOIN accounts a
     ON a.id = o.account_id
   GROUP BY 1
   HAVING SUM(o.total) >
     (SELECT total
      FROM t1))
SELECT COUNT(*)
FROM t2;
```
4. 对于（在作为客户的整个时期内）总消费 (**total_amt_usd**) 最多的客户，他们在每个渠道上有多少 **web_events**？
``` sql
WITH t1 AS
  (SELECT a.id,
          a.name,
          SUM(o.total_amt_usd) tot_spent
   FROM orders o
   JOIN accounts a
     ON a.id = o.account_id
   GROUP BY a.id,
            a.name
   ORDER BY 3 DESC LIMIT 1)
SELECT a.name,
       w.channel,
       COUNT(*)
FROM accounts a
JOIN web_events w
  ON a.id = w.account_id
  AND a.id =
  (SELECT id
   FROM t1)
GROUP BY 1,
         2
ORDER BY 3 DESC;
```
5. 对于总消费前十名的客户，他们的平均终身消费 (**total_amt_usd**) 是多少?
``` sql
WITH t1 AS
  (SELECT a.id,
          a.name,
          SUM(o.total_amt_usd) tot_spent
   FROM orders o
   JOIN accounts a
     ON a.id = o.account_id
   GROUP BY a.id,
            a.name
   ORDER BY 3 DESC LIMIT 10)
SELECT AVG(tot_spent)
FROM t1;
```
>304846.969193

6. 比所有客户的平均消费高的企业平均终身消费 (**total_amt_usd**) 是多少？
``` sql
 WITH t1 AS
  (SELECT AVG(o.total_amt_usd) avg_all
   FROM orders o
   JOIN accounts a
     ON a.id = o.account_id),
      t2 AS
    (SELECT o.account_id,
            AVG(o.total_amt_usd) avg_amt
   FROM orders o
   GROUP BY 1
   HAVING AVG(o.total_amt_usd) >
     (SELECT *
      FROM t1))
SELECT AVG(avg_amt)
FROM t2;
```

## 5. VIEW 视图

**什么是视图？**

在 SQL 中，视图是基于 SQL 语句的结果集的可视化的表。

视图包含行和列，就像一个真实的表。视图中的字段就是来自一个或多个数据库中的真实的表中的字段。我们可以向视图添加 SQL 函数、WHERE 以及 JOIN 语句，我们也可以提交数据，就像这些来自于某个单一的表。

**注释：**数据库的设计和结构不会受到视图中的函数、where 或 join 语句的影响。
>[w3school SQL VIEW（视图）](https://www.w3school.com.cn/sql/sql_view.asp)

`VIEW`视图是存放数据的一个接口，也可以说是虚拟的表。这些数据可以是从一个或几个基本表（或视图）的数据。也可以是用户自已定义的数据。其实视图里面不存放数据的，数据还是放在基本表里面，基本表里面的数据发生变动时，视图里面的数据随之变动。

### 创建视图

``` sql
CREATE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition
```

### 更新视图

``` sql
CREATE OR REPLACE VIEW view_name AS
SELECT column_name(s)
FROM table_name
WHERE condition
```

### 删除视图

```sql
DROP VIEW view_name
```


### 语法

```
CREATE [OR REPLACE] [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
VIEW view_name [(column_list)]
AS select_statement
[WITH [CASCADED | LOCAL] CHECK OPTION]
```

- `OR REPLACE`：该语句能替换已有的视图
- `WITH CHECK OPTION`：对视图进行更新操作的时，需要检查更新后的值是否还是满足视图公式定义的条件。通俗点，就是所更新的结果是否还会在视图中存在。如果更新后的值不在视图范围内，就不允许更新如果创建视图的时候，没有加上with check option，更新视图中的某项数据的话，mysql并不会进行有效性检查。删掉了就删掉了。在视图中将看不到了。使用`WHIT [CASCADED|LOCAL] CHECK OPTION`选项可以保证数据的安全性

### 类型

mysql的视图有三种类型：`MERGE`、`TEMPTABLE`、`UNDEFINED`。如果没有`ALGORITHM`子句，默认算法是`UNDEFINED`（未定义的）。算法会影响MySQL处理视图的方式。

1. `MERGE`，会将引用视图的语句的文本与视图定义合并起来，使得视图定义的某一部分取代语句的对应部分。

2. `TEMPTABLE`，视图的结果将被置于临时表中，然后使用它执行语句。

3. `UNDEFINED`，MySQL将选择所要使用的算法。如果可能，它倾向于MERGE而不是TEMPTABLE，这是因为MERGE通常更有效，而且如果使用了临时表，视图是不可更新的。

**注意**
`VIEW` 中 `FROM`不支持子查询。

   ```sql
DROP VIEW IF EXISTS view_name;


CREATE VIEW view_name AS
SELECT column_name(s)
FROM
  (SELECT *
   FROM tables) subqueries
WHERE condition;
   ```

   > 错误＃1349：View的select cluse在from子句中包含一个子查询 查询出错 (1349): View’s SELECT contains a subquery in the FROM clause

解决：将子查询改为`VIEW`






