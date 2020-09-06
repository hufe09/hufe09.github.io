---
layout: post
title: "「数据争论」Module 01: Data Wrangling Template"
subtitle: 'Data Analysis : Data Wrangling'
author: "Hufe"
header-img: "img/post-bg-datas.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Python
  - Data Analysis
  - Data Wrangling
---

# Data Wrangling Template

## Gather 收集数据


```python
import zipfile
import pandas as pd
```


```python
with zipfile.ZipFile('armenian-online-job-postings.zip', 'r') as myzip:
    myzip.extractall()
```

## Assess 评估数据


```python
df = pd.read_csv("online-job-postings.csv")
# df
```

## Tidy data requirements 
## 整洁的数据要求

- 每个变量形成一列。
- 每个观察形成一排。
- 每种类型的观察单位形成一个表格。


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 19001 entries, 0 to 19000
    Data columns (total 24 columns):
    jobpost             19001 non-null object
    date                19001 non-null object
    Title               18973 non-null object
    Company             18994 non-null object
    AnnouncementCode    1208 non-null object
    Term                7676 non-null object
    Eligibility         4930 non-null object
    Audience            640 non-null object
    StartDate           9675 non-null object
    Duration            10798 non-null object
    Location            18969 non-null object
    JobDescription      15109 non-null object
    JobRequirment       16479 non-null object
    RequiredQual        18517 non-null object
    Salary              9622 non-null object
    ApplicationP        18941 non-null object
    OpeningDate         18295 non-null object
    Deadline            18936 non-null object
    Notes               2211 non-null object
    AboutC              12470 non-null object
    Attach              1559 non-null object
    Year                19001 non-null int64
    Month               19001 non-null int64
    IT                  19001 non-null bool
    dtypes: bool(1), int64(2), object(21)
    memory usage: 3.4+ MB



```python
pd.value_counts('IT', dropna=False)
```




    IT    2
    dtype: int64



## Clean 清洗数据

- 修复非描述性列标题（ApplicationP, AboutC, RequiredQual ... and JobRequirment）

## 程序化数据清理流程：
1. Define 定义
2. Code 代码
3. Test 测试

## 清洗
### 问题
#### 1. 定义
- 将StartDate列中'As soon ad possible', 'Immediatedly'等类似描述，用'ASAP'替换
- 选择非描述性和错误拼写的列标题（ApplicationP, AboutC, RequiredQual ... and JobRequirment）,用（ApplicationProcedure, AboutCompany, RequiredQualifications,JobRequirement）

#### 2. 代码


```python
df_clean = df.copy()
# df_clean
```

- 选择非描述性和错误拼写的列标题（ApplicationP, AboutC, RequiredQual ... and JobRequirment）,用（ApplicationProcedure, AboutCompany, RequiredQualifications,JobRequirements）


```python
df_clean = df_clean.rename(index=str, columns={'ApplicationP':'ApplicationProcedure',
                                   'AboutC':'AboutCompany',
                                   'RequiredQual':'RequiredQualifications',
                                   'JobRequirment':'JobRequirements'})
```


```python
df_rename = pd.DataFrame({"A": [1, 2, 3], "B": [4, 5, 6]})
df_rename.rename(index=str, columns={"A": "a", "B": "c"})
```


<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>a</th>
      <th>c</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
</div>



- 将StartDate列中'As soon ad possible', 'Immediatedly'等类似描述，用'ASAP'替换


```python
df_clean.StartDate.value_counts()
```




    ASAP                                                    4754
    Immediately                                              773
    As soon as possible                                      543
    Upon hiring                                              261
    Immediate                                                259
    Immediate employment                                     140
    As soon as possible.                                      32
    01 September 2012                                         31
    March 2006                                                27
    November 2006                                             22
    January 2010                                              19
    01 February 2005                                          17
    February 2014                                             17
    Immediate job opportunity                                 17
    September 2010                                            16
    February 2011                                             16
    TBD                                                       16
    September 2008                                            15
    March 2011                                                15
    01 February 2015                                          14
    February 2007                                             14
    01 July 2014                                              14
    01 September 2010                                         13
    01 September 2008                                         13
    01 March 2006                                             13
    January 2016                                              13
    Fall 2010                                                 12
    01 October 2008                                           12
    01 March 2012                                             12
    01 October 2012                                           12
                                                            ... 
    14 September 2009                                          1
    01 May 2012                                                1
    02 September 2013                                          1
    6 September 2004                                           1
    October/ November 2012                                     1
    01 June, 2009                                              1
    December  2008                                             1
    June 2004                                                  1
    29 June 2004.  7:30 pm                                     1
    Summer 2006                                                1
    14 September 2015                                          1
    Upon availability                                          1
    01 March  2009                                             1
    29 April 2010                                              1
    10 December 2008                                           1
    04 August 2014                                             1
    July/ August 2012                                          1
    30 July 2004                                               1
    All qualified candidates                                   1
    Start date to be agreed, but probably in March 2005.       1
    20 June 2014                                               1
    Mid-January 2006                                           1
    20 July 2008                                               1
    05 June 2005                                               1
    07 December 2012                                           1
    11 February 2013  15 February 2013                         1
    06 April 2005                                              1
    01 July, 2010                                              1
    07 December 2015                                           1
    Midst June, 2004                                           1
    Name: StartDate, Length: 1186, dtype: int64




```python
asap_list = ['Immediately', 'As soon as possible','Upon hiring', 'Immediate','Immediate employment', 'As soon as possible.', 'Immediate job opportunity']
```

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/1555851028920-1555851028946.png)

