---
layout: post
title: "Pandas使用"
subtitle: 'Python Pandas'
author: "Hufe"
header-img: "img/post-bg-python.jpg"
header-mask: 0.3
tags:
  - Python
  - Pandas
---
# Pandas


```python
import pandas as pd
```

## 1. 创建 Pandas Series


```python
import pandas as pd
pd.set_option('display.max_columns', 100)  # 设置显示数据的最大列数，防止出现省略号…，导致数据显示不全
pd.set_option('expand_frame_repr', False)  # 当列太多时不自动换行
groceries = pd.Series(data = [30,20,'Yes','No'], index = ['bananas','apple','pen','eggs'])
groceries
```




    bananas     30
    apple       20
    pen        Yes
    eggs        No
    dtype: object




```python
print('groceries的索引：',groceries.index)
print('groceries的数据：',groceries.values)
print('groceries的纬度：',groceries.ndim)
print('groceries的大小：',groceries.size)
print('groceries的形状：',groceries.shape)
```

    groceries的索引： Index(['bananas', 'apple', 'pen', 'eggs'], dtype='object')
    groceries的数据： [30 20 'Yes' 'No']
    groceries的纬度： 1
    groceries的大小： 4
    groceries的形状： (4,)



```python
#bananas是否在groceries内
x = 'bananas' in groceries
print(x)

#bananas是否在groceries内
y = 'bread' in groceries
print(y)
```

    True
    False


## 2. 访问和删除 Pandas Series 中的元素
###  [ ] 内添加索引标签或数字索引访问元素

>现在我们来了解如何访问或修改 Pandas Series 中的元素。Pandas Series 的一大优势是我们能够以很多不同的方式访问数据。我们可以通过在方括号 [ ] 内添加索引标签或数字索引访问元素，就像访问 NumPy ndarray 中的元素一样。因为我们可以使用数字索引，因此可以使用正整数从 Series 的开头访问数据，或使用负整数从末尾访问。因为我们可以通过多种方式访问元素，为了清晰地表明我们指代的是索引标签还是数字索引，Pandas Series 提供了两个属性 `.loc` 和 `.iloc`，帮助我们清晰地表明指代哪种情况。属性 `.loc` 表示 位置，用于明确表明我们使用的是标签索引。同样，属性 `.iloc` 表示整型位置，用于明确表明我们使用的是数字索引。我们来看一些示例：


```python
#使用索引标签访问食品杂货中的元素
#单个索引标签
print('杂货店是否有鸡蛋', groceries['eggs'])
print()

#多个索引标签
print('杂货店还有多少香蕉和苹果\n', groceries[['bananas','apple']])
print()

#用loc访问多个索引标签
print('杂货店是否有鸡蛋和笔\n', groceries.loc[['eggs','pen']])
print()

#使用数字索引访问食品杂货中的元素
#多个数字索引
print('杂货店剩余多少香蕉和苹果\n', groceries[[0, 1]])
print()

#负的数字索引
print('杂货店是否有笔', groceries[-2])
print()

#单个数字索引
print('杂货店剩余多少苹果', groceries[1])
print()

#用iloc访问多个数字标签
print('杂货店是否有鸡蛋\n', groceries.iloc[[0, 1]])
```

    杂货店是否有鸡蛋 No
    
    杂货店还有多少香蕉和苹果
     bananas    30
    apple      20
    dtype: object
    
    杂货店是否有鸡蛋和笔
     eggs     No
    pen     Yes
    dtype: object
    
    杂货店剩余多少香蕉和苹果
     bananas    30
    apple      20
    dtype: object
    
    杂货店是否有笔 Yes
    
    杂货店剩余多少苹果 20
    
    杂货店是否有鸡蛋
     bananas    30
    apple      20
    dtype: object


>和 NumPy ndarray 一样，Pandas Series 也是可变的，也就是说，创建好 Pandas Series 后，我们可以更改其中的元素。例如，我们更改下购物清单中的鸡蛋购买数量


```python
# We display the original grocery list
print('Original Grocery List:\n', groceries)

# We change the number of eggs to 2
groceries['eggs'] = 2

# We display the changed grocery list
print()
print('Modified Grocery List:\n', groceries)
```

    Original Grocery List:
     bananas     30
    apple       20
    pen        Yes
    eggs        No
    dtype: object
    
    Modified Grocery List:
     bananas     30
    apple       20
    pen        Yes
    eggs         2
    dtype: object



```python
groceries
```




    bananas     30
    apple       20
    pen        Yes
    eggs         2
    dtype: object



###  .drop() 方法删除 Pandas Series 中的条目
>我们还可以使用 `.drop()` 方法删除 Pandas Series 中的条目。`Series.drop(label)` 方法会从给定 Series 中删除给定的 label。请注意，`Series.drop(label)` 方法不在原地地从 Series 中删除元素，即不会更改被修改的原始 Series。我们来看看代码编写方式:


```python
print('原来的杂货店列表：\n',groceries)
print()

print('删除pen\n',groceries.drop('pen'))
print()

print('删除pen之后的杂货店列表:\n',groceries)
```

    原来的杂货店列表：
     bananas     30
    apple       20
    pen        Yes
    eggs         2
    dtype: object
    
    删除pen
     bananas    30
    apple      20
    eggs        2
    dtype: object
    
    删除pen之后的杂货店列表:
     bananas     30
    apple       20
    pen        Yes
    eggs         2
    dtype: object


>很明显`.drop()` 方法没有修改原来的Pandas Series列表。
>
>我们可以通过在 `.drop()` 方法中将关键字 `inplace` 设为 `True`，原地地从 Pandas Series 中删除条目。我们来看一个示例：


```python
print('原来的杂货店列表：\n',groceries)
print()

print('删除pen\n',groceries.drop('pen', inplace=True))
print()

print('删除pen之后的杂货店列表:\n',groceries)
```

    原来的杂货店列表：
     bananas     30
    apple       20
    pen        Yes
    eggs         2
    dtype: object
    
    删除pen
     None
    
    删除pen之后的杂货店列表:
     bananas    30
    apple      20
    eggs        2
    dtype: object


## 3. 对 Pandas Series 执行算术运算

>和 NumPy ndarray 一样，我们可以对 Pandas Series 执行元素级算术运算。在这节课，我们将了解 Pandas Series 和单个数字之间的算术运算。我们创建一个新的 Pandas Series，用于存储只有水果的购物清单。


```python
fruits = pd.Series(data = [10,6,7], index = ['apples', 'bananas', 'oranges'])
print(fruits)
```

    apples     10
    bananas     6
    oranges     7
    dtype: int64



```python
# We print fruits for reference
print('原始杂货店水果目录:\n ', fruits)

# We perform basic element-wise operations using arithmetic symbols
#使用算术符号执行基本元素操作。
print()
print('fruits + 2:\n', fruits + 2) # We add 2 to each item in fruits
print()
print('fruits - 2:\n', fruits - 2) # We subtract 2 to each item in fruits
print()
print('fruits * 2:\n', fruits * 2) # We multiply each item in fruits by 2 
print()
print('fruits / 2:\n', fruits / 2) # We divide each item in fruits by 2
print()
```

    原始杂货店水果目录:
      apples     10
    bananas     6
    oranges     7
    dtype: int64
    
    fruits + 2:
     apples     12
    bananas     8
    oranges     9
    dtype: int64
    
    fruits - 2:
     apples     8
    bananas    4
    oranges    5
    dtype: int64
    
    fruits * 2:
     apples     20
    bananas    12
    oranges    14
    dtype: int64
    
    fruits / 2:
     apples     5.0
    bananas    3.0
    oranges    3.5
    dtype: float64


