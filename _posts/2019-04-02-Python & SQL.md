---
layout: post
title: "Python & SQL"
subtitle: "SQLAlchemy使用/ Pandas操作SQLAlchemy/ PyMySQL操作MySQL"
author: "Hufe"
header-img: "img/post-bg-mysql.jpg"
header-mask: 0.3
tags:
  - Python
  - SQLAlchemy
  - Pandas
  - MySQL

---

# SQLAlchemy使用

```python
import pandas as pd
import numpy as np

pd.set_option("display.max_columns", 100)  # 设置显示数据的最大列数，防止出现省略号…，导致数据显示不全
pd.set_option("expand_frame_repr", False)  # 当列太多时不自动换行
```


```python
from sqlalchemy import create_engine, Column, String, Integer, Float, or_, and_
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
```


```python
# engine = create_engine('mysql+pymysql://root:PASSWORD@localhost/pandas')
db_login = pd.read_csv('db_login.csv').iloc[0]
print('当前Database: ', db_login.schema)
# 初始化数据库连接:
mysql_engine = f'mysql+pymysql://{db_login.user}:{db_login.password}@{db_login.host}/{db_login.schema}'
engine = create_engine(mysql_engine)
```

    当前Database:  nba



```python
# 创建对象的基类
Base = declarative_base()


# 定义player对象
class Player(Base):
    # 表的名字
    __tablename__ = 'player'

    # 表的结构
    player_id = Column(Integer, primary_key=True, autoincrement=True)
    team_id = Column(Integer)
    player_name = Column(String(255))
    height = Column(Float(3, 2))
```

## 新增


```python
# 创建 DBSession 类型
DBSession = sessionmaker(bind=engine)

# 创建session对象
session = DBSession()
# 创建 Player 对象
new_player = Player(team_id=1003, player_name='黑曼巴-科比', height=2.08)
try:
    # 添加到session
    session.add(new_player)
    # 提交即保存在数据库
    session.commit()
    print('Add Success!')
except Exception as e:
    print(f'Error:\n {e}')
# 关闭 session
session.close()
```

    Add Success!



```python
# 增加 to_dict() 方法到 Base 类中
def to_dict(self):
    return {
        c.name: getattr(self, c.name, None)
        for c in self.__table__.columns
    }


# 将对象可以转化为 dict 类型
Base.to_dict = to_dict
```

## 查询


```python
import pprint
# 查询身高 >= 2.08 的球员
rows = session.query(Player).filter(Player.height >= 2.08).all()
pprint.pprint([row.to_dict() for row in rows])
```

    [{'height': Decimal('2.1100000000'),
      'player_id': 10003,
      'player_name': '安德烈-德拉蒙德',
      'team_id': 1001},
     {'height': Decimal('2.1700000000'),
      'player_id': 10004,
      'player_name': '索恩-马克',
      'team_id': 1001},
     {'height': Decimal('2.1100000000'),
      'player_id': 10009,
      'player_name': '扎扎-帕楚里亚',
      'team_id': 1001},
     {'height': Decimal('2.0800000000'),
      'player_id': 10010,
      'player_name': '乔恩-洛伊尔',
      'team_id': 1001},
     {'height': Decimal('2.0800000000'),
      'player_id': 10011,
      'player_name': '布雷克-格里芬',
      'team_id': 1001},
     {'height': Decimal('2.1100000000'),
      'player_id': 10015,
      'player_name': '亨利-埃伦森',
      'team_id': 1001},
     {'height': Decimal('2.1100000000'),
      'player_id': 10023,
      'player_name': '多曼塔斯-萨博尼斯',
      'team_id': 1002},
     {'height': Decimal('2.1100000000'),
      'player_id': 10024,
      'player_name': '迈尔斯-特纳',
      'team_id': 1002},
     {'height': Decimal('2.0800000000'),
      'player_id': 10032,
      'player_name': 'TJ-利夫',
      'team_id': 1002},
     {'height': Decimal('2.0800000000'),
      'player_id': 10033,
      'player_name': '凯尔-奥奎因',
      'team_id': 1002},
     {'height': Decimal('2.0800000000'),
      'player_id': 10037,
      'player_name': '伊凯·阿尼博古',
      'team_id': 1002},
     {'height': Decimal('2.0800000000'),
      'player_id': 10102,
      'player_name': '黑曼巴-科比',
      'team_id': 1003}]



