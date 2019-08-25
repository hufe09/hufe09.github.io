---
layout: post
title: "「数据挖掘」泰坦尼克生存分析"
subtitle: '决策树Decision Tree实战'
author: "Hufe"
header-img: "img/post-bg-datas.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Python
  - Data Analysis
  - Data Mining
---



# 泰坦尼克生存分析

## Table of Contents
<ul>
<li><a href="#intro">Introduction</a></li>
<li><a href="#wrangling">Data Wrangling</a></li>
<li><a href="#eda">Exploratory Data Analysis</a></li>
<li><a href="#conclusions">Conclusions</a></li>
<li><a href="#cart">CART Decision Tree</a></li>
<li><a href="#decision_tree">ID3 Decision Tree</a></li>
</ul>

<a id='intro'></a>
## Introduction

>RMS泰坦尼克号沉没是历史上最臭名昭着的沉船之一。1912年4月15日，在她的处女航中，泰坦尼克号在与冰山相撞后沉没，在2224名乘客和机组人员中造成1502人死亡。这场耸人听闻的悲剧震惊了国际社会，并为船舶制定了更好的安全规定。  
造成海难失事的原因之一是乘客和机组人员没有足够的救生艇。尽管幸存下沉有一些运气因素，但有些人比其他人更容易生存，例如妇女，儿童和上流社会。
>
>[kaggle数据集](https://www.kaggle.com/c/titanic/data)


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns


pd.set_option('display.max_columns', 100)  # 设置显示数据的最大列数，防止出现省略号…，导致数据显示不全
pd.set_option('expand_frame_repr', False)  # 当列太多时不自动换行
%matplotlib inline
sns.set_style('darkgrid')
```

<a id='wrangling'></a>
## Data Wrangling

> **Tip**: 这一部分，将加载数据，检查清洁度，然后修剪和清洗数据集进行分析。

- 使用 info() 了解数据表的基本情况：行数、列数、每列的数据类型、数据完整度；

- 使用 describe() 了解数据表的统计情况：总数、平均值、标准差、最小值、最大值等；

- 使用 describe(include=[‘O’]) 查看字符串类型（非数字）的整体情况；

- 使用 head 查看前几行数据（默认是前 5 行）；

- 使用 tail 查看后几行数据（默认是最后 5 行）。

### General Properties


```python
# 数据加载
df = pd.read_csv('titanic_dataset/train.csv')
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
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



| 特征 | 描述 | 值|
| - | - | - |
| survival | 生存           | 0 = No, 1 = Yes |
| pclass   | 票类别-社会地位  | 1 = 1st, 2 = 2nd, 3 = 3rd |
| sex      | 性别           | |
| Age      | 年龄           | |
| sibsp    | 兄弟/姐妹/配偶   | |
| parch    | 父母/孩子的数量 | |
| ticket   | 票号           | |
| fare     | 乘客票价       | |
| cabin    | 客舱号码       | |
| embarked | 登船港口       | C=Cherbourg, Q=Queenstown, S=Southampton |


```python
# 数据形状
df.shape
```


    (891, 12)



有891名乘客和12列


```python
# 以下是一些摘要统计数据
df.describe()
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
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>714.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>446.000000</td>
      <td>0.383838</td>
      <td>2.308642</td>
      <td>29.699118</td>
      <td>0.523008</td>
      <td>0.381594</td>
      <td>32.204208</td>
    </tr>
    <tr>
      <th>std</th>
      <td>257.353842</td>
      <td>0.486592</td>
      <td>0.836071</td>
      <td>14.526497</td>
      <td>1.102743</td>
      <td>0.806057</td>
      <td>49.693429</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.420000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>223.500000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>20.125000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>7.910400</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>446.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>14.454200</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>668.500000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>38.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>31.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>891.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>80.000000</td>
      <td>8.000000</td>
      <td>6.000000</td>
      <td>512.329200</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 12 columns):
    PassengerId    891 non-null int64
    Survived       891 non-null int64
    Pclass         891 non-null int64
    Name           891 non-null object
    Sex            891 non-null object
    Age            714 non-null float64
    SibSp          891 non-null int64
    Parch          891 non-null int64
    Ticket         891 non-null object
    Fare           891 non-null float64
    Cabin          204 non-null object
    Embarked       889 non-null object
    dtypes: float64(2), int64(5), object(5)
    memory usage: 83.6+ KB


通过观察发现ID，名字，票号，客舱号码对生存与否并没有什么关系，丢弃这些没用的列


```python
df.drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1, inplace=True)
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
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>7.2500</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>71.2833</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>7.9250</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>53.1000</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>8.0500</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>