​    

### 数学函数
>我们还可以对 Pandas Series 中的所有元素应用 NumPy 中的数学函数，例如 `sqrt(x)`。


```python
# We import NumPy as np to be able to use the mathematical functions
import numpy as np

# We print fruits for reference
print('Original grocery list of fruits:\n', fruits)

# We apply different mathematical functions to all elements of fruits
print()
print('EXP(X) = \n', np.exp(fruits))  #计算 e（自然对数的底，常数为2.71828）的指数
print() 
print('SQRT(X) =\n', np.sqrt(fruits))  #平方根
print()
print('POW(X,2) =\n',np.power(fruits,2)) # We raise all elements of fruits to the power of 2
```

    Original grocery list of fruits:
     apples     10
    bananas     6
    oranges     7
    dtype: int64
    
    EXP(X) = 
     apples     22026.465795
    bananas      403.428793
    oranges     1096.633158
    dtype: float64
    
    SQRT(X) =
     apples     3.162278
    bananas    2.449490
    oranges    2.645751
    dtype: float64
    
    POW(X,2) =
     apples     100
    bananas     36
    oranges     49
    dtype: int64


### 算术运算
>Pandas 还允许我们仅对 fruits 购物清单中的部分条目应用算术运算。我们来看一些示例：


```python
# We print fruits for reference
print('Original grocery list of fruits:\n ', fruits)
print()

# We add 2 only to the bananas
print('Amount of bananas + 2 = ', fruits['bananas'] + 2)
print()

# We subtract 2 from apples
print('Amount of apples - 2 = ', fruits.iloc[0] - 2)
print()

# We multiply apples and oranges by 2
print('We double the amount of apples and oranges:\n', fruits[['apples', 'oranges']] * 2)
print()

# We divide apples and oranges by 2
print('We half the amount of apples and oranges:\n', fruits.loc[['apples', 'oranges']] / 2)
```

    Original grocery list of fruits:
      apples     10
    bananas     6
    oranges     7
    dtype: int64
    
    Amount of bananas + 2 =  8
    
    Amount of apples - 2 =  8
    
    We double the amount of apples and oranges:
     apples     20
    oranges    14
    dtype: int64
    
    We half the amount of apples and oranges:
     apples     5.0
    oranges    3.5
    dtype: float64


>你还可以对具有混合数据类型的 Pandas Series 应用算术运算，前提是该算术运算适合 Series 中的所有数据类型，否则会出错。我们来看看将购物清单乘以 2 会发生什么


```python
# We multiply our grocery list by 2
groceries['gees'] = 'No'
groceries * 2
```




    bananas      60
    apple        40
    eggs          4
    gees       NoNo
    dtype: object



## 4. 创建 Pandas DataFrame

>Pandas DataFrames 是具有带标签的行和列的二维数据结构，可以存储很多类型的数据。如果你熟悉 Excel 的话，可以将 Pandas DataFrames 看做类似于电子表格。在接下来的课程中，我们将开始学习如何手动地通过字典创建 Pandas DataFrame，稍后，我们将学习如何将数据文件中的数据加载到 DataFrame 中。
>
>首先，我们将使用 Pandas Series 字典手动创建一个 DataFrame。第一步是创建 Pandas Series 字典。字典创建完毕后，我们可以将该字典传递给 `pd.DataFrame()` 函数。
>
>我们将创建一个字典，其中包含 Alice 和 Bob 从在线商店中购买的商品。该 Pandas Series 将使用所买商品的价格作为数据，所买商品作为索引标签。我们来看看如何编写代码：


```python
import pandas as pd
items = {'Bob' : pd.Series(data = [245, 34, 66], index = ['bike', 'pants', 'watch']), 
         'Alice' : pd.Series(data = [30, 500, 700, 1000], index = ['book', 'glasses', 'bike', 'pants'])}
print(type(items))
```

    <class 'dict'>



```python
shopping_carts = pd.DataFrame(items)
shopping_carts
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
      <th>Bob</th>
      <th>Alice</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>bike</th>
      <td>245.0</td>
      <td>700.0</td>
    </tr>
    <tr>
      <th>book</th>
      <td>NaN</td>
      <td>30.0</td>
    </tr>
    <tr>
      <th>glasses</th>
      <td>NaN</td>
      <td>500.0</td>
    </tr>
    <tr>
      <th>pants</th>
      <td>34.0</td>
      <td>1000.0</td>
    </tr>
    <tr>
      <th>watch</th>
      <td>66.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



>有几个事项需要注意。我们发现 DataFrame 以表格形式显示，和 Excel 电子表格很像，行和列的标签以粗体形式显示。此外注意，DataFrame 的行标签根据构建字典所用的两个 Pandas Series 的索引标签创建而成。DataFrame 的列标签来自字典的键。另一个注意事项是，列按照字母顺序排序，而不是字典中的顺序。稍后我们将发现，当我们从数据文件中向 DataFrame 加载数据时，不会发生这种情况。最后要注意的是，我们发现该 DataFrame 中出现了一些 `NaN` 值。`NaN` 是指非数字，Pandas 通过这种方式表示该行和列索引没有值。例如，如果我们查看 Alice 列，我们发现手表索引的值是 `NaN`。你可以通过查看一开始创建的字典，了解为何是这种情况。可以清晰地看出，Alice 手表标签没有条目。因此，在创建 DataFrame 时，如果特定行索引的特定列没有值，Pandas 将用 `NaN` 值填充。如果要将此数据馈送到机器学习算法中，我们首先需要删掉这些 `NaN` 值。在后面的课程中，我们将学习如何处理 `NaN` 值以及如何清理数据。暂时先将这些值留在我们的 DataFrame 中。
>
>在上述示例中，我们使用具有定义清晰的索引的 Pandas Series 字典创建了 Pandas DataFrame。如果我们不向 Pandas Series 提供索引标签，Pandas 在创建 DataFrame 时将使用数字行索引。我们来看一个示例：


```python
data = {'Bob' : pd.Series([234,567,890]), 'Alice' : pd.Series([10, 20, 30, 40])}
df = pd.DataFrame(data)
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
      <th>Bob</th>
      <th>Alice</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>234.0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>567.0</td>
      <td>20</td>
    </tr>
    <tr>
      <th>2</th>
      <td>890.0</td>
      <td>30</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>40</td>
    </tr>
  </tbody>
</table>
</div>



### 使用属性从 DataFrame 中提取信息
>可以看出，Pandas DataFrame 的行索引从 0 开始，就像 NumPy ndarray 的索引一样。
>
>现在，和 Pandas Series 一样，我们也可以使用属性从 DataFrame 中提取信息。我们输出 **shopping_carts** DataFrame 中的一些信息


