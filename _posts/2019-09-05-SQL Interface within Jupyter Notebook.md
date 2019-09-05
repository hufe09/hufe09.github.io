---
layout: post
title: "SQL Interface within Jupyter Notebook"
subtitle: "IPython 或 IPython Notebook 中执行 SQL 命令"
author: "Hufe"
header-img: "img/post-bg-mysql.jpg"
header-mask: 0.3
tags:
  - Python
  - SQL
  - Jupyter Tools
  - MySQL

---

大多数情况下，我们使用的数据存储在称为数据库的文件中，而数据科学家的一项基本任务是能够从数据库访问数据然后对其进行分析。因此，在 SQL 数据库和 Jupyter Notebook / Lab之间建立无缝接口是一个好主意，这样访问和操作数据变得更容易，更有效。

Jupyter Notebook / Lab 中使用 **ipython-sql** 结合 `%sql (or %%sql) ` 魔术命令执行 SQL 命令。

[ipython-sql Github](https://github.com/catherinedevlin/ipython-sql)

## 在 Notebook 中安装 ipython-sql
```
$ pip install ipython-sql
# Successfully installed ipython-sql-0.3.9 sqlparse-0.3.0
```

## 加载SQL模块


```python
%load_ext sql
```

如果后面执行 SQL 语句时，连接后较长时间未进行操作，出现如下错误：
```
TimeoutError: [WinError 10060] 由于连接方在一段时间后没有正确答复或连接的主机没有反应，连接尝试失败。
```
使用 `%reload_ext sql` 重新加载一次即可。

## 连接数据库

上面的 magic 命令加载 ipython-sql 扩展名。我们可以连接到 SQLAlchemy 支持的任何数据库。在这里，我们将连接到 SQLite 数据库。在代码单元格中输入以下命令：


```python
%sql sqlite://
```


    'Connected: @None'

如果获得输出 `'Connected: @None'`，则表示已建立连接。 

- 创建数据库


```python
%%sql   
CREATE TABLE EMPLOYEE(firstname varchar(50),lastname varchar(50));  
INSERT INTO EMPLOYEE VALUES('Tom','Mitchell');  
INSERT INTO EMPLOYEE VALUES('Jack','Ryan');
```

     * sqlite://
    Done.
    1 rows affected.
    1 rows affected.
    
    []



- 执行查询


```python
%sql SELECT * from EMPLOYEE;
```

     * sqlite://
    Done.

<table>
    <tr>
        <th>firstname</th>
        <th>lastname</th>
    </tr>
    <tr>
        <td>Tom</td>
        <td>Mitchell</td>
    </tr>
    <tr>
        <td>Jack</td>
        <td>Ryan</td>
    </tr>
</table>



## 使用现有数据库

连接字符串是 SQLAlchemy 标准。
```
mysql+pymysql://scott:tiger@localhost/foo
oracle://scott:tiger@127.0.0.1:1521/sidname
sqlite://
sqlite:///foo.db
mssql+pyodbc://username:password@host/database?driver=SQL+Server+Native+Client+11.0
```

mysql 和 mysql+pymysql 连接(可能还有其他连接)不会从 `.my.cnf.` 中读取的客户端字符集信息。需要在连接字符串中指定它:
```
mysql+pymysql://scott:tiger@localhost/foo?charset=utf8
```

我们还可以使用魔术功能连接到现有数据库。下面，将使用 SQL_SAFI 数据库（**Studying African Farmer-led Irrigation（SAFI）数据库）**。该[SAFI项目](http://www.safi-research.org/)是一个研究项目，寻找在坦桑尼亚和莫桑比克农民使用农业和灌溉方法。该数据集由与家庭和农业有关的调查数据组成。 你可以从[这里](https://datacarpentry.org/sql-socialsci/data/SQL_SAFI.sqlite)下载。


```python
%sql sqlite:///./SQL_SAFI.sqlite
```


    'Connected: @./SQL_SAFI.sqlite'



上面的语句打开了名为 ` SQL_SAFI.sqlite` 的数据库.让我们选择 Crops 表并显示其内容。

- 普通 IPython 任务适用于单行 `%sql` 查询:


```python
%sql select * from Crops;
```

       sqlite://
     * sqlite:///./SQL_SAFI.sqlite
    Done.

<table>
    <tr>
        <th>Id</th>
        <th>plot_Id</th>
        <th>crop_Id</th>
        <th>D05_times</th>
        <th>D_curr_crop</th>
        <th>D_repeat_times_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
        <tr>
        <td>...</td>
    </tr>
</table>



如果 sql 语句较复杂，单行显示不够美观清晰，则需要使用 `%%sql` 整个单元格执行 sql 语句。


```python
%%sql
select * 
from Crops 
where D_curr_crop = "maize" 
AND ID <= 3;
```

       sqlite://
     * sqlite:///./SQL_SAFI.sqlite
    Done.



<table>
    <tr>
        <th>Id</th>
        <th>plot_Id</th>
        <th>crop_Id</th>
        <th>D05_times</th>
        <th>D_curr_crop</th>
        <th>D_repeat_times_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
</table>



## Configuration


```python
%config SqlMagic
```

    SqlMagic options
    --------------
    SqlMagic.autocommit=<Bool>
        Current: True
        Set autocommit mode
    SqlMagic.autolimit=<Int>
        Current: 0
        Automatically limit the size of the returned result sets
    SqlMagic.autopandas=<Bool>
        Current: False
        Return Pandas DataFrames instead of regular result sets
    SqlMagic.column_local_vars=<Bool>
        Current: False
        Return data into local variables from column names
    SqlMagic.displaylimit=<Int>
        Current: None
        Automatically limit the number of rows displayed (full result set is still
        stored)
    SqlMagic.dsn_filename=<Unicode>
        Current: 'odbc.ini'
        Path to DSN file. When the first argument is of the form [section], a
        sqlalchemy connection string is formed from the matching section in the DSN
        file.
    SqlMagic.feedback=<Bool>
        Current: True
        Print number of rows affected by DML
    SqlMagic.short_errors=<Bool>
        Current: True
        Don't display the full traceback on SQL Programming Error
    SqlMagic.style=<Unicode>
        Current: 'DEFAULT'
        Set the table printing style to any of prettytable's defined styles
        (currently DEFAULT, MSWORD_FRIENDLY, PLAIN_COLUMNS, RANDOM)


设置显示行数为10


```python
%config SqlMagic.displaylimit=10
```


```python
%sql select * from Crops;
```

       sqlite://
     * sqlite:///./SQL_SAFI.sqlite
    Done.



<table>
    <tr>
        <th>Id</th>
        <th>plot_Id</th>
        <th>crop_Id</th>
        <th>D05_times</th>
        <th>D_curr_crop</th>
        <th>D_repeat_times_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>tomatoes</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>vegetable</td>
        <td>1</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>4</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>4</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>4</td>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>sorghum</td>
        <td>1</td>
    </tr>
    <tr>
        <td>5</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
</table>
<span style="font-style:italic;text-align:center;">1044 rows, truncated to displaylimit of 10</span>



## 维护与多个数据库的连接

```
sqlite://
 * sqlite:///./SQL_SAFI.sqlite
Done.
```
上面我们已经连接了两个数据库，可以看到，执行sql语句时，会以最新连接的库来查询。我们可以随意指定要查询的库。

**注意：在  `%sql` 命令中，在任何变量之前使用 `$` 来访问它。**

```
%sql $sqlite:///./SQL_SAFI.sqlite
```

例如下面：


```python
db_safi = 'sqlite:///./SQL_SAFI.sqlite'
%sql $db_safi select * from Crops;
```

    Done.



<table>
    <tr>
        <th>Id</th>
        <th>plot_Id</th>
        <th>crop_Id</th>
        <th>D05_times</th>
        <th>D_curr_crop</th>
        <th>D_repeat_times_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>tomatoes</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>vegetable</td>
        <td>1</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>4</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>4</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>4</td>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>sorghum</td>
        <td>1</td>
    </tr>
    <tr>
        <td>5</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
</table>
<span style="font-style:italic;text-align:center;">1044 rows, truncated to displaylimit of 10</span>



## 储存查询结果

绑定变量(绑定参数)可以在“命名”(:x)样式中使用。使用的变量名应该在本地命名空间中定义。


```python
d_curr_crop = 'maize'
result = %sql $db_safi select * from Crops where D_curr_crop = :d_curr_crop AND ID <= 3;
result
```

    Done.

<table>
    <tr>
        <th>Id</th>
        <th>plot_Id</th>
        <th>crop_Id</th>
        <th>D05_times</th>
        <th>D_curr_crop</th>
        <th>D_repeat_times_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
</table>


```python
result[:4]
```


    [(1, 1, 1, 1, 'maize', 1),
     (1, 2, 1, 1, 'maize', 1),
     (2, 1, 1, 1, 'maize', 1),
     (3, 1, 1, 1, 'maize', 1)]




```python
result.keys
```


    ['Id',
     'plot_Id',
     'crop_Id',
     'D05_times',
     'D_curr_crop',
     'D_repeat_times_count']




```python
result[0]['plot_Id'], result[0].D_curr_crop
```


    (1, 'maize')




```python
result.dict()
```


    {'Id': (1, 1, 2, 3),
     'plot_Id': (1, 2, 1, 1),
     'crop_Id': (1, 1, 1, 1),
     'D05_times': (1, 1, 1, 1),
     'D_curr_crop': ('maize', 'maize', 'maize', 'maize'),
     'D_repeat_times_count': (1, 1, 1, 1)}



**`<<` 运算符捕获局部变量中的查询结果，并可以在多行 `%%sql` 中使用：**

例如：将结果存入 `result`


```python
%%sql $db_safi
result << 
select * 
from Crops 
where D_curr_crop = "maize" 
AND ID <= 3;
```

    Done.
    Returning data to local variable result


结果只能在另外一个单元格查看


```python
result
```


<table>
    <tr>
        <th>Id</th>
        <th>plot_Id</th>
        <th>crop_Id</th>
        <th>D05_times</th>
        <th>D_curr_crop</th>
        <th>D_repeat_times_count</th>
    </tr>
    <tr>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>2</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
    <tr>
        <td>3</td>
        <td>1</td>
        <td>1</td>
        <td>1</td>
        <td>maize</td>
        <td>1</td>
    </tr>
</table>


```python
type(result)
```


    sql.run.ResultSet



## 存储为 csv

结果集附带一个 `.csv(filename=None)` 方法。这会生成逗号分隔的文本，作为返回值(如果未指定文件名)或给定名称的文件。


```python
result.csv()
```


    'Id,plot_Id,crop_Id,D05_times,D_curr_crop,D_repeat_times_count\r\n1,1,1,1,maize,1\r\n1,2,1,1,maize,1\r\n2,1,1,1,maize,1\r\n3,1,1,1,maize,1\r\n'




```python
result.csv(filename='Crops.csv')
```

​	CSV results



## Pandas

结果也可以转换为 Pandas DataFrame ，如下所示：


```python
dataframe = result.DataFrame()
dataframe
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Id</th>
      <th>plot_Id</th>
      <th>crop_Id</th>
      <th>D05_times</th>
      <th>D_curr_crop</th>
      <th>D_repeat_times_count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>maize</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>maize</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>maize</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>maize</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
dataframe.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 4 entries, 0 to 3
    Data columns (total 6 columns):
    Id                      4 non-null int64
    plot_Id                 4 non-null int64
    crop_Id                 4 non-null int64
    D05_times               4 non-null int64
    D_curr_crop             4 non-null object
    D_repeat_times_count    4 non-null int64
    dtypes: int64(5), object(1)
    memory usage: 320.0+ bytes



## Matplotlib

安装matplotlib后，使用 `plot()`, `pie()`, `bar()`等函数可快速绘图。


```python
%matplotlib inline

result.pie()
```




    ([<matplotlib.patches.Wedge at 0x1d4fb075ac8>,
      <matplotlib.patches.Wedge at 0x1d4fb075fd0>,
      <matplotlib.patches.Wedge at 0x1d4fb08c4a8>,
      <matplotlib.patches.Wedge at 0x1d4fb08c940>],
     [Text(0.7778174593052024, 0.7778174593052024, '1 1 1 1 maize'),
      Text(-0.7778174593052023, 0.7778174593052024, '1 2 1 1 maize'),
      Text(-0.7778174593052025, -0.7778174593052023, '2 1 1 1 maize'),
      Text(0.7778174593052022, -0.7778174593052025, '3 1 1 1 maize')])




![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/image_hosting/image.plp9nnqhjv.png)


## 使用 MySQL

```
!pip install pymysql
```


```python
def db_login(schema='pandas'):
    user = "hufe09"
    password = "password"
    host = '127.0.0.1'
    db = f'mysql+pymysql://{user}:{password}@{host}/{schema}?charset=utf8'
    return db
```


```python
db_pandas = db_login()
```


```python
%sql $db_pandas
```


    'Connected: hufe09@pandas'



### 执行存储过程


```python
%%sql $db_pandas

DROP PROCEDURE
IF
	EXISTS `add_nums`;
CREATE PROCEDURE `add_nums` ( IN n INT ) 
BEGIN
    DECLARE i INT;
    DECLARE sum INT;
    
    SET i = 1;
    SET sum = 0;
    WHILE
    	i <= n DO
    SET sum = sum + i;
    SET i = i + 1;
    
    END WHILE;
    SELECT 	sum;
END;
CALL add_nums ( 12 );
```

    0 rows affected.
    0 rows affected.
    1 rows affected.

<table>
    <tr>
        <th>sum</th>
    </tr>
    <tr>
        <td>78</td>
    </tr>
</table>




```python
db_lol = db_login('lol')
```


```python
%%sql $db_lol

DROP PROCEDURE
IF
	EXISTS `get_hero_scores`;
CREATE PROCEDURE `get_hero_scores` ( OUT max_max_hp FLOAT, OUT min_max_mp FLOAT, OUT avg_max_attack FLOAT, s VARCHAR ( 255 ) ) BEGIN
SELECT
	MAX( hp_max ),
	MIN( mp_max ),
	AVG( attack_max ) 
FROM
	lol.heros 
WHERE
	role_main = s INTO max_max_hp,
	min_max_mp,
	avg_max_attack;

END ;

CALL get_hero_scores(@max_max_hp, @min_max_mp, @avg_max_attack, '战士');
SELECT @max_max_hp, @min_max_mp, @avg_max_attack;
```

    0 rows affected.
    0 rows affected.
    1 rows affected.
    1 rows affected.



<table>
    <tr>
        <th>@max_max_hp</th>
        <th>@min_max_mp</th>
        <th>@avg_max_attack</th>
    </tr>
    <tr>
        <td>8050.0</td>
        <td>0.0</td>
        <td>342.1666564941406</td>
    </tr>
</table>



### 子查询


```python
db_parch_posey = db_login('parch_posey')
```


```python
%%sql $db_parch_posey
subqueries <<
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

    6 rows affected.
    Returning data to local variable subqueries

```python
subqueries
```


<table>
    <tr>
        <th>channel</th>
        <th>avg_events</th>
    </tr>
    <tr>
        <td>direct</td>
        <td>1.6227</td>
    </tr>
    <tr>
        <td>facebook</td>
        <td>1.1179</td>
    </tr>
    <tr>
        <td>organic</td>
        <td>1.1174</td>
    </tr>
    <tr>
        <td>adwords</td>
        <td>1.0850</td>
    </tr>
    <tr>
        <td>twitter</td>
        <td>1.0557</td>
    </tr>
    <tr>
        <td>banner</td>
        <td>1.0554</td>
    </tr>
</table>



## 结论
使用 SQL 和 Jupyter Notbook 将数据分析提升到一个新的水平。jupyter-sql 接口使得将 SQL Server 连接到 Jupyter 生态系统并将数据直接提取到它中非常容易，而无需离开 Jupyter 接口。

>参考链接：[datacamp](https://www.datacamp.com/community/tutorials/sql-interface-within-jupyterlab)