```python
def print_res(rows):
    return [row.to_dict() for row in rows]
```

## 多条件查询


```python
rows = session.query(Player).filter(
    or_(Player.height >= 2.08, Player.height <= 2.10)).all()
print_res(rows[:5])
```




    [{'player_id': 10001,
      'team_id': 1001,
      'player_name': '韦恩-艾灵顿',
      'height': Decimal('1.9300000000')},
     {'player_id': 10002,
      'team_id': 1001,
      'player_name': '雷吉-杰克逊',
      'height': Decimal('1.9100000000')},
     {'player_id': 10003,
      'team_id': 1001,
      'player_name': '安德烈-德拉蒙德',
      'height': Decimal('2.1100000000')},
     {'player_id': 10004,
      'team_id': 1001,
      'player_name': '索恩-马克',
      'height': Decimal('2.1700000000')},
     {'player_id': 10005,
      'team_id': 1001,
      'player_name': '布鲁斯-布朗',
      'height': Decimal('1.9600000000')}]



## 分组，排序，返回指定数量结果


```python
from sqlalchemy import func
rows = session.query(Player.team_id, func.count(Player.player_id)).group_by(
    Player.team_id).all()
rows
```


    [(1001, 20), (1002, 17), (1003, 1)]




```python
from sqlalchemy import func
rows = session.query(Player.team_id, func.count(Player.player_id)).group_by(
    Player.team_id).having(func.count(Player.player_id) > 5).order_by(
        func.count(Player.player_id).asc()).all()

rows
```


    [(1002, 17), (1001, 20)]



## 删除


```python
row = session.query(Player).filter(Player.player_name == '黑曼巴-科比').first()
print(row.to_dict())
try:
    # 删除行
    session.delete(row)
    session.commit()
    print('Delete Success!')
except Exception as e:
    print(f'Error:\n {e}')
    session.close()
```

    {'player_id': 10102, 'team_id': 1003, 'player_name': '黑曼巴-科比', 'height': Decimal('2.0800000000')}
    Delete Success!


## 修改


```python
row = session.query(Player).filter(Player.player_name == '索恩-马克').first()
if row is not None:
    pprint.pprint(row.to_dict())
    row.height = 2.17
    session.commit()
    session.close()
```

    {'height': Decimal('2.1700000000'),
     'player_id': 10004,
     'player_name': '索恩-马克',
     'team_id': 1001}


# Pandas操作SQLAlchemy

### [pandas.read_sql](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_sql.html#pandas.read_sql)