```python
print('shopping_carts 的数据为：\n',shopping_carts.values)
print('shopping_carts 的形状为{0}：有{0[0]}行，{0[1]}列'.format(shopping_carts.shape))
print('shopping_carts 的纬度为：',shopping_carts.ndim, '维')
print('shopping_carts 的大小为：',shopping_carts.size, '个元素')
print('shopping_carts 的行索引为：',shopping_carts.index)
print('shopping_carts 的列索引为：',shopping_carts.columns)
```

    shopping_carts 的数据为：
     [[ 245.  700.]
     [  nan   30.]
     [  nan  500.]
     [  34. 1000.]
     [  66.   nan]]
    shopping_carts 的形状为(5, 2)：有5行，2列
    shopping_carts 的纬度为： 2 维
    shopping_carts 的大小为： 10 个元素
    shopping_carts 的行索引为： Index(['bike', 'book', 'glasses', 'pants', 'watch'], dtype='object')
    shopping_carts 的列索引为： Index(['Bob', 'Alice'], dtype='object')


>在 shopping_carts DataFrame 时，我们将整个字典传递给了 pd.DataFrame() 函数。但是，有时候你可能只对一部分数据感兴趣。在 Pandas 中，我们可以通过关键字 columns 和 index 选择要将哪些数据放入 DataFrame 中。我们来看一些示例：


```python
bob_shopping_carts = pd.DataFrame(items, columns=['Bob'])
bob_shopping_carts
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
      <th>Bob</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>bike</th>
      <td>245</td>
    </tr>
    <tr>
      <th>pants</th>
      <td>34</td>
    </tr>
    <tr>
      <th>watch</th>
      <td>66</td>
    </tr>
  </tbody>
</table>
</div>




```python
al_shopping_carts = pd.DataFrame(items, index = ['glasses', 'bike'])
al_shopping_carts
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
      <th>Bob</th>
      <th>Alice</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>glasses</th>
      <td>NaN</td>
      <td>500</td>
    </tr>
    <tr>
      <th>bike</th>
      <td>245.0</td>
      <td>700</td>
    </tr>
  </tbody>
</table>
</div>




```python
alice_shopping_carts = pd.DataFrame(items, columns=['Alice'], index = ['glasses', 'bike'])
alice_shopping_carts
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
      <th>Alice</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>glasses</th>
      <td>500</td>
    </tr>
    <tr>
      <th>bike</th>
      <td>700</td>
    </tr>
  </tbody>
</table>
</div>



>你还可以使用列表（数组）字典手动地创建 DataFrame。流程和之前一样，首先创建一个字典，然后将该字典传递给 pd.DataFrame() 函数。但是在这种情况下，字典中的所有列表（数组）长度必须一样。我们来看一个示例：


```python
data_dic = {'waha': [1,2,4], 'haha' : [3.4, 5.6, 7.8]}
data_dic_ex = pd.DataFrame(data_dic)
data_dic_ex
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
      <th>waha</th>
      <th>haha</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>3.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>5.6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>4</td>
      <td>7.8</td>
    </tr>
  </tbody>
</table>
</div>



### 使用关键字 index，为行索引添加标签
>注意，因为我们创建的 data 字典没有标签索引，因此 Pandas 在创建 DataFrame 时自动使用数字行索引。但是，我们可以通过在 `pd.DataFrame()` 函数中使用关键字 `index`，为行索引添加标签。我们来看一个示例：


```python
data_dic = {'waha': [1,2,4], 'haha' : [3.4, 5.6, 7.8]}
data_dic_index = pd.DataFrame(data_dic, index = ['index1', 'index2', 'index3'])
data_dic_index
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
      <th>waha</th>
      <th>haha</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>index1</th>
      <td>1</td>
      <td>3.4</td>
    </tr>
    <tr>
      <th>index2</th>
      <td>2</td>
      <td>5.6</td>
    </tr>
    <tr>
      <th>index3</th>
      <td>4</td>
      <td>7.8</td>
    </tr>
  </tbody>
</table>
</div>



### 使用 Python 字典列表手动创建 Pandas DataFrame 
>手动创建 Pandas DataFrame 的最后一种方式是使用 Python 字典列表。流程和之前一样，我们先创建字典，然后将该字典传递给 pd.DataFrame() 函数。


```python
dic_items =[{'bikes': 20, 'pants':30, 'watches':40 }, 
            {'bikes': 10, 'glasses': 9999, 'pants': 999, 'watches': 1999}]
store_items = pd.DataFrame(dic_items)
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>40</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>1999</td>
    </tr>
  </tbody>
</table>
</div>



>同样注意，因为我们创建的 items2 字典没有标签索引，因此 Pandas 在创建 DataFrame 时自动使用数字行索引。和之前一样，我们可以通过在 pd.DataFrame() 函数中使用关键字 index，为行索引添加标签。假设我们将使用该 DataFrame 存储某个商店的商品库存数量。我们将行索引的标签设为 store 1 和 store 2。


```python
dic_items =[{'bikes': 20, 'pants':30, 'watches':40 }, 
            {'bikes': 10, 'glasses': 9999, 'pants': 999, 'watches': 1999}]
store_items = pd.DataFrame(dic_items, index = ['store1', 'store2'])
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>40</td>
    </tr>
    <tr>
      <th>store2</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>1999</td>
    </tr>
  </tbody>
</table>
</div>



## 5. 访问 Pandas DataFrame 中的元素

>我们可以通过多种不同的方式访问 Pandas DataFrame 中的元素。通常，我们可以使用行和列标签访问 DataFrame 的行、列或单个元素。我们将使用在上节课创建的同一 store_items DataFrame。我们来看一些示例：


```python
# We print the store_items DataFrame
print(store_items)

# We access rows, columns and elements using labels
print()
print('How many glasses are in each store:\n', store_items[['glasses']])
print()
print('How many bikes and pants are in each store:\n', store_items[['bikes', 'pants']])
print()
print('What items are in Store1:\n', store_items.loc[['store1']])
print()
print('How many bikes are in Store2:', store_items['bikes']['store2'])
```

            bikes  glasses  pants  watches
    store1     20      NaN     30       40
    store2     10   9999.0    999     1999
    
    How many glasses are in each store:
             glasses
    store1      NaN
    store2   9999.0
    
    How many bikes and pants are in each store:
             bikes  pants
    store1     20     30
    store2     10    999
    
    What items are in Store1:
             bikes  glasses  pants  watches
    store1     20      NaN     30       40
    
    How many bikes are in Store2: 10


>请注意，在访问 DataFrame 中的单个元素时，就像上个示例一样，**必须始终提供标签，并且列标签在前**，格式为 `dataframe[column][row]`。例如，在检索商店 2 中的自行车数量时，我们首先使用列标签 **bikes**，然后使用行标签 **store 2**。如果先提供行标签，将出错。
### 如何向 DataFrame 中添加新的列
>我们还可以通过添加行或列修改 DataFrame。我们先了解如何向 DataFrame 中添加新的列。假设我们想添加每个商店的衬衫库存。为此，我们需要向 store_items DataFrame 添加一个新列，表示每个商店的衬衫库存。我们来编写代码：


```python
# We add a new column named shirts to our store_items DataFrame indicating the number of shirts in stock at each store. We
# will put 15 shirts in store 1 and 2 shirts in store 2
store_items['shirts'] = [15,2]

# We display the modified DataFrame
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>watches</th>
      <th>shirts</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>40</td>
      <td>15</td>
    </tr>
    <tr>
      <th>store2</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>1999</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