### 整个数据框的直方图
与我们在摘要统计中看到的内容一致。
- 他们中的大多数人票价较低，偏向左边。
- 大多数乘客都是三等票。
- 大多数人单独 出行，没有和父母，孩子，兄弟姐妹或配偶一起来。
- 年龄也向左倾斜，大多数在20和40左右。
- 更多人没有幸存下来。


```python
df.hist(figsize=(10, 8))
```




    array([[<matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf948a6a0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf91a7240>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf91cea90>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf917b438>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf919dda0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf914b748>]],
          dtype=object)




![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.810vvgypw25.png)

### 数据清洗

此数据集一共有891行，年龄列只有714行，查看年龄为null的数据框，然后查看其直方图


```python
df[df.Age.isnull()].hist(figsize=(10, 8))
```




    array([[<matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf948ae48>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf8f46a20>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf8eef390>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf8f16cf8>],
           [<matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf8ec36a0>,
            <matplotlib.axes._subplots.AxesSubplot object at 0x7f5cf8e71048>]],
          dtype=object)




![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.afsg4onuncu.png)

Age列用均值填充空值


```python
df.fillna(df.mean(), inplace=True)
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 8 columns):
    Survived    891 non-null int64
    Pclass      891 non-null int64
    Sex         891 non-null object
    Age         891 non-null float64
    SibSp       891 non-null int64
    Parch       891 non-null int64
    Fare        891 non-null float64
    Embarked    889 non-null object
    dtypes: float64(2), int64(4), object(2)
    memory usage: 55.8+ KB



```python
df[df.Embarked.isnull()]
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
      <th>Survived</th>
      <th>Pclass</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>61</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>38.0</td>
      <td>0</td>
      <td>0</td>
      <td>80.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>829</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>62.0</td>
      <td>0</td>
      <td>0</td>
      <td>80.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
df['Embarked'].value_counts()
```


    S    644
    C    168
    Q     77
    Name: Embarked, dtype: int64



使用登录最多的港口来填充登录港口的 `nan` 值


```python
df['Embarked'].fillna('S', inplace=True)
```


```python
df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 8 columns):
    Survived    891 non-null int64
    Pclass      891 non-null int64
    Sex         891 non-null object
    Age         891 non-null float64
    SibSp       891 non-null int64
    Parch       891 non-null int64
    Fare        891 non-null float64
    Embarked    891 non-null object
    dtypes: float64(2), int64(4), object(2)
    memory usage: 55.8+ KB


<a id='eda'></a>
## 探索性数据分析

> **Tip**: 计算统计数据并创建可视化，目标是解决在简介部分提出的研究问题。

### Research Question 1
### 票价是否与生存有关？


```python
survived = df.Survived == True
died = df.Survived == False
```


```python
df.Fare[survived].mean()
```


    48.39540760233917




```python
df.Fare[died].mean()
```


    22.117886885245877




```python
df.Fare[survived].hist(alpha=0.5, bins=20, label="survived")
df.Fare[died].hist(alpha=0.5, bins=20, label="died")
plt.legend()
```





![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.ewyu0vut6k4.png)

票价越贵，幸存比例越高。

### Research Question 2
### 年龄是否与生存有关？


```python
df.Age[survived].hist(alpha=0.5, bins=20, label="survived")
df.Age[died].hist(alpha=0.5, bins=20, label="died")
plt.legend()
```


![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.422l1mkcphs.png)

小孩子比其他年龄段的生存机会更高，但除此之外，它们看起来并不太相关。

### Research Question 3
### 社会地位是否与生存有关？


```python
df.groupby('Pclass').Survived.mean()
```


    Pclass
    1    0.629630
    2    0.472826
    3    0.242363
    Name: Survived, dtype: float64




```python
df.groupby('Pclass').Survived.mean().plot(kind="bar")
```


![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.saoaufctdw.png)

社会地位越高，越容易生存。

### Research Question 4
### 性别是否与生存有关？


```python
df.Sex.value_counts()
```


    male      577
    female    314
    Name: Sex, dtype: int64




