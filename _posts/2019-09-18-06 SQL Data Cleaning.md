---
layout: post
title: "「SQL For Data Analysis」06 SQL Data Cleaning"
subtitle: 'SQL 数据清洗'
author: "Hufe"
header-img: "img/post-bg-mysql.jpg"
header-mask: 0.3
tags:
  - SQL For Data Analysis
  - SQL
---

# SQL Data Cleaning 数据清洗

## 目标：

1. 清理和重新整理混乱的数据。
2. 将列转换为不同的数据类型。
3. 处理 **NULL** 的技巧。

## 1. LEFT 与 RIGHT
使用字符串函数进行清理

**查看下面三个函数**

1. **LEFT**
2. **RIGHT**
3. **LENGTH**

- **LEFT** 从起点（或左侧）开始，从特定列中的每行获取一定数量的字符。正如此处看到的，你可以使用 **LEFT(phone_number, 3)** 获取电话号码中的前三位。
- **RIGHT** 从末尾（或右侧）开始，从特定列中的每行获取一定数量的字符。正如此处看到的，你可以使用 **RIGHT(phone_number, 8)** 获取电话号码的最后 8 位。
- **LENGTH** 提供了特定列每行的字符数。这里，我们可以使用 **LENGTH(phone_number)** 得出每个电话号码的长度。

![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.va88a84ci.png)

### LEFT & RIGHT Practice