>以看出，当我们添加新的列时，新列添加到了 DataFrame 的末尾。
### 使用算术运算符向 DataFrame 中的其他列之间添加新列
>还可以使用算术运算符向 DataFrame 中的其他列之间添加新列。我们来看一个示例：


```python
# We make a new column called suits by adding the number of shirts and pants
store_items['suits'] = store_items['pants'] + store_items['shirts']

# We display the modified DataFrame
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>watches</th>
      <th>shirts</th>
      <th>suits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>40</td>
      <td>15</td>
      <td>45</td>
    </tr>
    <tr>
      <th>store2</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>1999</td>
      <td>2</td>
      <td>1001</td>
    </tr>
  </tbody>
</table>
</div>



>假设现在你开了一家新店，需要将该商店的商品库存添加到 DataFrame 中。为此，我们可以向 store_items Dataframe 中添加一个新行。要向 DataFrame 中添加行，我们首先需要创建新的 Dataframe，然后将其附加到原始 DataFrame 上。我们来看看代码编写方式


```python
# We create a dictionary from a list of Python dictionaries that will number of items at the new store
new_items = [{'bikes': 20, 'pants': 30, 'watches': 35, 'glasses': 4}]

# We create new DataFrame with the new_items and provide and index labeled store 3
new_store = pd.DataFrame(new_items, index = ['store3'])

# We display the items at the new store
new_store
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store3</th>
      <td>20</td>
      <td>4</td>
      <td>30</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



### .append() 方法将此行添加到 store_items DataFrame 中
>现在，我们使用 `.append()` 方法将此行添加到 store_items DataFrame 中。


```python
store_items = store_items.append(new_store)
store_items
```

    E:\ProgramData\Anaconda3\envs\data_analysis\lib\site-packages\pandas\core\frame.py:6211: FutureWarning: Sorting because non-concatenation axis is not aligned. A future version
    of pandas will change to not sort by default.
    
    To accept the future behavior, pass 'sort=False'.
    
    To retain the current behavior and silence the warning, pass 'sort=True'.
    
      sort=sort)





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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>15.0</td>
      <td>45.0</td>
      <td>40</td>
    </tr>
    <tr>
      <th>store2</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>2.0</td>
      <td>1001.0</td>
      <td>1999</td>
    </tr>
    <tr>
      <th>store3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



>注意，将新行附加到 DataFrame 后，列按照字母顺序排序了。
>
>我们还可以仅使用特定列的特定行中的数据向 DataFrame 添加新的列。例如，假设你想在商店 2 和 3 中上一批新手表，并且新手表的数量与这些商店原有手表的库存一样。我们来看看如何编写代码


```python
store_items['new watches'] = store_items['watches'][0:]
print(store_items)
store_items['new watches'] = store_items['watches'][1:]
store_items
```

            bikes  glasses  pants  shirts   suits  watches  new watches
    store1     20      NaN     30    15.0    45.0       40           40
    store2     10   9999.0    999     2.0  1001.0     1999         1999
    store3     20      4.0     30     NaN     NaN       35           35





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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>suits</th>
      <th>watches</th>
      <th>new watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>15.0</td>
      <td>45.0</td>
      <td>40</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>store2</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>2.0</td>
      <td>1001.0</td>
      <td>1999</td>
      <td>1999.0</td>
    </tr>
    <tr>
      <th>store3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>35</td>
      <td>35.0</td>
    </tr>
  </tbody>
</table>
</div>



### dataframe.insert(loc,label,data)方法
>我们还可以将新列插入 DataFrames 的任何位置。`dataframe.insert(loc,label,data) `方法使我们能够将新列（具有给定列标签和给定数据）插入 dataframe 的 `loc` 位置。我们将名称为 **shoes** 的新列插入 **suits** 列前面。因为 **suits** 的数字索引值为 4，我们将此值作为 `loc`。我们来看看代码编写方式：


```python
store_items.insert(5, 'shoes', [40, 34, 35])
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>suits</th>
      <th>shoes</th>
      <th>watches</th>
      <th>new watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>15.0</td>
      <td>45.0</td>
      <td>40</td>
      <td>40</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>store2</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>2.0</td>
      <td>1001.0</td>
      <td>34</td>
      <td>1999</td>
      <td>1999.0</td>
    </tr>
    <tr>
      <th>store3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>35</td>
      <td>35</td>
      <td>35.0</td>
    </tr>
  </tbody>
</table>
</div>



### .pop() 和 .drop() 方法删除元素
>就像我们可以添加行和列一样，我们也可以删除它们。要删除 DataFrame 中的行和列，我们将使用 `.pop()` 和 `.drop()` 方法。`.pop()` 方法仅允许我们删除列，而 .drop() 方法可以同时用于删除行和列，只需使用关键字 `axis` 即可。我们来看一些示例：


```python
store_items
store_items.pop('new watches')
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>suits</th>
      <th>shoes</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>15.0</td>
      <td>45.0</td>
      <td>40</td>
      <td>40</td>
    </tr>
    <tr>
      <th>store2</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>2.0</td>
      <td>1001.0</td>
      <td>34</td>
      <td>1999</td>
    </tr>
    <tr>
      <th>store3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>35</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>




```python
# We remove the watches and shoes columns
store_items = store_items.drop(['watches', 'shoes'], axis = 1)

# we display the modified DataFrame
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>suits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>15.0</td>
      <td>45.0</td>
    </tr>
    <tr>
      <th>store2</th>
      <td>10</td>
      <td>9999.0</td>
      <td>999</td>
      <td>2.0</td>
      <td>1001.0</td>
    </tr>
    <tr>
      <th>store3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>




```python
# We remove the store 2 and store 1 rows
store_items = store_items.drop(['store2', 'store1'], axis = 0)

# we display the modified DataFrame
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>suits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



>有时候，我们可能需要更改行和列标签。我们使用 `.rename()` 方法将 **bikes** 列标签改为 **hats**


```python
# We change the column label bikes to hats
store_items = store_items.rename(columns = {'bikes': 'hats'})

# we display the modified DataFrame
store_items
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
      <th>hats</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>suits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



### .rename() 方法更改行标签
>现在再次使用 `.rename()` 方法更改行标签。


```python
store_items = store_items.rename(index = {'store3': 'last store'})
store_items
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
      <th>hats</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>suits</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>last store</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



>你还可以将索引改为 DataFrame 中的某个列。


```python
# We change the row index to be the data in the pants column
store_items = store_items.set_index('pants')

# we display the modified DataFrame
store_items
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
      <th>hats</th>
      <th>glasses</th>
      <th>shirts</th>
      <th>suits</th>
    </tr>
    <tr>
      <th>pants</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>30</th>
      <td>20</td>
      <td>4.0</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
</div>



## 6. 处理NaN

>正如之前提到的，在能够使用大型数据集训练学习算法之前，我们通常需要先清理数据。也就是说，我们需要通过某个方法检测并更正数据中的错误。虽然任何给定数据集可能会出现各种糟糕的数据，例如离群值或不正确的值，但是我们几乎始终会遇到的糟糕数据类型是缺少值。正如之前看到的，Pandas 会为缺少的值分配 NaN 值。在这节课，我们将学习如何检测和处理 `NaN` 值。
>
>首先，我们将创建一个具有一些 `NaN` 值的 DataFrame。


```python
# We create a list of Python dictionaries
items2 = [{'bikes': 20, 'pants': 30, 'watches': 35, 'shirts': 15, 'shoes':8, 'suits':45},
{'watches': 10, 'glasses': 50, 'bikes': 15, 'pants':5, 'shirts': 2, 'shoes':5, 'suits':7},
{'bikes': 20, 'pants': 30, 'watches': 35, 'glasses': 4, 'shoes':10}]