```python
for phrase in asap_list:
    df_clean.StartDate.replace(phrase, 'ASAP', inplace=True) 
```

#### 3. 测试


```python
df_clean.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 19001 entries, 0 to 19000
    Data columns (total 24 columns):
    jobpost                   19001 non-null object
    date                      19001 non-null object
    Title                     18973 non-null object
    Company                   18994 non-null object
    AnnouncementCode          1208 non-null object
    Term                      7676 non-null object
    Eligibility               4930 non-null object
    Audience                  640 non-null object
    StartDate                 9675 non-null object
    Duration                  10798 non-null object
    Location                  18969 non-null object
    JobDescription            15109 non-null object
    JobRequirements           16479 non-null object
    RequiredQualifications    18517 non-null object
    Salary                    9622 non-null object
    ApplicationProcedure      18941 non-null object
    OpeningDate               18295 non-null object
    Deadline                  18936 non-null object
    Notes                     2211 non-null object
    AboutCompany              12470 non-null object
    Attach                    1559 non-null object
    Year                      19001 non-null int64
    Month                     19001 non-null int64
    IT                        19001 non-null bool
    dtypes: bool(1), int64(2), object(21)
    memory usage: 3.5+ MB



```python
df_clean.StartDate.value_counts()
```




    ASAP                                                                                             6779
    01 September 2012                                                                                  31
    March 2006                                                                                         27
    November 2006                                                                                      22
    January 2010                                                                                       19
    February 2014                                                                                      17
    01 February 2005                                                                                   17
    September 2010                                                                                     16
    TBD                                                                                                16
    February 2011                                                                                      16
    September 2008                                                                                     15
    March 2011                                                                                         15
    01 February 2015                                                                                   14
    01 July 2014                                                                                       14
    February 2007                                                                                      14
    01 September 2008                                                                                  13
    January 2016                                                                                       13
    01 September 2010                                                                                  13
    01 March 2006                                                                                      13
    Fall 2010                                                                                          12
    01 April 2014                                                                                      12
    01 October 2012                                                                                    12
    01 October 2008                                                                                    12
    01 March 2012                                                                                      12
    September 2013                                                                                     12
    June 2008                                                                                          12
    January 2011                                                                                       11
    01 February 2012                                                                                   11
    March 2008                                                                                         11
    01 September 2013                                                                                  11
                                                                                                     ... 
    03 December 2011                                                                                    1
    28 May 2015                                                                                         1
    Mid. August                                                                                         1
    01 May 2005 - 01 May 2006                                                                           1
    27 April 2013                                                                                       1
    05 November 2008                                                                                    1
    11 August 2004                                                                                      1
    17 November 2010                                                                                    1
    28 March 2005                                                                                       1
    22 July 2013                                                                                        1
    17 August 2004                                                                                      1
    12 January 2005                                                                                     1
    1 May 2005                                                                                          1
    01 March 2015 (preferably)                                                                          1
    17 April 2007                                                                                       1
    14 March 2011                                                                                       1
    Early August 2004                                                                                   1
    15 July 2008                                                                                        1
    15 August 2015                                                                                      1
    10 October 2013 (negotiable)                                                                        1
    20 October 2010                                                                                     1
    05 August 2010                                                                                      1
    08 July 2008, 18:00                                                                                 1
    10 June 2006                                                                                        1
    25 August 2007                                                                                      1
    December 2008 (we are planning to conduct the\r\ntrainings on Christmas days/ 24-25 December)       1
    11 March 2010                                                                                       1
    25 July 2005                                                                                        1
    August 10, 2004                                                                                     1
    Midst June, 2004                                                                                    1
    Name: StartDate, Length: 1179, dtype: int64




```python
# assert 2 + 2 == 5
# AssertionError: 
assert 2 + 2 == 4
# output nothing
```


```python
for phrase in asap_list:
    assert phrase not in df_clean.StartDate.values
```


```python
# df_clean.StartDate
```

## Analysis & Visualization 
## 分析和可视化


```python
# Numbers of 'ASAP' start dates（numerator 分子）
asap_counts = df_clean.StartDate.value_counts()['ASAP']
asap_counts
```




    6779




```python
# Nunmbers of non-empty start dates (denominator 分母)
non_empty_counts = df_clean.StartDate.count()
non_empty_counts
```




    9675




```python
# Precent of positions with an urgent start date i.e. 'ASAP'
asap_counts / non_empty_counts
```




    0.700671834625323




```python
%matplotlib inline
import numpy as np
labels = np.full(len(df_clean.StartDate.value_counts()), "", dtype=object)
labels[0] = 'ASAP'
df_clean.StartDate.value_counts().plot(kind= "pie", labels = labels)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x18987016ba8>




![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/output_33_1-1555850886784.png)

## 清洗前


```python
asap_counts = df.StartDate.value_counts()['ASAP']
non_empty_counts = df.StartDate.count()
asap_counts / non_empty_counts
```




    0.4913695090439276




```python
labels = np.full(len(df.StartDate.value_counts()), "", dtype=object)
labels[0] = 'ASAP'
df.StartDate.value_counts().plot(kind= "pie", labels = labels)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x18989193cc0>




![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/output_36_1-1555850922177.png)



[Jupyter Notebook](https://nbviewer.jupyter.org/github/hufe09/DataWrangling/blob/master/data_wrangling_template.ipynb)
[Github](https://github.com/hufe09/DataWrangling)