1. 在 **accounts** 表格中，有一个列存储的是每个公司的**网站**。最后三个数字表示他们使用的是什么类型的网址。[此处](https://iwantmyname.com/domains/domain-name-registration-list-of-extensions)给出了扩展（和价格）列表。请获取这些扩展并得出 **accounts** 表格中每个网址类型的存在数量。

``` sql
SELECT RIGHT(website, 3),
       COUNT(*)
FROM accounts
GROUP BY 1;
```

2. 对于公司名称[（甚至名称的第一个字母）](<https://www.entrepreneur.com/article/237643>)的作用存在颇多争议。请从 **accounts** 表格中获取每个公司名称的第一个字母，看看以每个字母（数字）开头的公司名称分布情况。

``` sql
SELECT LEFT(name, 1),
       count(*)
FROM accounts
GROUP BY 1;
```

3. 使用 **accounts** 表格和 **CASE** 语句创建两个群组：一个是以数字开头的公司名称群组，另一个是以字母开头的公司名称群组。以字母开头的公司名称所占的比例是多少？

``` sql
SELECT
	SUM( num ) nums,
	SUM( letter ) letters 
FROM
	(
SELECT
	`name`,
CASE WHEN 
        LEFT ( UPPER( `name` ), 1 ) IN ( '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' ) THEN 1 
        ELSE 0 END AS num,
CASE WHEN 
        LEFT ( UPPER( `name` ), 1 ) IN ( '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' ) THEN 0 
        ELSE 1 END AS letter 
FROM
	accounts 
) t1;
```
4. 元音是指 `a`、`e`、`i`、`o` 和 `u`。有多少比例的公司名称以元音开头，以其他音节开头的公司名称百分比是多少？

``` sql
SELECT SUM(vowels) vowels,
       SUM(other) other
FROM
  (SELECT name,
          CASE
              WHEN LEFT(UPPER(name), 1) IN ('A',
                                            'E',
                                            'I',
                                            'O',
                                            'U') THEN 1
              ELSE 0
          END AS vowels,
          CASE
              WHEN LEFT(UPPER(name), 1) IN ('A',
                                            'E',
                                            'I',
                                            'O',
                                            'U') THEN 0
              ELSE 1
          END AS other
   FROM accounts) t1;
```

>有80 的公司名称以元音开头，271 的公司以其他音节开头。元音的比例是 80/351，或 22.8%。因此，有 77.2% 的公司名称没有以元音开头。


## 2.POSITION、STRPOS 和 SUBSTR
更高级的字符串函数

1. **POSITION**
2. **STRPOS**
3. **LOWER**
4. **UPPER**

**POSITION** 获取字符和列，并提供该字符在每行的索引。第一个位置的索引在 SQL 中是 1。

如果你之前学习了其他编程语言，就会发现很多语言的索引是从 0 开始。这里，你发现可以使用 **POSITION(',' IN city_state)** 获取逗号的索引。

**STRPOS** 和 **POSITION** 提供的结果相同，但是语法不太一样，如下所示：**STRPOS(city_state, ‘,’)**。

![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.krnf2b9uehh.png)

> 注意，**POSITION** 和 **STRPOS** 都区分大小写，因此查找 **A** 的位置与查找 **a** 的结果不同。
>
> 因此，如果你想获取某个字母的索引，但是不区分大小写，则需要使用 **LOWER** 或 **UPPER** 让所有字符变成小写或大写。

POSITION、STRPOS 和 SUBSTR

1. 使用 `accounts` 表格创建一个**名字**和**姓氏**列，用于存储 `primary_poc` 的名字和姓氏。

``` sql
SELECT LEFT(primary_poc, STRPOS(primary_poc, ' ') -1) first_name,
       RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name
FROM accounts;
```

2. 现在创建一个包含 `sales_rep` 表格中每个销售代表姓名的列，同样，需要提供名字和姓氏列。
``` sql
SELECT LEFT(`name`, STRPOS(`name`, ' ') -1) first_name,
       RIGHT(`name`, LENGTH(`name`) - STRPOS(`name`, ' ')) last_name
FROM sales_reps;
```


## 3. MySQL 子字符串匹配：POSITION、LOCATE 和 INSTR

**POSITION(',' IN city_state)**  获取逗号的索引

**LOCATE( ', ', city_state )** 获取逗号的索引

**INSTR(city_state, ',')** 获取逗号的索引

```  mysql
SELECT
	`name`,
	POSITION( ' ' IN `name` ),
	LOCATE( ' ', `name` ),
	INSTR( `name`, ' ' ),
	LEFT ( `name`, INSTR( `name`, ' ' ) - 1 ) first_name,
	RIGHT ( `name`, LENGTH( `name` ) - INSTR( `name`, ' ' ) ) last_name 
FROM
	sales_reps;
```

![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.nayu6cdoup8.png)



| name            | POSITION(' ' IN `name`) | LOCATE(' ', `name`) | INSTR(`name`, ' ') | first_name | last_name |
| --------------- | ----------------------- | ------------------- | ------------------ | ---------- | --------- |
| Samuel Racine   | 7                       | 7                   | 7                  | Samuel     | Racine    |
| Renetta Carew   | 8                       | 8                   | 8                  | Renetta    | Carew     |
| Cara Clarke     | 5                       | 5                   | 5                  | Cara       | Clarke    |
| Shawanda Selke  | 9                       | 9                   | 9                  | Shawanda   | Selke     |
| Sibyl Lauria    | 6                       | 6                   | 6                  | Sibyl      | Lauria    |

## 4. MySQL 全匹配：FIND_IN_SET 和 FIELD

**FIND_IN_SET('mysql','oracle,sql server,mysql,db2')** 获取 'mysql' 在序列 **<'oracle,sql server,mysql,db2'>**  中的索引

**FIELD('mysql', 'oracle', 'sql server', 'mysql', 'db2')** 获取 'mysql' 在序列 **<'oracle', 'sql server', 'mysql', 'db2'>** 中的索引

``` mysql
SELECT FIND_IN_SET('mysql','oracle,sql server,mysql,db2'),
       FIND_IN_SET('oracle','oracle,sql server,mysql,db2'),
       FIND_IN_SET('sql','oracle,sql server,mysql,db2') ;
```

| FIND_IN_SET('mysql','oracle,sql   server,mysql,db2') | FIND_IN_SET('oracle','oracle,sql   server,mysql,db2') | FIND_IN_SET('sql','oracle,sql   server,mysql,db2') |
| ---------------------------------------------------- | ----------------------------------------------------- | -------------------------------------------------- |
| 3                                                    | 1                                                     | 0                                                  |

``` mysql
SELECT FIELD('mysql', 'oracle', 'sql server', 'mysql', 'db2'),
       FIELD('oracle','oracle', 'sql server', 'mysql', 'db2'),
       FIELD('sql', 'oracle', 'sql server', 'mysql', 'db2');
```

| FIELD('mysql',   'oracle', 'sql server', 'mysql', 'db2') | FIELD('oracle','oracle', 'sql server',   'mysql', 'db2') | FIELD('sql', 'oracle', 'sql server', 'mysql',   'db2') |
| -------------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------ |
| 3                                                        | 1                                                        | 0                                                      |

> 索引从1开始，没有匹配项结果为0

## 5. MySQL 根据位置找字符串：ELT 和 MAKE_SET

**ETL 函数返回指定位置的字符串**

**MAKE_SET 则更强大，将换算二进制之后为条件进行选择。1为选择，0不选择。**

``` mysql
SELECT ELT(2, 'oracle', 'sql server', 'mysql', 'db2'),
       BIN(5),
       MAKE_SET(5, 'oracle', 'sql server', 'mysql', 'db2');
```

| ELT(2, 'oracle',   'sql server', 'mysql', 'db2') | BIN(5) | MAKE_SET(5, 'oracle', 'sql server', 'mysql',   'db2') |
| ------------------------------------------------ | ------ | ----------------------------------------------------- |
| sql server                                       | 101    | oracle,mysql                                          |

> 将5转换为二进制位101, 第一个选择，第二个不选择，第三个选择。。。

## 6. 字符拼接 CONCAT 和 Piping `||`

1. **CONCAT**

2. **Piping  `||`**

这两个工具都能将不同行的列组合到一起。在此视频中，你学习了如何将存储在不同列中的名字和姓氏组合到一起，形成全名：**CONCAT(first_name, ' ', last_name)**，或者使用双竖线：**first_name || ' ' || last_name**。

> MySQL 不支持 `||`

### CONCAT Practice

1. `accounts` 表格中的每个客户都想为每个 `primary_poc` 创建一个电子邮箱。邮箱应该是 **primary_poc** 的名字.**primary_poc**的姓氏@公司名称.com。
``` sql
WITH t1 AS
  ( SELECT LEFT(primary_poc, STRPOS(primary_poc, ' ') -1) first_name,
           RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name,
           name
   FROM accounts)
SELECT first_name,
       last_name,
       CONCAT(first_name, '.', last_name, '@', name, '.com')
FROM t1;
```
2. 你可能注意到了，在上一个答案中，有些公司名称存在空格，肯定不适合作为邮箱地址。看看你能否通过删掉客户名称中的所有空格来创建合适的邮箱地址，否则你的答案就和问题 1. 的一样。[此处](https://www.postgresql.org/docs/8.1/static/functions-string.html)是一些实用的文档。
``` sql
WITH t1 AS
  ( SELECT LEFT(primary_poc, STRPOS(primary_poc, ' ') -1) first_name,
           RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name,
           name
   FROM accounts)
SELECT first_name,
       last_name,
       CONCAT(first_name, '.', last_name, '@', REPLACE(name, ' ', ''), '.com')
FROM t1;
```
3. 我们还需要创建初始密码，在用户第一次登录时将更改。初始密码将是 `primary_poc` 的名字的第一个字母（小写），然后依次是名字的最后一个字母（小写）、姓氏的第一个字母（小写）、姓氏的最后一个字母（小写）、名字的字母数量、姓氏的字母数量，然后是合作的公司名称（全大写，没有空格）
``` sql
WITH t1 AS
  ( SELECT LEFT(primary_poc, STRPOS(primary_poc, ' ') -1) first_name,
           RIGHT(primary_poc, LENGTH(primary_poc) - STRPOS(primary_poc, ' ')) last_name,
           name
   FROM accounts)
SELECT first_name,
       last_name,
       CONCAT(first_name, '.', last_name, '@', name, '.com'),
       LEFT(LOWER(first_name), 1) || RIGHT(LOWER(first_name), 1) || LEFT(LOWER(last_name), 1) || RIGHT(LOWER(last_name), 1) || LENGTH(first_name) || LENGTH(last_name) || REPLACE(UPPER(name), ' ', '')
FROM t1;
```



MySQL 版解答：

创建 VIEW 将名字分为 **姓，名，全名** 三列

``` mysql
-- DROP VIEW IF EXISTS t1;

CREATE VIEW t1 AS (
SELECT LEFT
	( primary_poc, POSITION( ' ' IN primary_poc ) - 1 ) first_name,
	RIGHT ( primary_poc, LENGTH( primary_poc ) - POSITION( ' ' IN primary_poc ) ) last_name,
	`name`
	
FROM
	accounts 
	);
```
1. 

``` mysql
SELECT first_name,
       last_name,
       CONCAT(first_name, '.', last_name, '@', `name`, '.com')
FROM t1;
```
2. 
``` mysql
SELECT first_name,
       last_name,
       CONCAT(first_name, '.', last_name, '@', REPLACE(`name`, ' ', ''), '.com')
FROM t1;
```
3. 
``` mysql
SELECT CONCAT(last_name, '.', first_name, '@', REPLACE(`name`, ' ', ''), '.com') mail,
       CONCAT(LEFT (last_name, 1), RIGHT (last_name, 1), LEFT (first_name, 1), RIGHT (first_name, 1), LENGTH(last_name), LENGTH(first_name), UPPER(REPLACE(`name`, ' ', ''))) AS `password`
FROM t1;
```

![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.tnj2n2v7p5.png)



## 7. COALESCE 处理NULL值

```sql
SELECT *,
       COALESCE(primary_poc, 'no POC') AS primary_poc_modified
FROM accounts
WHERE primary_poc IS NULL;
```
| id   | name                            | website                 | lat      | long     | primary_poc | sales_rep_id | primary_poc_modified |
| ---- | ------------------------------- | ----------------------- | -------- | -------- | ----------- | ------------ | -------------------- |
| 1501 | Intel                           | www.intel.com           | 41.03154 | -74.6685 |             | 321580       | no POC               |
| 1671 | Delta Air Lines                 | www.delta.com           | 40.75861 | -73.9907 |             | 321510       | no POC               |
| 1951 | Twenty-First Century Fox        | www.21cf.com            | 42.35468 | -71.0548 |             | 321560       | no POC               |
| 2131 | USAA                            | www.usaa.com            | 41.87745 | -87.6279 |             | 321780       | no POC               |
| 2141 | Duke Energy                     | www.duke-energy.com     | 41.87751 | -87.6275 |             | 321790       | no POC               |
| 2151 | Time Warner Cable               | www.twc.com             | 41.87656 | -87.6327 |             | 321710       | no POC               |
| 2611 | Southern                        | www.southerncompany.com | 33.79735 | -84.4878 |             | 321870       | no POC               |
| 2931 | Western Digital                 | www.westerndigital.com  | 33.77162 | -84.2944 |             | 321890       | no POC               |
| 3711 | Public Service Enterprise Group | www.pseg.com            | 34.05599 | -118.251 |             | 321910       | no POC               |
| 4471 | Oneok                           | www.oneok.com           | 45.51351 | -122.682 |             | 321960       | no POC               |

``` sql
SELECT COUNT(primary_poc) AS regular_count,
       COUNT(COALESCE(primary_poc, 'no POC')) AS modified_count
FROM accounts;
```

| regular_count | modified_count |
| ------------- | -------------- |
| 341           | 351            |

### COALESCE Practice

 ![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.lu95g6xhof.png)

1. 

``` sql
SELECT *
FROM accounts a
LEFT JOIN orders o
  ON a.id = o.account_id
  WHERE o.total IS NULL;
```
2. 
``` sql
SELECT COALESCE(a.id, a.id) filled_id,
       a.name,
       a.website,
       a.lat,
       a.long,
       a.primary_poc,
       a.sales_rep_id,
       o.*
FROM accounts a
LEFT JOIN orders o
  ON a.id = o.account_id
  WHERE o.total IS NULL;
```
3. 
``` sql
  SELECT COALESCE(a.id, a.id) filled_id,
         a.name,
         a.website,
         a.lat,
         a.long,
         a.primary_poc,
         a.sales_rep_id,
         COALESCE(o.account_id, a.id) account_id,
         o.occurred_at,
         o.standard_qty,
         o.gloss_qty,
         o.poster_qty,
         o.total,
         o.standard_amt_usd,
         o.gloss_amt_usd,
         o.poster_amt_usd,
         o.total_amt_usd
FROM accounts a
LEFT JOIN orders o
  ON a.id = o.account_id
  WHERE o.total IS NULL;
```
4. 
``` sql
  SELECT COALESCE(a.id, a.id) filled_id,
         a.name,
         a.website,
         a.lat,
         a.long,
         a.primary_poc,
         a.sales_rep_id,
         COALESCE(o.account_id, a.id) account_id,
         o.occurred_at,
         COALESCE(o.standard_qty, 0) standard_qty,
         COALESCE(o.gloss_qty,0) gloss_qty,
         COALESCE(o.poster_qty,0) poster_qty,
         COALESCE(o.total,0) total,
         COALESCE(o.standard_amt_usd,0) standard_amt_usd,
         COALESCE(o.gloss_amt_usd,0) gloss_amt_usd,
         COALESCE(o.poster_amt_usd,0) poster_amt_usd,
         COALESCE(o.total_amt_usd,0) total_amt_usd
FROM accounts a
LEFT JOIN orders o
  ON a.id = o.account_id
  WHERE o.total IS NULL;
```
5. 
``` sql
  SELECT COUNT(*)
FROM accounts a
LEFT JOIN orders o
  ON a.id = o.account_id;
```
>6913
6. 
``` sql
  SELECT COALESCE(a.id, a.id) filled_id,
         a.name,
         a.website,
         a.lat,
         a.long,
         a.primary_poc,
         a.sales_rep_id,
         COALESCE(o.account_id, a.id) account_id,
         o.occurred_at,
         COALESCE(o.standard_qty, 0) standard_qty,
         COALESCE(o.gloss_qty,0) gloss_qty,
         COALESCE(o.poster_qty,0) poster_qty,
         COALESCE(o.total,0) total,
         COALESCE(o.standard_amt_usd,0) standard_amt_usd,
         COALESCE(o.gloss_amt_usd,0) gloss_amt_usd,
         COALESCE(o.poster_amt_usd,0) poster_amt_usd,
         COALESCE(o.total_amt_usd,0) total_amt_usd
FROM accounts a
LEFT JOIN orders o
  ON a.id = o.account_id;
```

## 8. CAST

**转换数据类型**

1. **TO_DATE**
2. **CAST**
3. **使用 :: 进行转型**

**DATE_PART('month', TO_DATE(month, 'month'))** 将月份名称改成了与该月相关的数字。

然后，可以使用 **CAST** 将字符串改为日期。**CAST** 实际上可以用来更改各种列类型。经常，会使用 **CAST(date_column AS DATE)** 将字符串改成日期。但是，可能还会对列的数据类型做出其他更改。可以在[此处](http://www.postgresqltutorial.com/postgresql-cast/)看到其他例子。

在此示例中，除了 **CAST(date_column AS DATE)** 之外，可以使用 **date_column::DATE**。

![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.xsbkqwz9s9e.png)

### MySQL CAST

**CAST( value AS type )**

| value    | 描述                                            |
| -------- | ----------------------------------------------- |
| DATE     | 日期，格式为 'YYYY-MM-DD'.                      |
| DATETIME | 日期加具体的时间，格式为 'YYYY-MM-DD HH:MM:SS'. |
| TIME     | 时间，格式为 'HH:MM:SS'.                        |
| CHAR     | 字符型                                          |
| SIGNED   | int                                             |
| UNSIGNED | 无符号int                                       |
| BINARY   | 二进制型                                        |
| DECIMAL  | float型                                         |

``` mysql
SELECT *,
       CAST(CAST_DATETIME AS TIME) CAST_TIME_SUB,
       CAST(CAST_DATETIME AS SIGNED) CAST_SIGNED_SUB,
       CAST(2019.192727 AS DECIMAL (7, 3)) CAST_DECIMAL
FROM
  (SELECT occurred_at,
          CONCAT(SUBSTR(occurred_at, 1, 4), '-', SUBSTR(occurred_at, 6, 2), '-', SUBSTR(occurred_at, 9, 2)) CONCAT_DATE,
          CAST(occurred_at AS CHAR) CAST_CHAR,
          CAST(occurred_at AS DATE) CAST_DATE,
          CAST(occurred_at AS DATETIME) CAST_DATETIME,
          CAST(occurred_at AS SIGNED) CAST_SIGNED,
          CAST(occurred_at AS TIME) CAST_TIME
   FROM orders) t1 LIMIT 5 ;
```

| occurred_at              | CONCAT_DATE | CAST_CHAR                | CAST_DATE      | CAST_DATETIME   | CAST_SIGNED | CAST_TIME | CAST_TIME_SUB | CAST_SIGNED_SUB | CAST_DECIMAL |
| ------------------------ | ----------- | ------------------------ | -------------- | --------------- | ----------- | --------- | ------------- | --------------- | ------------ |
| 2015-10-06T17:31:14.000Z | 2015/10/6   | 2015-10-06T17:31:14.000Z | 2015/10/6 0:00 | 2015/10/6 17:31 | 2015        | 0:20:15   | 17:31:14      | 20151006173114  | 2019.193     |
| 2015-11-05T03:34:33.000Z | 2015/11/5   | 2015-11-05T03:34:33.000Z | 2015/11/5 0:00 | 2015/11/5 3:34  | 2015        | 0:20:15   | 3:34:33       | 20151105033433  | 2019.193     |
| 2015-12-04T04:21:55.000Z | 2015/12/4   | 2015-12-04T04:21:55.000Z | 2015/12/4 0:00 | 2015/12/4 4:21  | 2015        | 0:20:15   | 4:21:55       | 20151204042155  | 2019.193     |
| 2016-01-02T01:18:24.000Z | 2016/1/2    | 2016-01-02T01:18:24.000Z | 2016/1/2 0:00  | 2016/1/2 1:18   | 2016        | 0:20:16   | 1:18:24       | 20160102011824  | 2019.193     |
| 2016-02-01T19:27:27.000Z | 2016/2/1    | 2016-02-01T19:27:27.000Z | 2016/2/1 0:00  | 2016/2/1 19:27  | 2016        | 0:20:16   | 19:27:27      | 20160201192727  | 2019.193     |

## 9. 总结

MySQL字符串的拼接、截取、替换、查找位置。

**常用的字符串函数：**

<table border="1" cellpadding="1" cellspacing="1">
    <thead>
        <tr>
        <th scope="col" style="width:298px;">函数</th>
		<th scope="col" style="width:551px;">说明</th>
		</tr>
    </thead>
    <tbody>
        <tr>
            <td style="width:298px;">CONCAT(s1,s2，...)</td>
			<td style="width:551px;">返回连接参数产生的字符串，一个或多个待拼接的内容，任意一个为NULL则返回值为NULL。</td>
		</tr>
        <tr>
            <td style="width:298px;">CONCAT_WS(x,s1,s2,...)</td>
			<td style="width:551px;">返回多个字符串拼接之后的字符串，每个字符串之间有一个x。</td>
		</tr>
        <tr>
            <td style="width:298px;">SUBSTRING(s,n,len)、MID(s,n,len)</td>
			<td style="width:551px;">两个函数作用相同，从字符串s中返回一个第n个字符开始、长度为len的字符串。</td>
		</tr>
        <tr>
            <td style="width:298px;">LEFT(s,n)、RIGHT(s,n)</td>
			<td style="width:551px;">前者返回字符串s从最左边开始的n个字符，后者返回字符串s从最右边开始的n个字符。</td>
		</tr>
        <tr>
            <td style="width:298px;">INSERT(s1,x,len,s2)</td>
			<td style="width:551px;">返回字符串s1，其子字符串起始于位置x，被字符串s2取代len个字符。</td>
		</tr>
        <tr>
            <td style="width:298px;">REPLACE(s,s1,s2)</td>
			<td style="width:551px;">返回一个字符串，用字符串s2替代字符串s中所有的字符串s1。</td>
		</tr>
        <tr>
            <td style="width:298px;">LOCATE(str1,str)、POSITION(str1 IN str)、INSTR(str,str1)</td>
			<td style="width:551px;">三个函数作用相同，返回子字符串str1在字符串str中的开始位置（从第几个字符开始）。</td>
		</tr>
        <tr>
            <td style="width:298px;">FIELD(s,s1,s2,...)</td>
			<td style="width:551px;">返回第一个与字符串s匹配的字符串的位置。</td>
		</tr>
<tr>
    <td style="width:298px;">COALESCE(column_name, replacement_value_of_Null)</td>
			<td style="width:551px;">将此列中NULL替换。</td>
		</tr>
		</tbody>
</table>