pandas.`read_sql`(*sql*, *con*, *index_col=None*, *coerce_float=True*, *params=None*, *parse_dates=None*, *columns=None*, *chunksize=None*)[[source\]](http://github.com/pandas-dev/pandas/blob/v0.25.0/pandas/io/sql.py#L336-L437)

Read SQL query or database table into a DataFrame.

This function is a convenience wrapper around `read_sql_table` and `read_sql_query` (for backward compatibility). It will delegate to the specific function depending on the provided input. A SQL query will be routed to `read_sql_query`, while a database table name will be routed to `read_sql_table`. Note that the delegated function might have more specific notes about their functionality not listed here.

- **Parameters**:

- **sql** : string or SQLAlchemy Selectable (select or text object)
    SQL query to be executed or a table name.

- **con** : SQLAlchemy connectable (engine/connection) or database string URI
or DBAPI2 connection (fallback mode)

    Using SQLAlchemy makes it possible to use any DB supported by that library. If a DBAPI2 object, only sqlite3 is supported.

- **index_col** : string or list of strings, optional, default: None
Column(s) to set as index(MultiIndex).

- **coerce_float** : boolean, default True
    Attempts to convert values of non-string, non-numeric objects (like decimal.Decimal) to floating point, useful for SQL result sets.

- **params** : list, tuple or dict, optional, default: None
    List of parameters to pass to execute method. The syntax used to pass parameters is database driver dependent. Check your database driver documentation for which of the five syntax styles, described in PEP 249’s paramstyle, is supported. Eg. for psycopg2, uses %(name)s so use params={‘name’ : ‘value’}

- **parse_dates** : list or dict, default: None
    - List of column names to parse as dates.
    - Dict of {column_name: format string} where format string is strftime compatible in case of parsing string times, or is one of (D, s, ns, ms, us) in case of parsing integer timestamps.
    - Dict of {column_name: arg dict}, where the arg dict corresponds to the keyword arguments of pandas.to_datetime() Especially useful with databases without native Datetime support, such as SQLite.
- **columns** : list, default: None
    List of column names to select from SQL table (only used when reading a table).

- **chunksize** : int, default None
    If specified, return an iterator where chunksize is the number of rows to include in each chunk.
    
- **Returns** : DataFrame


```python
# SQLAlchemy Selectable
sql = "SELECT * FROM team;"
try:
    df = pd.read_sql(sql, engine)
except Exception as e:
    print(e)
df
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
      <th>team_id</th>
      <th>team_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1001</td>
      <td>底特律活塞</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1002</td>
      <td>印第安纳步行者</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1003</td>
      <td>亚特兰大老鹰</td>
    </tr>
  </tbody>
</table>
</div>



### pandas.read_sql_query


```python
# string SQL query
sql = "SELECT * FROM team;"
try:
    df = pd.read_sql_query(sql, engine)
except Exception as e:
    print(e)
df.head()
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
      <th>team_id</th>
      <th>team_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1001</td>
      <td>底特律活塞</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1002</td>
      <td>印第安纳步行者</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1003</td>
      <td>亚特兰大老鹰</td>
    </tr>
  </tbody>
</table>
</div>



### pandas.read_sql_table


```python
# Name of SQL table in database.
try:
    df = pd.read_sql_table('team', engine)
except Exception as e:
    print(e)
df.head()
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
      <th>team_id</th>
      <th>team_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1001</td>
      <td>底特律活塞</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1002</td>
      <td>印第安纳步行者</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1003</td>
      <td>亚特兰大老鹰</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.to_csv('team.csv', index=False)
```

### 将DataFrame 存入 数据库


```python
df.to_sql('team', engine, index=False, if_exists='replace')
```

- **if_exists**:
    - append：如果表存在，则将数据添加到这个表的后面
    - fail：如果表存在就不操作
    - replace：如果存在表，删了，重建

# PyMySQL操作MySQL


```python
# ! pip install pymysql
import pymysql

# 打开数据库连接
# db = pymysql.connect("localhost","testuser","test123","TESTDB" )
db_login = pd.read_csv('db_login.csv').iloc[0]
db = pymysql.connect(db_login.host, db_login.user, db_login.password,
                     db_login.schema)
print('当前Database: ', db_login.schema)
```

    当前Database:  nba



```python
# 使用 cursor() 方法创建一个游标对象 cursor
cursor = db.cursor()

# 使用 execute()  方法执行 SQL 查询, 执行完毕返回的结果集默认以元组显示
cursor.execute("SELECT VERSION()")

# 使用 fetchone() 方法获取单条数据.
data = cursor.fetchone()

print(f"Database version: {data}")
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```

    Database version: ('5.7.25-0ubuntu0.18.04.2',)


## 查询


```python
# SQL 查询语句
sql = "SELECT * FROM nba.player;"
try:
    cursor.execute(sql)
except Exception as e:
    print(e)
# 获取单条
cursor.fetchone()
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```




    (10001, 1001, '韦恩-艾灵顿', 1.93)




```python
# SQL 查询语句
sql = "SELECT * FROM nba.player;"
try:
    cursor.execute(sql)
except Exception as e:
    print(e)
# 可以获取指定数量的数据
cursor.fetchmany(3)
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```




    ((10001, 1001, '韦恩-艾灵顿', 1.93),
     (10002, 1001, '雷吉-杰克逊', 1.91),
     (10003, 1001, '安德烈-德拉蒙德', 2.11))




```python
# SQL 查询语句
sql = "SELECT * FROM nba.player LIMIT 10"
try:
    cursor.execute(sql)
except Exception as e:
    print(e)
# 获取所有
cursor.fetchall()
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```




    ((10001, 1001, '韦恩-艾灵顿', 1.93),
     (10002, 1001, '雷吉-杰克逊', 1.91),
     (10003, 1001, '安德烈-德拉蒙德', 2.11),
     (10004, 1001, '索恩-马克', 2.17),
     (10005, 1001, '布鲁斯-布朗', 1.96),
     (10006, 1001, '兰斯顿-加洛韦', 1.88),
     (10007, 1001, '格伦-罗宾逊三世', 1.98),
     (10008, 1001, '伊斯梅尔-史密斯', 1.83),
     (10009, 1001, '扎扎-帕楚里亚', 2.11),
     (10010, 1001, '乔恩-洛伊尔', 2.08))




```python
# 光标按绝对位置移动1
cursor.scroll(1, mode="absolute")
```


```python
# 光标按照相对位置(当前位置)移动1
print(cursor.scroll(1, mode="relative"))
```

    None


测试错误语句提示


```python
# SQL 查询语句
sql = "SELECT * FROMS  pandas.bestofrt LIMIT 5;"
try:
    cursor.execute(sql)
except Exception as e:
    print(f'Error: \n{e}')
cursor.fetchall()
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```

    Error: 
    (1064, "You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'FROMS  pandas.bestofrt LIMIT 5' at line 1")
    
    ()



## 插入单条语句 cursor.execute


```python
sql = 'INSERT INTO player (team_id, player_name, height) VALUES (%s,%s,%s);'

team_id = '1003'
player_name = '科比-布莱恩特'
height = 1.98
try:
    # 拼接并执行 SQL 语句
    cursor.execute(sql, [team_id, player_name, height])
    db.commit()
except Exception as e:
    print(f'Error: \n{e}')
    # 事务回滚
    conn.rollback()
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```

## 获取最新的那一条数据的ID


```python
# 获取最新的那一条数据的ID
insert_id = cursor.lastrowid
print("最后一条数据的ID是:", insert_id)
```

    最后一条数据的ID是: 10103


## 插入多条语句 cursor.executemany


```python
sql = 'INSERT INTO player (team_id, player_name, height) VALUES (%s,%s,%s);'
data = [(1003, '勒布朗-詹姆斯', 2.02), (1004, '迈克尔-乔丹', 1.98), (1005, '姚明', 2.26)]
try:
    # 拼接并执行 SQL 语句
    cursor.executemany(sql, data)
    db.commit()
except Exception as e:
    print(f'Error: \n{e}')
    # 事务回滚
    db.rollback()
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```


```python
# 获取最新的那一条数据的ID
insert_id = cursor.lastrowid
print("最后一条数据的ID是:", insert_id)
```

    最后一条数据的ID是: 10066


最后一条数据的ID是: 10066
![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.rmqcd21upf.png)

插入多条数据时，`cursor.lastrowid`获取到的id是多语句的第一条

## 删除数据

### 删除单条


```python
sql = "DELETE FROM player WHERE player_name=%s;"
player_name = "科比-布莱恩特"

try:
    # 拼接并执行SQL语句
    cursor.execute(sql, [player_name])
    # 涉及写操作注意要提交
    db.commit()
except Exception as e:
    print(f'Error: \n{e}')
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```

### 删除多条


```python
sql = "DELETE FROM player WHERE player_name = %s;"
player_names = ['勒布朗-詹姆斯', '迈克尔-乔丹', '姚明']

try:
    # 拼接并执行SQL语句
    cursor.executemany(sql, player_names)
    # 涉及写操作注意要提交
    db.commit()
except Exception as e:
    print(f'Error: \n{e}')
    # 事务回滚
    db.rollback()  
# 关闭光标对象
# cursor.close()
# 关闭数据库连接
# db.close()
```

### 删除表


```python
# 使用 execute() 方法执行 SQL，如果表存在则删除
# cursor.execute("DROP TABLE IF EXISTS test")
```

## 修改数据


```python
sql = "UPDATE player SET height=%s WHERE player_name=%s;"
try:
    # 拼接并执行SQL语句
    cursor.execute(sql, [2.03, "勒布朗-詹姆斯"])
    # 涉及写操作注意要提交
    db.commit()
except Exception as e:
    print(f'Error: \n{e}')
# 关闭光标对象
cursor.close()
# 关闭数据库连接
db.close()
```