# We create a DataFrame  and provide the row index
store_items = pd.DataFrame(items2, index = ['store 1', 'store 2', 'store 3'])

# We display the DataFrame
store_items
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>15.0</td>
      <td>8</td>
      <td>45.0</td>
      <td>35</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15</td>
      <td>50.0</td>
      <td>5</td>
      <td>2.0</td>
      <td>5</td>
      <td>7.0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>10</td>
      <td>NaN</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



### .isnull() 和 sum() 方法来计算我们的 DataFrame 中的 NaN 值的数量
>可以清晰地看出，我们创建的 DataFrame 具有 3 个 NaN 值：商店 1 中有一个，商店 3 中有两个。但是，如果我们向 DataFrame 中加载非常庞大的数据集，可能有数百万条数据，那么就不太容易直观地发现 `NaN` 值的数量。对于这些情形，我们结合使用多种方法来计算数据中的 `NaN` 值的数量。以下示例同时使用了 `.isnull()` 和 `sum()` 方法来计算我们的 DataFrame 中的 `NaN` 值的数量。


```python
x = store_items.isnull().sum().sum()
print('DataFrame中NaN的个数是：', x)
```

    DataFrame中NaN的个数是： 3


>在上述示例中，`.isnull()` 方法返回一个大小和 `store_items` 一样的布尔型 DataFrame，并用 `True` 表示具有 `NaN` 值的元素，用 `False` 表示非 `NaN` 值的元素。我们来看一个示例：


```python
store_items.isnull()
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



>在 Pandas 中，逻辑值 `True` 的数字值是 1，逻辑值 `False` 的数字值是 0。因此，我们可以通过数逻辑值 `True` 的数量数出 `NaN` 值的数量。为了数逻辑值 `True` 的总数，我们使用 `.sum()` 方法两次。要使用该方法两次，是因为第一个 `sum()` 返回一个 Pandas Series，其中存储了列上的逻辑值 `True` 的总数，如下所示：


```python
store_items.isnull().sum()
```




    bikes      0
    glasses    1
    pants      0
    shirts     1
    shoes      0
    suits      1
    watches    0
    dtype: int64



>第二个 sum() 将上述 Pandas Series 中的 1 相加。
除了数 NaN 值的数量之外，我们还可以采用相反的方式，我们可以数*非 NaN* 值的数量。为此，我们可以使用 `.count()` 方法，如下所示：


```python
print()
print('Number of non-NaN values in the columns of our DataFrame:\n', store_items.count())
```


    Number of non-NaN values in the columns of our DataFrame:
     bikes      3
    glasses    2
    pants      3
    shirts     2
    shoes      3
    suits      2
    watches    3
    dtype: int64


### .dropna(axis) 方法将删除包含 NaN 值的任何行或列
>现在我们已经知道如何判断数据集中是否有任何 `NaN` 值，下一步是决定如何处理这些 NaN 值。通常，我们有两种选择，可以删除或替换 `NaN` 值。在下面的示例中，我们将介绍这两种方式。
>
>首先，我们将学习如何从 DataFrame 中删除包含任何 `NaN` 值的行或列。如果 `axis = 0`，`.dropna(axis)` 方法将删除包含 `NaN` 值的任何*行*，如果 `axis = 1`，`.dropna(axis)` 方法将删除包含 `NaN` 值的任何*列*。我们来看一些示例：


```python
# We drop any rows with NaN values
# 我们删除任何含有NaN值的行
store_items.dropna(axis = 0)
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 2</th>
      <td>15</td>
      <td>50.0</td>
      <td>5</td>
      <td>2.0</td>
      <td>5</td>
      <td>7.0</td>
      <td>10</td>
    </tr>
  </tbody>
</table>
</div>




```python
# We drop any columns with NaN values
# 我们删除任何含有NaN值的列
store_items.dropna(axis = 1)
#store_items.dropna(axis = 1, inplace = True) #同时删除原始 DataFrame 中任何含有NaN值的列
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
      <th>bikes</th>
      <th>pants</th>
      <th>shoes</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20</td>
      <td>30</td>
      <td>8</td>
      <td>35</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15</td>
      <td>5</td>
      <td>5</td>
      <td>10</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20</td>
      <td>30</td>
      <td>10</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



>注意，`.dropna()` 方法不在原地地删除具有 `NaN` 值的行或列。也就是说，原始 DataFrame 不会改变。你始终可以在 dropna() 方法中将关键字 `inplace` 设为 `True`，在原地删除目标行或列。
### 使用 .fillna() 方法将NaN 值替换为合适的值
>现在，我们不再删除 `NaN` 值，而是将它们替换为合适的值。例如，我们可以选择将所有 `NaN` 值替换为 0。为此，我们可以使用 `.fillna()` 方法，如下所示。


```python
# We replace all NaN values with 0
# 我们用0替换NaN值
store_items.fillna(0)
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20</td>
      <td>0.0</td>
      <td>30</td>
      <td>15.0</td>
      <td>8</td>
      <td>45.0</td>
      <td>35</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15</td>
      <td>50.0</td>
      <td>5</td>
      <td>2.0</td>
      <td>5</td>
      <td>7.0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>0.0</td>
      <td>10</td>
      <td>0.0</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



####  .fillna(method = 'ffill', axis)方法前向填充
>我们还可以使用 `.fillna()` 方法将 `NaN` 值替换为 DataFrame 中的上个值，称之为前向填充。在通过前向填充替换 `NaN` 值时，我们可以使用列或行中的上个值。`.fillna(method = 'ffill', axis)` 将通过前向填充 `(ffill)` 方法沿着给定 `axis` 使用上个已知值替换 `NaN` 值。我们来看一些示例：


```python
# We replace NaN values with the previous value in the column
# 我们用列中的前一个值替换NaN值
store_items.fillna(method = 'ffill', axis = 0)
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>15.0</td>
      <td>8</td>
      <td>45.0</td>
      <td>35</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15</td>
      <td>50.0</td>
      <td>5</td>
      <td>2.0</td>
      <td>5</td>
      <td>7.0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>2.0</td>
      <td>10</td>
      <td>7.0</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



>注意 store 3 中的两个 `NaN` 值被替换成了它们所在列中的上个值。但是注意， store 1 中的 `NaN` 值没有被替换掉。因为这列前面没有值，因为 `NaN` 值是该列的第一个值。但是，如果使用上个行值进行前向填充，则不会发生这种情况。我们来看看具体情形：


```python
# We replace NaN values with the previous value in the row
# 我们用行中的前一个值替换NaN值
store_items.fillna(method = 'ffill', axis = 1)
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20.0</td>
      <td>20.0</td>
      <td>30.0</td>
      <td>15.0</td>
      <td>8.0</td>
      <td>45.0</td>
      <td>35.0</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15.0</td>
      <td>50.0</td>
      <td>5.0</td>
      <td>2.0</td>
      <td>5.0</td>
      <td>7.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20.0</td>
      <td>4.0</td>
      <td>30.0</td>
      <td>30.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>35.0</td>
    </tr>
  </tbody>