```python
df.groupby('Sex').Survived.mean()
```


    Sex
    female    0.742038
    male      0.188908
    Name: Survived, dtype: float64




```python
df.groupby('Sex').Survived.mean().plot(kind="bar")
```


![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.o4zhh4pv9k.png)


```python
df.groupby('Sex')['Pclass'].value_counts()
```


    Sex     Pclass
    female  3         144
            1          94
            2          76
    male    3         347
            1         122
            2         108
    Name: Pclass, dtype: int64




```python
df.query('Sex == "female"')['Fare'].median(), df.query(
    'Sex == "male"')["Fare"].median()
```


    (23.0, 10.5)



女性的票价比男性的更贵。


```python
df.groupby(['Pclass', 'Sex']).Survived.mean().plot(kind='bar')
```




![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.ne7ng4reot8.png)

很明显，即使你按等级汇总，女性比男性更容易幸存。因此，性别与生存之间似乎存在着非常强烈的联系。

### Research Question 4
### 家庭关系是否与生存有关？

- 兄弟/姐妹/配偶


```python
df.SibSp[survived].value_counts().plot(
    kind='bar', alpha=0.5, color='green', label='survived')
df.SibSp[died].value_counts().plot(
    kind='bar', alpha=0.5, color='red', label='died')
plt.legend()
```


![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.g5cwgn7iu95.png)

- 父母/孩子


```python
df.Parch[survived].value_counts().plot(
    kind='bar', alpha=0.5, color='green', label='survived')
df.Parch[died].value_counts().plot(
    kind='bar', alpha=0.5, color='red', label='died')
plt.legend()
```


![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.c30xnns8wa7.png)

同行人数越少，越容易幸存下来。

### Research Question 5
### 登船港口是否与生存有关？


```python
df.Embarked[survived].value_counts()
```


    S    219
    C     93
    Q     30
    Name: Embarked, dtype: int64




```python
df.Embarked[died].value_counts()
```


    S    427
    C     75
    Q     47
    Name: Embarked, dtype: int64




```python
df.Embarked[survived].value_counts().plot(
    kind='bar', alpha=0.5, color='green', label='survived')
df.Embarked[died].value_counts().plot(
    kind='bar', alpha=0.5, color='red', label='died')
plt.legend()
```


![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.ohezcgzvgji.png)

Cherbourg港口登船的乘客幸存的比死亡的多, Queenstown和Southampton港口死亡的多余幸存。

<a id='conclusions'></a>
## 结论

- 票价是否与生存有关？
  - 票价越贵，幸存比例越高。

- 年龄是否与生存有关？  
    - 子比其他年龄段的生存机会更高，但除此之外，它们看起来并不太相关。

- 社会地位是否与生存有关？  
    - 地位越高，越容易生存。

- 性别是否与生存有关？
    - 女性比男性更容易幸存。

- 家庭关系是否与生存有关？
     - 同行人数越少，越容易幸存。

- 登船港口是否与生存有关？
     - Cherbourg港口登船的乘客幸存的比死亡的多；
     - Queenstown和Southampton港口死亡的多于幸存。

# sklearn 中的决策树模型

sklearn 中只实现了 ID3 与 CART 决策树，所以我们暂时只能使用这两种决策树，在构造 DecisionTreeClassifier 类时，其中有一个参数是 criterion，意为标准。它决定了构造的分类树是采用 ID3 分类树，还是 CART 分类树，对应的取值分别是 entropy 或者 gini：

- entropy: 基于信息熵，也就是 ID3 算法，实际结果与 C4.5 相差不大；
- gini：默认参数，基于基尼系数。CART 算法是基于基尼系数做属性划分的，所以 criterion=gini 时，实际上执行的是 CART 算法。

我们通过设置 criterion='entropy’可以创建一个 ID3 决策树分类器，然后打印下 clf，看下决策树在 sklearn 中是个什么东西？

```
DecisionTreeClassifier(class_weight=None, criterion='entropy', max_depth=None,
            max_features=None, max_leaf_nodes=None,
            min_impurity_decrease=0.0, min_impurity_split=None,
            min_samples_leaf=1, min_samples_split=2,
            min_weight_fraction_leaf=0.0, presort=False, random_state=None,
            splitter='best')

```

