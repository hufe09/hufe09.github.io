---
layout: post
title: "用MySQL VIEW拼出一张表"
subtitle: ''
author: "Hufe"
header-img: "img/post-bg-mysql.jpg"
header-mask: 0.3
tags:
  - MySQL
---

> 背景：为比赛做一份统计信息表格，内容包括每个商店的总粉丝数，总海报浏览量，以及近三天的日增粉丝数，每日浏览量。

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553960194588-1553960194591.png)

## 1. 初始化一张新表
```
DROP TABLE
IF
	EXISTS `matchData`;
CREATE TABLE `matchData` (
dianpu_id INT NULL,
dianpu_name VARCHAR ( 255 ) COLLATE utf8_bin NULL,
totalFans INT DEFAULT 0 NULL,
todayFans INT DEFAULT 0 NULL,
yesterdayFans INT DEFAULT 0 NULL,
beforeYesterdayFans INT DEFAULT 0 NULL,
totalStatistics INT DEFAULT 0 NULL,
todayStatistics INT DEFAULT 0 NULL,
yesterdayStatistics INT DEFAULT 0 NULL,
beforeYesterdayStatistics INT DEFAULT 0 NULL 
);
```
![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553957733824-1553957733827.png)


## 2. 查询出参加比赛店铺列表id，存入id_list_temp VIEW 
为了后面不重复查询id列表，此处将id列表先保存在VIEW中
```
CREATE OR REPLACE VIEW id_list_temp AS (
SELECT
	dianpu_id 
FROM
	dc_dianpu 
WHERE
	dianpu_id IN (459,397,442,391，418) 
	);
```
## 3. 将店铺ID,店铺名增加到matchData
```
INSERT INTO matchData ( dianpu_id, dianpu_name ) SELECT
dianpu_id,
dianpu_name 
FROM
	dc_dianpu 
WHERE
	dianpu_id IN ( SELECT * FROM id_list_temp );
```

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553958020637-1553958020641.png)

##  4. 查询出比赛店铺三天内每天的粉丝，存入fans_sub VIEW
```
CREATE 
	OR REPLACE VIEW fans_sub AS (
SELECT
	d.dianpu_id,
	d.dianpu_name,
	FROM_UNIXTIME( time, '%Y-%m-%d' ) time2,
	count( DISTINCT openId ) fans 
FROM
	dc_dianpu d
	JOIN openid o ON o.dianpu_id = d.dianpu_id 
WHERE
	DATE_SUB( CURDATE( ), INTERVAL 2 DAY ) <= DATE( FROM_UNIXTIME( time, '%Y-%m-%d' ) ) 
	AND d.dianpu_id IN ( SELECT * FROM id_list_temp ) 
	AND o.isSubscribe = 1 
GROUP BY
	1,
	2,
	3 
ORDER BY
	3 DESC 
	);
```
![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553958801978-1553958801982.png)

##  5. 行列转换，按日期将粉丝数汇总，存入fans_temp VIEW

```
CREATE 
	OR REPLACE VIEW fans_temp AS (
SELECT
	dianpu_id dianpu_id1,
	SUM( IF ( time2 = CURDATE( ), fans, 0 ) ) AS todayFans,
	SUM( IF ( time2 = DATE_SUB( CURDATE( ), INTERVAL 1 DAY ), fans, 0 ) ) AS yesterdayFans,
	SUM( IF ( time2 = DATE_SUB( CURDATE( ), INTERVAL 2 DAY ), fans, 0 ) ) AS beforeYesterdayFans 
FROM
	fans_sub 
GROUP BY
	1 
ORDER BY
	2 DESC 
	);
```
![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553958911425-1553958911430.png)

##  6. 查询出比赛店铺三天内每天的浏览量，存入statistics_sub VIEW
```
CREATE 
	OR REPLACE VIEW statistics_sub AS (
SELECT
	d.dianpu_id,
	FROM_UNIXTIME( s.time, '%Y-%m-%d' ) dates,
	SUM( s.menuStatistical ) + SUM( s.posterStatistical ) totalStatistics 
FROM
	statistical s
	JOIN dc_dianpu d ON d.dianpu_id = s.dianpu_id 
WHERE
	DATE_SUB( CURDATE( ), INTERVAL 2 DAY ) <= DATE( FROM_UNIXTIME( time, '%Y-%m-%d' ) ) 
	AND d.dianpu_id IN ( SELECT * FROM id_list_temp ) 
GROUP BY
	1,
	2 
ORDER BY
	3 DESC 
	);
```
![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553959803390-1553959803394.png)