</table>
</div>



>我们看到，在这种情形下，所有 `NaN` 值都被替换成了之前的行值。
#### .fillna(method = 'backfill', axis)方法后向填充
>同样，你可以选择用 DataFrame 中之后的值替换 `NaN` 值，称之为后向填充。`.fillna(method = 'backfill', axis)` 将通过后向填充 `(backfill)` 方法沿着给定 axis 使用下个已知值替换 `NaN` 值。和前向填充一样，我们可以选择使用行值或列值。我们来看一些示例：


```python
# We replace NaN values with the next value in the column
# 我们用列中的后一个值替换NaN值
store_items.fillna(method = 'backfill', axis = 0)
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20</td>
      <td>50.0</td>
      <td>30</td>
      <td>15.0</td>
      <td>8</td>
      <td>45.0</td>
      <td>35</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15</td>
      <td>50.0</td>
      <td>5</td>
      <td>2.0</td>
      <td>5</td>
      <td>7.0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>NaN</td>
      <td>10</td>
      <td>NaN</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



>注意，store 1 中的 `NaN` 值被替换成了它所在列的下个值。但是注意，store 3 中的两个 `NaN` 值没有被替换掉。因为这些列中没有下个值，这些 `NaN` 值是这些列中的最后一个值。但是，如果使用下个行值进行后向填充，则不会发生这种情况。我们来看看具体情形：


```python
# We replace NaN values with the next value in the row
# 我们用行中的后一个值替换NaN值
store_items.fillna(method = 'backfill', axis = 1)
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20.0</td>
      <td>30.0</td>
      <td>30.0</td>
      <td>15.0</td>
      <td>8.0</td>
      <td>45.0</td>
      <td>35.0</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15.0</td>
      <td>50.0</td>
      <td>5.0</td>
      <td>2.0</td>
      <td>5.0</td>
      <td>7.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20.0</td>
      <td>4.0</td>
      <td>30.0</td>
      <td>10.0</td>
      <td>10.0</td>
      <td>35.0</td>
      <td>35.0</td>
    </tr>
  </tbody>
</table>
</div>



>注意，`.fillna()` 方法不在原地替换（填充）`NaN` 值。也就是说，原始 DataFrame 不会改变。你始终可以在 `fillna()` 函数中将关键字 `inplace` 设为 `True`，在原地替换 `NaN` 值。
### .interpolate(method = 'linear', axis)用列值线性插值代替NaN值
>我们还可以选择使用不同的插值方法替换 `NaN` 值。例如，`.interpolate(method = 'linear', axis)` 方法将通过 `linear` 插值使用沿着给定 axis 的值替换 `NaN` 值。我们来看一些示例：


```python
# We replace NaN values by using linear interpolation using column values
# 用列值线性插值代替NaN值
store_items.interpolate(method = 'linear', axis = 0)
print(store_items)
store_items.interpolate(method = 'linear', axis = 0, inplace = True) #同时删除原始 DataFrame 中任何含有NaN值的列)
store_items
```

             bikes  glasses  pants  shirts  shoes  suits  watches
    store 1     20      NaN     30    15.0      8   45.0       35
    store 2     15     50.0      5     2.0      5    7.0       10
    store 3     20      4.0     30     NaN     10    NaN       35





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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20</td>
      <td>NaN</td>
      <td>30</td>
      <td>15.0</td>
      <td>8</td>
      <td>45.0</td>
      <td>35</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15</td>
      <td>50.0</td>
      <td>5</td>
      <td>2.0</td>
      <td>5</td>
      <td>7.0</td>
      <td>10</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20</td>
      <td>4.0</td>
      <td>30</td>
      <td>2.0</td>
      <td>10</td>
      <td>7.0</td>
      <td>35</td>
    </tr>
  </tbody>
</table>
</div>



>注意，store 3 中的两个 `NaN` 值被替换成了线性插值。但是注意，store 1 中的 `NaN` 值没有被替换掉。因为该 `NaN` 值是该列中的第一个值，因为它前面没有数据，因此插值函数无法计算值。现在，我们使用行值插入值：


```python
# We replace NaN values by using linear interpolation using row values
# 用行值线性插值代替NaN值
store_items.interpolate(method = 'linear', axis = 1)
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
      <th>bikes</th>
      <th>glasses</th>
      <th>pants</th>
      <th>shirts</th>
      <th>shoes</th>
      <th>suits</th>
      <th>watches</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store 1</th>
      <td>20.0</td>
      <td>25.0</td>
      <td>30.0</td>
      <td>15.0</td>
      <td>8.0</td>
      <td>45.0</td>
      <td>35.0</td>
    </tr>
    <tr>
      <th>store 2</th>
      <td>15.0</td>
      <td>50.0</td>
      <td>5.0</td>
      <td>2.0</td>
      <td>5.0</td>
      <td>7.0</td>
      <td>10.0</td>
    </tr>
    <tr>
      <th>store 3</th>
      <td>20.0</td>
      <td>4.0</td>
      <td>30.0</td>
      <td>2.0</td>
      <td>10.0</td>
      <td>7.0</td>
      <td>35.0</td>
    </tr>
  </tbody>
</table>
</div>



>和我们看到的其他方法一样，`.interpolate()` 方法不在原地地替换 `NaN` 值。

## 7. 将数据加载到 Pandas DataFrame 中

>在机器学习中，你很有可能会使用来自很多来源的数据库训练学习算法。Pandas 使我们能够将不同格式的数据库加载到 DataFrame 中。用于存储数据库的最热门数据格式是 csv。CSV 是指逗号分隔值，是一种简单的数据存储格式。我们可以使用 `pd.read_csv()` 函数将 CSV 文件加载到 Pandas DataFrame 中。我们将 Google 股票数据加载到一个 Pandas DataFrame 中。GOOG.csv 文件包含从雅虎金融那获取的 2004 年 8 月 19 日至 2017 年 10 月 13 日 Google 股票数据。




```python
# 我们将 Google 股票数据加载到 DataFrame 中
Google_stock = pd.read_csv('./GOOG.csv')