这里我们看到了很多参数，除了设置 criterion 采用不同的决策树算法外，一般建议使用默认的参数，默认参数不会限制决策树的最大深度，不限制叶子节点数，认为所有分类的权重都相等等。当然你也可以调整这些参数，来创建不同的决策树模型。

![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.uuy8ytijo5t.png)

在构造决策树分类器后，我们可以使用 fit 方法让分类器进行拟合，使用 predict 方法对新数据进行预测，得到预测的分类结果，也可以使用 score 方法得到分类器的准确率。

下面这个表格是 fit 方法、predict 方法和 score 方法的作用。
![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.vdwwjd31zi.png)

<a id='cart'></a>
## 如何使用 CART 算法来创建分类树
CART 分类树实际上是基于基尼系数来做属性划分的。在 Python 的 sklearn 中，如果我们想要创建 CART 分类树，可以直接使用 DecisionTreeClassifier 这个类。创建这个类的时候，默认情况下 criterion 这个参数等于 gini，也就是按照基尼系数来选择属性划分，即默认采用的是 CART 分类树。

下面，来用 CART 分类树，给 iris 数据集构造一棵分类决策树。基于 iris 数据集，构造 CART 分类树的代码如下：


```python
# encoding=utf-8
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.tree import DecisionTreeClassifier
from sklearn.datasets import load_iris
# 准备数据集
iris = load_iris()
# 获取特征集和分类标识
features = iris.data
labels = iris.target
# 随机抽取 33% 的数据作为测试集，其余为训练集
train_features, test_features, train_labels, test_labels = train_test_split(
    features, labels, test_size=0.33, random_state=0)
# 创建 CART 分类树
clf = DecisionTreeClassifier(criterion='gini')
# 拟合构造 CART 分类树
clf = clf.fit(train_features, train_labels)
# 用 CART 分类树做预测
test_predict = clf.predict(test_features)
# 预测结果与测试集结果作比对
score = accuracy_score(test_labels, test_predict)
print("CART 分类树准确率 %.4lf" % score)
```

    CART 分类树准确率 0.9600



```python
# 最后利用graphviz库打印出决策树图
from sklearn import tree
import graphviz
dot_data = tree.export_graphviz(
    clf, out_file=None, feature_names=iris.feature_names, class_names=iris.target_names, filled=True, rounded=True, special_characters=True)
graph = graphviz.Source(dot_data)
graph
```



![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.39w0xoohai3.png)

<a id='decision_tree'></a>
# 使用 ID3 算法进行Titanic 乘客生存预测

## 模块 1：数据探索


```python
import pandas as pd
pd.set_option('display.max_columns', 100)  # 设置显示数据的最大列数，防止出现省略号…，导致数据显示不全
pd.set_option('expand_frame_repr', False)  # 当列太多时不自动换行
```