## 7.行列转换，按日期将浏览量汇总，存入statistics_temp VIEW
```
CREATE 
	OR REPLACE VIEW statistics_temp AS (
SELECT
	dianpu_id dianpu_id2,
	SUM( IF ( dates = CURDATE( ), totalStatistics, 0 ) ) AS todayStatistics,
	SUM( IF ( dates = DATE_SUB( CURDATE( ), INTERVAL 1 DAY ), totalStatistics, 0 ) ) AS yesterdayStatistics,
	SUM( IF ( dates = DATE_SUB( CURDATE( ), INTERVAL 2 DAY ), totalStatistics, 0 ) ) AS beforeYesterdayStatistics 
FROM
	statistics_sub 
GROUP BY
	1 
	);
```
![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553959839140-1553959839143.png)

## 8. 查询出总粉丝数，存入total_fans VIEW
```
CREATE 
	OR REPLACE VIEW total_fans AS (
SELECT
	dianpu_id,
	count( DISTINCT openId ) totalFans 
FROM
	openid 
WHERE
	isSubscribe = 1 
	AND dianpu_id IN ( SELECT * FROM id_list_temp ) 
GROUP BY
	1 
	);
```
![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553959963909-1553959963914.png)

## 9. 查询出总浏览量，存入total_statistics VIEW
```
CREATE 
	OR REPLACE VIEW total_statistics AS ( 
	SELECT dianpu_id, 
	SUM( menuStatistical + posterStatistical ) totalStatistics 
	FROM statistical 
	WHERE dianpu_id 	IN ( SELECT * FROM id_list_temp ) GROUP BY 1 );
	
```

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553959982769-1553959982782.png)
	
## 10. 更新粉丝表到matchData
```
UPDATE matchData,
fans_temp 
SET matchData.todayFans = fans_temp.todayFans,
matchData.yesterdayFans = fans_temp.yesterdayFans,
matchData.beforeYesterdayFans = fans_temp.beforeYesterdayFans 
WHERE
	matchData.dianpu_id = fans_temp.dianpu_id1;
```

## 11. 更新浏览量表到matchData
```
SET sql_mode = '';
UPDATE matchData,
statistics_temp 
SET matchData.todayStatistics = statistics_temp.todayStatistics,
matchData.yesterdayStatistics = statistics_temp.yesterdayStatistics,
matchData.beforeYesterdayStatistics = statistics_temp.beforeYesterdayStatistics 
WHERE
	matchData.dianpu_id = statistics_temp.dianpu_id2;
```
> 在代码执行过程中，遇到1366的错误，其原因是statistics_temp有一批新添加的数据为null
>`1366 - Incorrect DECIMAL value: '0' for column '' at row -1`
>解决方法：暂时改变sql_model
>参考：[解决1366错误](https://blog.csdn.net/weiwoyonzhe/article/details/85177294)
## 12. 更新总粉丝到matchData
```
UPDATE matchData,
total_fans 
SET matchData.totalFans = total_fans.totalFans
WHERE
	matchData.dianpu_id = total_fans.dianpu_id;
```

## 13. 更新总浏览量到matchData
```
UPDATE matchData,
total_statistics 
SET matchData.totalStatistics = total_statistics.totalStatistics
WHERE
	matchData.dianpu_id = total_statistics.dianpu_id;
```

## 14. 输出结果
```
SELECT
	dianpu_id `店铺ID`,
	dianpu_name 店铺名,
	totalFans 总粉丝,
	todayFans 今日增粉,
	yesterdayFans 昨日增粉,
	beforeYesterdayFans 前天增粉,
	totalStatistics 总浏览量,
	todayStatistics 今日浏览量,
	yesterdayStatistics 昨日浏览量,
	beforeYesterdayStatistics 前天浏览量 
FROM
	matchData 
ORDER BY
	3 DESC;
```

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1553960194588-15539601945911.png)

## 15. `VIEW`
`VIEW`视图是存放数据的一个接口，也可以说是虚拟的表。这些数据可以是从一个或几个基本表（或视图）的数据。也可以是用户自已定义的数据。其实视图里面不存放数据的，数据还是放在基本表里面，基本表里面的数据发生变动时，视图里面的数据随之变动。
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
### 注意
`VIEW`中`FROM`不支持子查询。
```
DROP VIEW
IF
	EXISTS total_temp;
CREATE VIEW total_temp AS (
SELECT
	sub1.dianpu_id dianpu_id2,
	sub2.totalFans totalFans,
	SUM( sub1.menuStatistical ) + SUM( sub1.posterStatistical ) totalStatistics 
FROM
	( SELECT * FROM statistical ) sub1
	JOIN ( SELECT dianpu_id, count( DISTINCT openId ) totalFans FROM openid WHERE isSubscribe = 1 GROUP BY 1 ) sub2 ON sub1.dianpu_id = sub2.dianpu_id
	JOIN dc_dianpu ON dc_dianpu.dianpu_id = sub1.dianpu_id 
WHERE
	sub1.dianpu_id IN ( SELECT * FROM id_list_temp ) 
GROUP BY
	1 
	);
```
>错误＃1349：View的select cluse在from子句中包含一个子查询
查询出错 (1349): View's SELECT contains a subquery in the FROM clause

解决：将子查询改为`VIEW`