# 我们输出关于 Google_stock 的一些信息
print('Google_stock is of type:', type(Google_stock))
print('Google_stock has shape:', Google_stock.shape)
```

    Google_stock is of type: <class 'pandas.core.frame.DataFrame'>
    Google_stock has shape: (10, 7)


>可以看出，我们将 GOOG.csv 文件加载到了 Pandas DataFrame 中，其中包含 3,313 行和 7 列数据。现在我们来看看股票数据


```python
Google_stock
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
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2004/8/19</td>
      <td>49.676899</td>
      <td>51.693783</td>
      <td>47.669952</td>
      <td>49.845802</td>
      <td>49.845802</td>
      <td>44994500</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2004/8/20</td>
      <td>50.178635</td>
      <td>54.187561</td>
      <td>49.925285</td>
      <td>53.805050</td>
      <td>53.805050</td>
      <td>23005800</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2004/8/23</td>
      <td>55.017166</td>
      <td>56.373344</td>
      <td>54.172661</td>
      <td>54.346527</td>
      <td>54.346527</td>
      <td>18393200</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2004/8/24</td>
      <td>55.260582</td>
      <td>55.439419</td>
      <td>51.450363</td>
      <td>52.096165</td>
      <td>52.096165</td>
      <td>15361800</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2004/8/25</td>
      <td>52.140873</td>
      <td>53.651051</td>
      <td>51.604362</td>
      <td>52.657513</td>
      <td>52.657513</td>
      <td>9257400</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2017/10/9</td>
      <td>980.000000</td>
      <td>985.424988</td>
      <td>976.109985</td>
      <td>977.000000</td>
      <td>977.000000</td>
      <td>891400</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2017/10/10</td>
      <td>980.000000</td>
      <td>981.570007</td>
      <td>966.080017</td>
      <td>972.599976</td>
      <td>972.599976</td>
      <td>968400</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2017/10/11</td>
      <td>973.719971</td>
      <td>990.710022</td>
      <td>972.250000</td>
      <td>989.250000</td>
      <td>989.250000</td>
      <td>1693300</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2017/10/12</td>
      <td>987.450012</td>
      <td>994.119995</td>
      <td>985.000000</td>
      <td>987.830017</td>
      <td>987.830017</td>
      <td>1262400</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2017/10/13</td>
      <td>992.000000</td>
      <td>997.210022</td>
      <td>989.000000</td>
      <td>989.679993</td>
      <td>989.679993</td>
      <td>1157700</td>
    </tr>
  </tbody>
</table>
</div>



>可以看出，这是一个非常庞大的数据集，Pandas 自动为该 DataFrame 分配了数字行索引。Pandas 还使用出现在 CSV 文件中的标签为列分配标签。
>
在处理这样的大型数据集时，通常有必要直接查看前几行数据，而不是整个数据集。我们可以使用 `.head()` 方法查看前 5 行数据，如下所示


```python
Google_stock.head()
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
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2004/8/19</td>
      <td>49.676899</td>
      <td>51.693783</td>
      <td>47.669952</td>
      <td>49.845802</td>
      <td>49.845802</td>
      <td>44994500</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2004/8/20</td>
      <td>50.178635</td>
      <td>54.187561</td>
      <td>49.925285</td>
      <td>53.805050</td>
      <td>53.805050</td>
      <td>23005800</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2004/8/23</td>
      <td>55.017166</td>
      <td>56.373344</td>
      <td>54.172661</td>
      <td>54.346527</td>
      <td>54.346527</td>
      <td>18393200</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2004/8/24</td>
      <td>55.260582</td>
      <td>55.439419</td>
      <td>51.450363</td>
      <td>52.096165</td>
      <td>52.096165</td>
      <td>15361800</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2004/8/25</td>
      <td>52.140873</td>
      <td>53.651051</td>
      <td>51.604362</td>
      <td>52.657513</td>
      <td>52.657513</td>
      <td>9257400</td>
    </tr>
  </tbody>
</table>
</div>




```python
Google_stock.head(3)
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
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2004/8/19</td>
      <td>49.676899</td>
      <td>51.693783</td>
      <td>47.669952</td>
      <td>49.845802</td>
      <td>49.845802</td>
      <td>44994500</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2004/8/20</td>
      <td>50.178635</td>
      <td>54.187561</td>
      <td>49.925285</td>
      <td>53.805050</td>
      <td>53.805050</td>
      <td>23005800</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2004/8/23</td>
      <td>55.017166</td>
      <td>56.373344</td>
      <td>54.172661</td>
      <td>54.346527</td>
      <td>54.346527</td>
      <td>18393200</td>
    </tr>
  </tbody>
</table>
</div>



>我们还可以使用 `.tail()` 方法查看最后 5 行数据：


```python
Google_stock.tail()
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
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>5</th>
      <td>2017/10/9</td>
      <td>980.000000</td>
      <td>985.424988</td>
      <td>976.109985</td>
      <td>977.000000</td>
      <td>977.000000</td>
      <td>891400</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2017/10/10</td>
      <td>980.000000</td>
      <td>981.570007</td>
      <td>966.080017</td>
      <td>972.599976</td>
      <td>972.599976</td>
      <td>968400</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2017/10/11</td>
      <td>973.719971</td>
      <td>990.710022</td>
      <td>972.250000</td>
      <td>989.250000</td>
      <td>989.250000</td>
      <td>1693300</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2017/10/12</td>
      <td>987.450012</td>
      <td>994.119995</td>
      <td>985.000000</td>
      <td>987.830017</td>
      <td>987.830017</td>
      <td>1262400</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2017/10/13</td>
      <td>992.000000</td>
      <td>997.210022</td>
      <td>989.000000</td>
      <td>989.679993</td>
      <td>989.679993</td>
      <td>1157700</td>
    </tr>
  </tbody>
</table>
</div>




```python
Google_stock.tail(2)
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
      <th>Date</th>
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>2017/10/12</td>
      <td>987.450012</td>
      <td>994.119995</td>
      <td>985.0</td>
      <td>987.830017</td>
      <td>987.830017</td>
      <td>1262400</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2017/10/13</td>
      <td>992.000000</td>
      <td>997.210022</td>
      <td>989.0</td>
      <td>989.679993</td>
      <td>989.679993</td>
      <td>1157700</td>
    </tr>
  </tbody>
</table>
</div>



>我们还可以选择使用 `.head(N)` 或 `.tail(N)` 分别显示前 `N` 行和后 `N` 行数据。
>
>我们快速检查下数据集中是否有任何 NaN 值。为此，我们将使用 `.isnull()` 方法，然后是 `.any()` 方法，检查是否有任何列包含 `NaN` 值。


```python
Google_stock.isnull().any()
```




    Date         False
    Open         False
    High         False
    Low          False
    Close        False
    Adj Close    False
    Volume       False
    dtype: bool



>可以看出没有任何 `NaN` 值。
### .describe() 方法获取描述性统计信息
>在处理大型数据集时，通常有必要获取关于数据集的统计信息。通过使用 Pandas 的 `.describe()` 方法，可以获取关于 DataFrame 每列的描述性统计信息。我们来看看代码编写方式：