```python
# 数据加载
train_data = pd.read_csv('titanic_dataset/train.csv')
test_data = pd.read_csv('titanic_dataset/test.csv')
# 数据探索
train_data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 12 columns):
    PassengerId    891 non-null int64
    Survived       891 non-null int64
    Pclass         891 non-null int64
    Name           891 non-null object
    Sex            891 non-null object
    Age            714 non-null float64
    SibSp          891 non-null int64
    Parch          891 non-null int64
    Ticket         891 non-null object
    Fare           891 non-null float64
    Cabin          204 non-null object
    Embarked       889 non-null object
    dtypes: float64(2), int64(5), object(5)
    memory usage: 83.6+ KB


| 特征 | 描述 | 值|
| - | - | - |
| survival | 生存           | 0 = No, 1 = Yes |
| pclass   | 票类别-社会地位  | 1 = 1st, 2 = 2nd, 3 = 3rd |
| sex      | 性别           | |
| Age      | 年龄           | |
| sibsp    | 兄弟/姐妹/配偶   | |
| parch    | 父母/孩子的数量 | |
| ticket   | 票号           | |
| fare     | 乘客票价       | |
| cabin    | 客舱号码       | |
| embarked | 登船港口       | C=Cherbourg, Q=Queenstown, S=Southampton |


```python
train_data.head()
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
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>3</td>
      <td>Braund, Mr. Owen Harris</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>A/5 21171</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>1</td>
      <td>1</td>
      <td>Cumings, Mrs. John Bradley (Florence Briggs Th...</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>PC 17599</td>
      <td>71.2833</td>
      <td>C85</td>
      <td>C</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>1</td>
      <td>3</td>
      <td>Heikkinen, Miss. Laina</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>STON/O2. 3101282</td>
      <td>7.9250</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>1</td>
      <td>1</td>
      <td>Futrelle, Mrs. Jacques Heath (Lily May Peel)</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>113803</td>
      <td>53.1000</td>
      <td>C123</td>
      <td>S</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>0</td>
      <td>3</td>
      <td>Allen, Mr. William Henry</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>373450</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
  </tbody>
</table>
</div>




```python
test_data.tail()
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
      <th>PassengerId</th>
      <th>Pclass</th>
      <th>Name</th>
      <th>Sex</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Ticket</th>
      <th>Fare</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>413</th>
      <td>1305</td>
      <td>3</td>
      <td>Spector, Mr. Woolf</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>A.5. 3236</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>414</th>
      <td>1306</td>
      <td>1</td>
      <td>Oliva y Ocana, Dona. Fermina</td>
      <td>female</td>
      <td>39.0</td>
      <td>0</td>
      <td>0</td>
      <td>PC 17758</td>
      <td>108.9000</td>
      <td>C105</td>
      <td>C</td>
    </tr>
    <tr>
      <th>415</th>
      <td>1307</td>
      <td>3</td>
      <td>Saether, Mr. Simon Sivertsen</td>
      <td>male</td>
      <td>38.5</td>
      <td>0</td>
      <td>0</td>
      <td>SOTON/O.Q. 3101262</td>
      <td>7.2500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>416</th>
      <td>1308</td>
      <td>3</td>
      <td>Ware, Mr. Frederick</td>
      <td>male</td>
      <td>NaN</td>
      <td>0</td>
      <td>0</td>
      <td>359309</td>
      <td>8.0500</td>
      <td>NaN</td>
      <td>S</td>
    </tr>
    <tr>
      <th>417</th>
      <td>1309</td>
      <td>3</td>
      <td>Peter, Master. Michael J</td>
      <td>male</td>
      <td>NaN</td>
      <td>1</td>
      <td>1</td>
      <td>2668</td>
      <td>22.3583</td>
      <td>NaN</td>
      <td>C</td>
    </tr>
  </tbody>
</table>
</div>




```python
train_data.describe()
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
      <th>PassengerId</th>
      <th>Survived</th>
      <th>Pclass</th>
      <th>Age</th>
      <th>SibSp</th>
      <th>Parch</th>
      <th>Fare</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>714.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
      <td>891.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>446.000000</td>
      <td>0.383838</td>
      <td>2.308642</td>
      <td>29.699118</td>
      <td>0.523008</td>
      <td>0.381594</td>
      <td>32.204208</td>
    </tr>
    <tr>
      <th>std</th>
      <td>257.353842</td>
      <td>0.486592</td>
      <td>0.836071</td>
      <td>14.526497</td>
      <td>1.102743</td>
      <td>0.806057</td>
      <td>49.693429</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.420000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>223.500000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>20.125000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>7.910400</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>446.000000</td>
      <td>0.000000</td>
      <td>3.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>14.454200</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>668.500000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>38.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>31.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>891.000000</td>
      <td>1.000000</td>
      <td>3.000000</td>
      <td>80.000000</td>
      <td>8.000000</td>
      <td>6.000000</td>
      <td>512.329200</td>
    </tr>
  </tbody>
</table>
</div>




```python
train_data.describe(include=['O'])
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
      <th>Name</th>
      <th>Sex</th>
      <th>Ticket</th>
      <th>Cabin</th>
      <th>Embarked</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>891</td>
      <td>891</td>
      <td>891</td>
      <td>204</td>
      <td>889</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>891</td>
      <td>2</td>
      <td>681</td>
      <td>147</td>
      <td>3</td>
    </tr>
    <tr>
      <th>top</th>
      <td>Allison, Mrs. Hudson J C (Bessie Waldo Daniels)</td>
      <td>male</td>
      <td>347082</td>
      <td>B96 B98</td>
      <td>S</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>1</td>
      <td>577</td>
      <td>7</td>
      <td>4</td>
      <td>644</td>
    </tr>
  </tbody>
</table>
</div>



## 模块 2：数据清洗



```python
# 使用平均年龄来填充年龄中的 nan 值
train_data['Age'].fillna(train_data['Age'].mean(), inplace=True)
test_data['Age'].fillna(test_data['Age'].mean(), inplace=True)
# 使用票价的均值填充票价中的 nan 值
train_data['Fare'].fillna(train_data['Fare'].mean(), inplace=True)
test_data['Fare'].fillna(test_data['Fare'].mean(), inplace=True)
```


```python
sum(train_data['Embarked'].value_counts())
train_data['Embarked'].value_counts()
```


    S    644
    C    168
    Q     77
    Name: Embarked, dtype: int64




```python
# 使用登录最多的港口来填充登录港口的 nan 值
train_data['Embarked'].fillna('S', inplace=True)
test_data['Embarked'].fillna('S', inplace=True)
```


```python
x = test_data.isnull().sum()
y = train_data.isnull().sum()
print(x, y)
```

    PassengerId      0
    Pclass           0
    Name             0
    Sex              0
    Age              0
    SibSp            0
    Parch            0
    Ticket           0
    Fare             0
    Cabin          327
    Embarked         0
    dtype: int64 PassengerId      0
    Survived         0
    Pclass           0
    Name             0
    Sex              0
    Age              0
    SibSp            0
    Parch            0
    Ticket           0
    Fare             0
    Cabin          687
    Embarked         0
    dtype: int64


## 模块 3：特征选择

通过数据探索我们发现，
- PassengerId 为乘客编号，对分类没有作用，可以放弃；
- Name 为乘客姓名，对分类没有作用，可以放弃；
- Cabin 字段缺失值太多，可以放弃；
- Ticket 字段为船票号码，杂乱无章且无规律，可以放弃。
- 其余的字段包括：Pclass、Sex、Age、SibSp、Parch 和 Fare，这些属性分别表示了乘客的船票等级、性别、年龄、亲戚数量以及船票价格，可能会和乘客的生存预测分类有关系。

具体是什么关系，我们可以交给分类器来处理。

```python
# 特征选择
features = ['Pclass', 'Sex', 'Age', 'SibSp', 'Parch', 'Fare', 'Embarked']
train_features = train_data[features]
train_labels = train_data['Survived']
test_features = test_data[features]
```


```python
from sklearn.feature_extraction import DictVectorizer
dvec = DictVectorizer(sparse=False)
train_features = dvec.fit_transform(train_features.to_dict(orient='record'))
```


```python
dvec.feature_names_
```


    ['Age',
     'Embarked=C',
     'Embarked=Q',
     'Embarked=S',
     'Fare',
     'Parch',
     'Pclass',
     'Sex=female',
     'Sex=male',
     'SibSp']




```python
train_data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 891 entries, 0 to 890
    Data columns (total 12 columns):
    PassengerId    891 non-null int64
    Survived       891 non-null int64
    Pclass         891 non-null int64
    Name           891 non-null object
    Sex            891 non-null object
    Age            891 non-null float64
    SibSp          891 non-null int64
    Parch          891 non-null int64
    Ticket         891 non-null object
    Fare           891 non-null float64
    Cabin          204 non-null object
    Embarked       891 non-null object
    dtypes: float64(2), int64(5), object(5)
    memory usage: 83.6+ KB



```python
test_data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 418 entries, 0 to 417
    Data columns (total 11 columns):
    PassengerId    418 non-null int64
    Pclass         418 non-null int64
    Name           418 non-null object
    Sex            418 non-null object
    Age            418 non-null float64
    SibSp          418 non-null int64
    Parch          418 non-null int64
    Ticket         418 non-null object
    Fare           418 non-null float64
    Cabin          91 non-null object
    Embarked       418 non-null object
    dtypes: float64(2), int64(4), object(5)
    memory usage: 36.0+ KB


## 模块 4：决策树模型


设置 `criterion='entropy'` 即为ID3 算法，默认为`criterion='ginn'` CART 算法，即在创建 DecisionTreeClassifier 时，然后使用 fit 进行训练，将特征值矩阵和分类标识结果作为参数传入，得到决策树分类器。


```python
from sklearn.tree import DecisionTreeClassifier
# 构造 ID3 决策树
clf = DecisionTreeClassifier(criterion='entropy')
# 决策树训练
clf.fit(train_features, train_labels)
```




    DecisionTreeClassifier(class_weight=None, criterion='entropy', max_depth=None,
                max_features=None, max_leaf_nodes=None,
                min_impurity_decrease=0.0, min_impurity_split=None,
                min_samples_leaf=1, min_samples_split=2,
                min_weight_fraction_leaf=0.0, presort=False, random_state=None,
                splitter='best')



## 模块 5：模型预测 & 评估


```python
test_features = dvec.transform(test_features.to_dict(orient='record'))
# 决策树预测
pred_labels = clf.predict(test_features)
```


```python
# 得到决策树准确率
acc_decision_tree = round(clf.score(train_features, train_labels), 6)
print(u'score 准确率为 %.4lf' % acc_decision_tree)
```

    score 准确率为 0.9820


会发现刚用训练集做训练，再用训练集自身做准确率评估自然会很高。但这样得出的准确率并不能代表决策树分类器的准确率。

这是为什么呢？

因为没有测试集的实际结果，因此无法用测试集的预测结果与实际结果做对比。如果使用 score 函数对训练集的准确率进行统计，正确率会接近于 100%（如上结果为 98.2%），无法对分类器的在实际环境下做准确率的评估。

那么有什么办法，来统计决策树分类器的准确率呢？

这里可以使用**K 折交叉验证**的方式，交叉验证是一种常用的验证分类准确率的方法，原理是拿出大部分样本进行训练，少量的用于分类器的验证。K 折交叉验证，就是做 K 次交叉验证，每次选取 K 分之一的数据作为验证，其余作为训练。轮流 K 次，取平均值。

K 折交叉验证的原理是这样的：

1. 将数据集平均分割成 K 个等份；
2. 使用 1 份数据作为测试数据，其余作为训练数据；
3. 计算测试准确率；
4. 使用不同的测试集，重复 2、3 步骤。

在 sklearn 的 model_selection 模型选择中提供了 cross_val_score 函数。cross_val_score 函数中的参数 cv 代表对原始数据划分成多少份，也就是 K 值，一般建议 K 值取 10，因此我们可以设置 CV=10，我们可以对比下 score 和 cross_val_score 两种函数的正确率的评估结果：


```python
import numpy as np
from sklearn.model_selection import cross_val_score
# 使用 K 折交叉验证 统计决策树准确率
print(u'cross_val_score 准确率为 %.4lf' %
      np.mean(cross_val_score(clf, train_features, train_labels, cv=10)))
```

    cross_val_score 准确率为 0.7836


## 模块 6：决策树可视化
sklearn 的决策树模型还是比较抽象的。可以使用 Graphviz 可视化工具帮把决策树呈现出来。


```python
# 最后利用graphviz库打印出决策树图
from sklearn import tree
import graphviz
dot_data = tree.export_graphviz(
    clf, out_file=None, filled=True, rounded=True, special_characters=True)
graph = graphviz.Source(dot_data)
graph.render("titanic_id3")
graph
```

![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.958wwvai2iv.png)
[查看决策树图详情](https://hufe09.github.io/html/titanic_id3.pdf)

### 参考
- https://time.geekbang.org/column/article/79072


### Bug
graphviz
`ExecutableNotFound: failed to execute ['dot', '-Tsvg'], make sure the Graphviz executables are on your systems' PATH`
解决：
- Windows下前往 [graphviz官网](https://graphviz.gitlab.io/_pages/Download/Download_windows.html/)下载即可
- ubuntu可以直接`sudo apt install graphviz`亦可，如果不行，请前往[graphviz官网](http://www.graphviz.org/download/)下载或者 可以直接到[Ubuntu官网](https://packages.ubuntu.com/search?keywords=graphviz&searchon=names)搜索graphviz包

环境依赖请参考—— Ubuntu下安装依赖的几个解决方案
`sudo apt-get -f install`

查看文件详情（Windows，Linux通用命令）
下载完成后，使用`dot -verison`命令，查询bin文件夹地址
查看Graphviz的bin位置
![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.s8yfd671h6k.png)
Ubuntu环境下加入环境变量
然后加入环境变量，`source /etc/profile` ,重启Jupyter Notebook使其生效。

`export PATH=$PATH:/usr/lib/x86_64-linux-gnu/graphviz`
此时错误已经解决。

![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.ypizj18p5km.png)



Code

> 点击此处获取完整代码[Kaggle](<https://www.kaggle.com/hufe09/titanic-survival-analysis>)