```python
Google_stock.describe()
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
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>10.000000</td>
      <td>10.000000</td>
      <td>10.000000</td>
      <td>10.000000</td>
      <td>10.000000</td>
      <td>1.000000e+01</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>517.544414</td>
      <td>522.038019</td>
      <td>514.326262</td>
      <td>517.911104</td>
      <td>517.911104</td>
      <td>1.169859e+07</td>
    </tr>
    <tr>
      <th>std</th>
      <td>490.273720</td>
      <td>493.091421</td>
      <td>488.468367</td>
      <td>490.563253</td>
      <td>490.563253</td>
      <td>1.434778e+07</td>
    </tr>
    <tr>
      <th>min</th>
      <td>49.676899</td>
      <td>51.693783</td>
      <td>47.669952</td>
      <td>49.845802</td>
      <td>49.845802</td>
      <td>8.914000e+05</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>52.859946</td>
      <td>54.500525</td>
      <td>51.488863</td>
      <td>52.944397</td>
      <td>52.944397</td>
      <td>1.183875e+06</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>514.490277</td>
      <td>518.971676</td>
      <td>510.126339</td>
      <td>513.473251</td>
      <td>513.473251</td>
      <td>5.475350e+06</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>980.000000</td>
      <td>989.388764</td>
      <td>975.144989</td>
      <td>985.122513</td>
      <td>985.122513</td>
      <td>1.763535e+07</td>
    </tr>
    <tr>
      <th>max</th>
      <td>992.000000</td>
      <td>997.210022</td>
      <td>989.000000</td>
      <td>989.679993</td>
      <td>989.679993</td>
      <td>4.499450e+07</td>
    </tr>
  </tbody>
</table>
</div>



>如果有必要，我们可以对单列应用 `.describe()` 方法，如下所示：


```python
Google_stock['Adj Close'].describe()
```




    count     10.000000
    mean     517.911104
    std      490.563253
    min       49.845802
    25%       52.944397
    50%      513.473251
    75%      985.122513
    max      989.679993
    Name: Adj Close, dtype: float64



>同样，你可以使用 Pandas 提供的很多统计学函数查看某个统计信息。我们来看一些示例：


```python
# We print information about our DataFrame  
print()
print('Maximum values of each column:\n', Google_stock.max())
print()
print('Minimum Close value:', Google_stock['Close'].min())
print()
print('Average value of each column:\n', Google_stock.mean())
```


    Maximum values of each column:
     Date         2017/10/9
    Open               992
    High            997.21
    Low                989
    Close           989.68
    Adj Close       989.68
    Volume        44994500
    dtype: object
    
    Minimum Close value: 49.845802
    
    Average value of each column:
     Open         5.175444e+02
    High         5.220380e+02
    Low          5.143263e+02
    Close        5.179111e+02
    Adj Close    5.179111e+02
    Volume       1.169859e+07
    dtype: float64


### .corr() 方法获取不同列之间的关联性
>另一个重要统计学衡量指标是数据相关性。数据相关性可以告诉我们不同列的数据是否有关联。我们可以使用 `.corr()` 方法获取不同列之间的关联性，如下所示：


```python
# We display the correlation between columns
Google_stock.corr()
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
      <th>Open</th>
      <th>High</th>
      <th>Low</th>
      <th>Close</th>
      <th>Adj Close</th>
      <th>Volume</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Open</th>
      <td>1.000000</td>
      <td>0.999967</td>
      <td>0.999974</td>
      <td>0.999923</td>
      <td>0.999923</td>
      <td>-0.773126</td>
    </tr>
    <tr>
      <th>High</th>
      <td>0.999967</td>
      <td>1.000000</td>
      <td>0.999979</td>
      <td>0.999990</td>
      <td>0.999990</td>
      <td>-0.772657</td>
    </tr>
    <tr>
      <th>Low</th>
      <td>0.999974</td>
      <td>0.999979</td>
      <td>1.000000</td>
      <td>0.999955</td>
      <td>0.999955</td>
      <td>-0.773204</td>
    </tr>
    <tr>
      <th>Close</th>
      <td>0.999923</td>
      <td>0.999990</td>
      <td>0.999955</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>-0.772572</td>
    </tr>
    <tr>
      <th>Adj Close</th>
      <td>0.999923</td>
      <td>0.999990</td>
      <td>0.999955</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>-0.772572</td>
    </tr>
    <tr>
      <th>Volume</th>
      <td>-0.773126</td>
      <td>-0.772657</td>
      <td>-0.773204</td>
      <td>-0.772572</td>
      <td>-0.772572</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



>关联性值为 1 表明关联性很高，关联性值为 0 告诉我们数据根本不相关。
### .groupby() 方法以不同的方式对数据分组
>在这门“Pandas 入门”课程的最后，我们将讲解 `.groupby()` 方法。`.groupby()` 方法使我们能够以不同的方式对数据分组。我们来看看如何分组数据，以获得不同类型的信息。在下面的示例中，我们将加载关于虚拟公司的虚拟数据。


```python
# We load fake Company data in a DataFrame
data = pd.read_csv('./fake_company.csv')

data
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
      <th>Year</th>
      <th>Name</th>
      <th>Department</th>
      <th>Age</th>
      <th>Salary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1990</td>
      <td>Alice</td>
      <td>HR</td>
      <td>25</td>
      <td>50000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1990</td>
      <td>Bob</td>
      <td>RD</td>
      <td>30</td>
      <td>48000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1990</td>
      <td>Charlie</td>
      <td>Admin</td>
      <td>45</td>
      <td>55000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1991</td>
      <td>Alice</td>
      <td>HR</td>
      <td>26</td>
      <td>52000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1991</td>
      <td>Bob</td>
      <td>RD</td>
      <td>31</td>
      <td>50000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>1991</td>
      <td>Charlie</td>
      <td>Admin</td>
      <td>46</td>
      <td>60000</td>
    </tr>
    <tr>
      <th>6</th>
      <td>1992</td>
      <td>Alice</td>
      <td>Admin</td>
      <td>27</td>
      <td>60000</td>
    </tr>
    <tr>
      <th>7</th>
      <td>1992</td>
      <td>Bob</td>
      <td>RD</td>
      <td>32</td>
      <td>52000</td>
    </tr>
    <tr>
      <th>8</th>
      <td>1992</td>
      <td>Charlie</td>
      <td>Admin</td>
      <td>28</td>
      <td>62000</td>
    </tr>
  </tbody>
</table>
</div>



>可以看出，上述数据包含从 1990 年到 1992 年的信息。对于每一年，我们都能看到员工姓名、所在的部门、年龄和年薪。现在，我们使用 `.groupby()` 方法获取信息。
>
>我们来计算公司每年在员工薪资上花费的数额。为此，我们将使用 `.groupby()` 方法按年份对数据分组，然后使用 `.sum()` 方法将所有员工的薪资相加。


```python
# We display the total amount of money spent in salaries each year
data.groupby(['Year'])['Salary'].sum()
```




    Year
    1990    153000
    1991    162000
    1992    174000
    Name: Salary, dtype: int64



>可以看出，该公司在 1990 年的薪资花费总额为 153,000 美元，在 1991 年为 162,000 美元，在 1992 年为 174,000 美元。
>
>现在假设我们想知道每年的平均薪资是多少。为此，我们将使用 .groupby() 方法按年份对数据分组，就像之前一样，然后使用 `.mean()` 方法获取平均薪资。我们来看看代码编写方式


```python
# We display the average salary per year
data.groupby(['Year'])['Salary'].mean()
```




    Year
    1990    51000
    1991    54000
    1992    58000
    Name: Salary, dtype: int64



>可以看出，1990 年的平均薪资为 51,000 美元，1991 年为 54,000 美元，1992 年为 58,000 美元。
>
>现在我们来看看在这三年的时间内每位员工都收到多少薪资。在这种情况下，我们将使用`.groupby()`方法按照Name来对数据分组。之后，我们会把每年的薪资加起来。让我们来看看结果。


```python
# We display the salary distribution per department per year.
data.groupby(['Year', 'Department'])['Salary'].sum()
```




    Year  Department
    1990  Admin          55000
          HR             50000
          RD             48000
    1991  Admin          60000
          HR             52000
          RD             50000
    1992  Admin         122000
          RD             52000
    Name: Salary, dtype: int64



>我们看到，1990年，管理部门支付了55,000美元的薪资，HR部门支付了50,000，研发部门支付了48,000。1992年，管理部门支付了122,000美元的薪资，研发部门支付了52,000。
