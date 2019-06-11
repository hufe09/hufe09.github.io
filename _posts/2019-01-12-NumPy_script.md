---
layout: post
title: "NumPy使用"
subtitle: 'Python NumPy'
author: "Hufe"
header-img: "img/post-bg-python.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Python
  - NumPy
---

## 1. 创建和保存NumPy


```python
import numpy as np
x = np.array([1,2,3,4,5])
print("x=",x)
```

    x= [1 2 3 4 5]



```python
y1 = np.array([2,3,4,5,6])
print()
print('y1= ',y1)
print()

print('y1的尺寸为：',y1.shape,'y1的秩为：',len(y1.shape))
print('y1的类型为：',type(y1))
print('y1里面元素的类型为：',y1.dtype)
```


    y1=  [2 3 4 5 6]
    
    y1的尺寸为： (5,) y1的秩为： 1
    y1的类型为： <class 'numpy.ndarray'>
    y1里面元素的类型为： int32


### NumPy数据类型
>请务必注意，Python 列表和 ndarray 之间的最大区别是：与 Python 列表不同的是，ndarray 的所有元素都必须类型相同。因此，虽然我们可以同时使用整数和字符串创建 Python 列表，但是无法在 ndarray 中同时使用这两种类型。如果向 np.array() 函数提供同时具有整数和字符串的 Python 列表，NumPy 会将所有元素解析为字符串。我们可以在下面的示例中见到这种情况：

> **例如y2**


```python
y2 = np.array([['hello','world'],"Data_analysis",(1,2,3,4)])
print()
print('y2= \n',y2)
print()

print('y2的尺寸为：',y2.shape,'y2的秩为：',len(y2.shape))
print('y2的类型为：',type(y2))
print('y2里面元素的类型为：',y2.dtype)
```


    y2= 
     [list(['hello', 'world']) 'Data_analysis' (1, 2, 3, 4)]
    
    y2的尺寸为： (3,) y2的秩为： 1
    y2的类型为： <class 'numpy.ndarray'>
    y2里面元素的类型为： object



```python
y3 = np.array([['hello','world'],['1','2'],['a','b']])
print()
print('y3= \n',y3)
print()

print('y3的尺寸为：',y3.shape,'y2的秩为：',len(y3.shape))
print('y3的类型为：',type(y3))
print('y3里面元素的类型为：',y3.dtype)
```


    y3= 
     [['hello' 'world']
     ['1' '2']
     ['a' 'b']]
    
    y3的尺寸为： (3, 2) y2的秩为： 2
    y3的类型为： <class 'numpy.ndarray'>
    y3里面元素的类型为： <U5


### 创建一个秩为2的数组


```python
# We create a rank 2 ndarray that only contains integers
Y = np.array([[1,2,3],[4,5,6],[7,8,9], [10,11,12]])

# We print Y
print()
print('Y = \n', Y)
print()

# We print information about Y
print('Y has dimensions:', Y.shape, 'Y的秩为：', len(Y.shape))
print('Y has a total of', Y.size, 'elements')
print('Y is an object of type:', type(Y))
print('The elements in Y are of type:', Y.dtype)
```


    Y = 
     [[ 1  2  3]
     [ 4  5  6]
     [ 7  8  9]
     [10 11 12]]
    
    Y has dimensions: (4, 3) Y的秩为： 2
    Y has a total of 12 elements
    Y is an object of type: <class 'numpy.ndarray'>
    The elements in Y are of type: int32


> **可以看出，现在 shape 属性返回元组 (4,3)，告诉我们 Y 的秩为 2，有 4 行 3 列。.size 属性告诉我们 Y 共有 12 个元素。**


```python
x = np.array([1,2,3])
y = np.array([1.0,2.0,3.0])
z = np.array([1,2.5,3])

print('x的元素类型是：',x.dtype)
print('y的元素类型是：',y.dtype)
print('z的元素类型是：',z.dtype)
```

    x的元素类型是： int32
    y的元素类型是： float64
    z的元素类型是： float64



```python
x = np.array([1.5,2.0,5.6], dtype = np.int64)

print()
print('x= ', x)
print('the dtype of x', x.dtype)

np.save('my_array', x)
```


    x=  [1 2 5]
    the dtype of x int64



```python
# We load the saved array from our current directory into variable y
y = np.load('my_array.npy')

# We print y
print()
print('y = ', y)
print()

# We print information about the ndarray we loaded
print('y is an object of type:', type(y))
print('The elements in y are of type:', y.dtype)
```


    y =  [1 2 5]
    
    y is an object of type: <class 'numpy.ndarray'>
    The elements in y are of type: int64


## 2. 使用内置函数创建ndarray
### np.zeros((3,4)) 创建3行4列元素为0的数组


```python
# X = np.zeros((3,4))
X = np.zeros((3,4), dtype=int)
print()
print('X= \n', X)
print()

print(X.shape)
print(type(X))
print(X.dtype)
```


    X= 
     [[0 0 0 0]
     [0 0 0 0]
     [0 0 0 0]]
    
    (3, 4)
    <class 'numpy.ndarray'>
    int32


### np.ones((4,3)) 创建4行3列元素为1的数组


```python
X = np.ones((4,3))
print()
print('X= \n', X)
print()

print(X.shape)
print(type(X))
print(X.dtype)
```


    X= 
     [[1. 1. 1.]
     [1. 1. 1.]
     [1. 1. 1.]
     [1. 1. 1.]]
    
    (4, 3)
    <class 'numpy.ndarray'>
    float64


### np.full((4,3), 5) 创建4行3列元素为5的数组
>np.full(shape, constant value) 函数有两个参数。第一个参数是你要创建的 ndarray 的形状，第二个参数是你要向数组中填充的常数值。


```python
X = np.full((4,3), 5)
print()
print('X= \n', X)
print()

print(X.shape)
print(type(X))
print(X.dtype)
```


    X= 
     [[5 5 5]
     [5 5 5]
     [5 5 5]
     [5 5 5]]
    
    (4, 3)
    <class 'numpy.ndarray'>
    int32


### np.eye() 创建单位矩阵


```python
X = np.eye(4)
print()
print('X= \n', X)
print()

print(X.shape)
print(type(X))
print(X.dtype)
```


    X= 
     [[1. 0. 0. 0.]
     [0. 1. 0. 0.]
     [0. 0. 1. 0.]
     [0. 0. 0. 1.]]
    
    (4, 4)
    <class 'numpy.ndarray'>
    float64


### np.diag() 函数创建对角矩阵


```python
X = np.diag([10,20,30,40])
print()
print('X= \n', X)
print()

print(X.shape)
print(type(X))
print(X.dtype)
```


    X= 
     [[10  0  0  0]
     [ 0 20  0  0]
     [ 0  0 30  0]
     [ 0  0  0 40]]
    
    (4, 4)
    <class 'numpy.ndarray'>
    int32


### np.arange() 创建均匀分布的ndarry
>传入三个参数，np.arange(start,stop,step) 将创建一个秩为 1 的 ndarray，其中包含位于半开区间 [start, stop) 内并均匀分布的值，step 表示两个相邻值之间的差。


```python
X1 = np.arange(10)
print()
print('X1= \n', X1)
print()

print(X1.shape)
print(type(X1))
print(X1.dtype)

X2 = np.arange(2,9)
print()
print('X2= \n', X2)
print()

print(X2.shape)
print(type(X2))
print(X2.dtype)

X3 = np.arange(5,25,5)
print()
print('X3= \n', X3)
print()

print(X3.shape)
print(type(X3))
print(X3.dtype)
```


    X1= 
     [0 1 2 3 4 5 6 7 8 9]
    
    (10,)
    <class 'numpy.ndarray'>
    int32
    
    X2= 
     [2 3 4 5 6 7 8]
    
    (7,)
    <class 'numpy.ndarray'>
    int32
    
    X3= 
     [ 5 10 15 20]
    
    (4,)
    <class 'numpy.ndarray'>
    int32


### np.linspace() 非整数间隔
>np.linspace(start, stop, N) 函数返回 N 个在闭区间 [start, stop] 内均匀分布的数字。


```python
Y1 = np.linspace(1, 5, 10)
print()
print('Y1= \n', Y1)
print()

print(Y1.shape)
print(type(Y1))
print(Y1.dtype)

Y2 = np.linspace(0, 25, 10, endpoint=False)
print()
print('Y2= \n', Y2)
print()

print(Y1.shape)
print(type(Y1))
print(Y1.dtype)
```


    Y1= 
     [1.         1.44444444 1.88888889 2.33333333 2.77777778 3.22222222
     3.66666667 4.11111111 4.55555556 5.        ]
    
    (10,)
    <class 'numpy.ndarray'>
    float64
    
    Y2= 
     [ 0.   2.5  5.   7.5 10.  12.5 15.  17.5 20.  22.5]
    
    (10,)
    <class 'numpy.ndarray'>
    float64


### np.reshape(ndarray, new_shape) 
>到目前为止，我们仅使用了内置函数 np.arange() 和 np.linspace() 来创建秩为 1 的 ndarray。但是，我们可以将这些函数与 np.reshape() 函数相结合，创建秩为 2 的任何形状 ndarray。np.reshape(ndarray, new_shape) 函数会将给定 ndarray 转换为指定的 new_shape。请务必注意：new_shape 应该与给定 ndarray 中的元素数量保持一致。例如，你可以将秩为 1 的 6 元素 ndarray 转换为秩为 2 的 3 x 2 ndarray，或秩为 2 的 2 x 3 ndarray，因为这两个秩为 2 的数组元素总数都是 6 个。但是，你无法将秩为 1 的 6 元素 ndarray 转换为秩为 2 的 3 x 3 ndarray，因为这个秩为 2 的数组将包含 9 个元素，比原始 ndarray 中的元素数量多。


```python
# We create a rank 1 ndarray with sequential integers from 0 to 19
x = np.arange(20)

# We print x
print()
print('Original x = ', x)
print()

# We reshape x into a 4 x 5 ndarray 
x = np.reshape(x, (4,5))

# We print the reshaped x
print()
print('Reshaped x = \n', x)
print()
# We print information about the reshaped x
print('x has dimensions:', x.shape)
print('x is an object of type:', type(x))
print('The elements in x are of type:', x.dtype) 
```


    Original x =  [ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19]


​    
​    Reshaped x = 
​     [[ 0  1  2  3  4]
​     [ 5  6  7  8  9]
​     [10 11 12 13 14]
​     [15 16 17 18 19]]
​    
​    x has dimensions: (4, 5)
​    x is an object of type: <class 'numpy.ndarray'>
​    The elements in x are of type: int32


>NumPy 的一大特性是某些函数还可以当做方法使用。这样我们便能够在一行代码中按顺序应用不同的函数。ndarray 方法和 ndarray 属性相似，它们都使用点记法 (.)。我们来看看如何只用一行代码实现上述示例中的相同结果：


```python
# We create a a rank 1 ndarray with sequential integers from 0 to 19 and
# reshape it to a 4 x 5 array 
Y = np.arange(20).reshape(4, 5)

# We print Y
print()
print('Y = \n', Y)
print()

# We print information about Y
print('Y has dimensions:', Y.shape)
print('Y is an object of type:', type(Y))
print('The elements in Y are of type:', Y.dtype) 
```


    Y = 
     [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]
     [15 16 17 18 19]]
    
    Y has dimensions: (4, 5)
    Y is an object of type: <class 'numpy.ndarray'>
    The elements in Y are of type: int32


### reshape() 与 np.linspace() 
>同样，我们也可以使用 reshape() 与 np.linspace() 创建秩为 2 的数组，如以下示例所示。


```python
# We create a rank 1 ndarray with 10 integers evenly spaced between 0 and 50,
# with 50 excluded. We then reshape it to a 5 x 2 ndarray
X = np.linspace(0,50,10, endpoint=False).reshape(5,2)

# We print X
print()
print('X = \n', X)
print()

# We print information about X
print('X has dimensions:', X.shape)
print('X is an object of type:', type(X))
print('The elements in X are of type:', X.dtype)
```


    X = 
     [[ 0.  5.]
     [10. 15.]
     [20. 25.]
     [30. 35.]
     [40. 45.]]
    
    X has dimensions: (5, 2)
    X is an object of type: <class 'numpy.ndarray'>
    The elements in X are of type: float64


### np.random 创建随机ndarray
#### np.random.random(shape) 函数创建具有给定形状的 ndarray


```python
# We create a 3 x 3 ndarray with random floats in the half-open interval [0.0, 1.0).
X = np.random.random((3,3))

# We print X
print()
print('X = \n', X)
print()

# We print information about X
print('X has dimensions:', X.shape)
print('X is an object of type:', type(X))
print('The elements in x are of type:', X.dtype)
```


    X = 
     [[0.21230998 0.28900339 0.08705847]
     [0.48096698 0.95425654 0.15348678]
     [0.83497175 0.92573567 0.02216025]]
    
    X has dimensions: (3, 3)
    X is an object of type: <class 'numpy.ndarray'>
    The elements in x are of type: float64


#### np.random.randint(start, stop, size = shape) 
会创建一个具有给定形状的 ndarray，其中包含在半开区间 [start, stop) 内的随机整数。


```python
# We create a 3 x 2 ndarray with random integers in the half-open interval [4, 15).
X = np.random.randint(4,15,size=(3,2))

# We print X
print()
print('X = \n', X)
print()

# We print information about X
print('X has dimensions:', X.shape)
print('X is an object of type:', type(X))
print('The elements in X are of type:', X.dtype)
```


    X = 
     [[13  8]
     [ 8 10]
     [11  8]]
    
    X has dimensions: (3, 2)
    X is an object of type: <class 'numpy.ndarray'>
    The elements in X are of type: int32


>在某些情况下，你可能需要创建由满足特定统计学特性的随机数字组成的 ndarray。例如，你可能希望 ndarray 中的随机数字平均值为 0。NumPy 使你能够创建从各种概率分布中抽样的数字组成的随机 ndarray。例如，函数`np.random.normal(mean, standard deviation, size=shape) `会创建一个具有给定`形状`的 ndarray，其中包含从`正态`高斯分布（具有给定`均值`和`标准差`）中抽样的随机数字。我们来创建一个 1,000 x 1,000 ndarray，其中包含从正态分布（均值为 0，标准差为 0.1）中随机抽样的浮点数。


```python
# We create a 1000 x 1000 ndarray of random floats drawn from normal (Gaussian) distribution
# with a mean of zero and a standard deviation of 0.1.
X = np.random.normal(0, 0.1, size=(1000,1000))

# We print X
print()
print('X = \n', X)
print()

# We print information about X
print('X has dimensions:', X.shape)
print('X is an object of type:', type(X))
print('The elements in X are of type:', X.dtype)
print('The elements in X have a mean of:', X.mean())
print('The maximum value in X is:', X.max())
print('The minimum value in X is:', X.min())
print('X has', (X < 0).sum(), 'negative numbers')
print('X has', (X > 0).sum(), 'positive numbers')
```


    X = 
     [[-0.00360278  0.07708453  0.09719537 ...  0.177679   -0.04761913
       0.16346406]
     [-0.02823084 -0.12225687 -0.11911609 ...  0.05164287 -0.08137814
      -0.06063261]
     [-0.13484967 -0.03077555  0.02996952 ... -0.0775261  -0.02802056
      -0.08150592]
     ...
     [-0.09890516  0.11955715 -0.0937293  ...  0.02439718  0.03279139
      -0.06356691]
     [-0.12959071  0.12534209  0.04072241 ... -0.2371915   0.1287925
       0.04140548]
     [-0.06930125 -0.04762866  0.05096849 ... -0.14165001  0.08026659
      -0.03596368]]
    
    X has dimensions: (1000, 1000)
    X is an object of type: <class 'numpy.ndarray'>
    The elements in X are of type: float64
    The elements in X have a mean of: 3.598110850467985e-05
    The maximum value in X is: 0.4779593605055418
    The minimum value in X is: -0.4915900424001299
    X has 499546 negative numbers
    X has 500454 positive numbers


## 3. 访问和删除 ndarray 中的元素及向其中插入元素

### 访问元素

>我们首先将了解如何通过索引访问或修改 ndarray 中的元素。可以在方括号 [ ] 中添加索引来访问元素。在 NumPy 中，你可以使用正索引和负索引访问 ndarray 中的元素。正索引表示从数组的开头访问元素，负索引表示从数组的末尾访问元素。我们来看看如何访问秩为 1 的 ndarray 中的元素：


```python
# We create a rank 1 ndarray that contains integers from 1 to 5
x = np.array([1, 2, 3, 4, 5])

# We print x
print()
print('x = ', x)
print()

# Let's access some elements with positive indices
print('This is First Element in x:', x[0]) 
print('This is Second Element in x:', x[1])
print('This is Fifth (Last) Element in x:', x[4])
print()

# Let's access the same elements with negative indices
print('This is First Element in x:', x[-5])
print('This is Second Element in x:', x[-4])
print('This is Fifth (Last) Element in x:', x[-1])
```


    x =  [1 2 3 4 5]
    
    This is First Element in x: 1
    This is Second Element in x: 2
    This is Fifth (Last) Element in x: 5
    
    This is First Element in x: 1
    This is Second Element in x: 2
    This is Fifth (Last) Element in x: 5


>现在我们看看如何更改秩为 1 的 ndarray 中的元素。方法是访问要更改的元素，然后使用 = 符号分配新的值：


```python
# We create a rank 1 ndarray that contains integers from 1 to 5
x = np.array([1, 2, 3, 4, 5])

# We print the original x
print()
print('Original:\n x = ', x)
print()

# We change the fourth element in x from 4 to 20
x[3] = 20

# We print x after it was modified 
print('Modified:\n x = ', x)
```


    Original:
     x =  [1 2 3 4 5]
    
    Modified:
     x =  [ 1  2  3 20  5]


>同样，我们可以访问和修改秩为 2 的 ndarray 中的特定元素。要访问秩为 2 的 ndarray 中的元素，我们需要提供两个索引，格式为 [row, column]。我们来看一些示例：


```python
# We create a 3 x 3 rank 2 ndarray that contains integers from 1 to 9
X = np.array([[1,2,3],[4,5,6],[7,8,9]])

# We print X
print()
print('X = \n', X)
print()

# Let's access some elements in X
print('This is (0,0) Element in X:', X[0,0])
print('This is (0,1) Element in X:', X[0,1])
print('This is (2,2) Element in X:', X[2,2])
```


    X = 
     [[1 2 3]
     [4 5 6]
     [7 8 9]]
    
    This is (0,0) Element in X: 1
    This is (0,1) Element in X: 2
    This is (2,2) Element in X: 9


>注意，索引 [0, 0] 是指第一行第一列的元素。

>可以像针对秩为 1 的 ndarray 一样修改秩为 2 的 ndarray 中的元素。我们来看一个示例：


```python
# We create a 3 x 3 rank 2 ndarray that contains integers from 1 to 9
X = np.array([[1,2,3],[4,5,6],[7,8,9]])

# We print the original x
print()
print('Original:\n X = \n', X)
print()

# We change the (0,0) element in X from 1 to 20
X[0,0] = 20

# We print X after it was modified 
print('Modified:\n X = \n', X)
```


    Original:
     X = 
     [[1 2 3]
     [4 5 6]
     [7 8 9]]
    
    Modified:
     X = 
     [[20  2  3]
     [ 4  5  6]
     [ 7  8  9]]


### np.delete(ndarray, elements, axis) 函数删除元素


```python
# We create a rank 1 ndarray 
x = np.array([1, 2, 3, 4, 5])

# We create a rank 2 ndarray
Y = np.array([[1,2,3],[4,5,6],[7,8,9]])

# We print x
print()
print('Original x = ', x)

# We delete the first and last element of x
x = np.delete(x, [0,4])

# We print x with the first and last element deleted
print()
print('Modified x = ', x)

# We print Y
print()
print('Original Y = \n', Y)

# We delete the first row of y
w = np.delete(Y, 0, axis=0)

# We delete the first and last column of y
v = np.delete(Y, [0,2], axis=1)

# We print w
print()
print('w = \n', w)

# We print v
print()
print('v = \n', v)
```


    Original x =  [1 2 3 4 5]
    
    Modified x =  [2 3 4]
    
    Original Y = 
     [[1 2 3]
     [4 5 6]
     [7 8 9]]
    
    w = 
     [[4 5 6]
     [7 8 9]]
    
    v = 
     [[2]
     [5]
     [8]]


### np.append(ndarray, elements, axis) 函数向 ndarray 中附加值
>现在我们来看看如何向 ndarray 中附加值。我们可以使用 np.append(ndarray, elements, axis) 函数向 ndarray 中附加值。该函数会将给定的元素列表沿着指定的轴附加到 ndarray 中。我们来看一些示例：


```python
# We create a rank 1 ndarray 
x = np.array([1, 2, 3, 4, 5])

# We create a rank 2 ndarray 
Y = np.array([[1,2,3],[4,5,6]])

# We print x
print()
print('Original x = ', x)

# We append the integer 6 to x
x = np.append(x, 6)

# We print x
print()
print('x = ', x)

# We append the integer 7 and 8 to x
x = np.append(x, [7,8])

# We print x
print()
print('x = ', x)

# We print Y
print()
print('Original Y = \n', Y)

# We append a new row containing 7,8,9 to y
v = np.append(Y, [[7,8,9]], axis=0)

# We append a new column containing 9 and 10 to y
q = np.append(Y,[[9],[10]], axis=1)

# We print v
print()
print('v = \n', v)

# We print q
print()
print('q = \n', q)
```


    Original x =  [1 2 3 4 5]
    
    x =  [1 2 3 4 5 6]
    
    x =  [1 2 3 4 5 6 7 8]
    
    Original Y = 
     [[1 2 3]
     [4 5 6]]
    
    v = 
     [[1 2 3]
     [4 5 6]
     [7 8 9]]
    
    q = 
     [[ 1  2  3  9]
     [ 4  5  6 10]]


### np.insert(ndarray, index, elements, axis) 函数向 ndarray 中插入值
>np.insert(ndarray, index, elements, axis) 函数向 ndarray 中插入值。此函数会将给定的元素列表沿着指定的轴插入到 ndarray 中，并放在给定的索引前面。我们来看一些示例：


```python
# We create a rank 1 ndarray 
x = np.array([1, 2, 5, 6, 7])

# We create a rank 2 ndarray 
Y = np.array([[1,2,3],[7,8,9]])

# We print x
print()
print('Original x = ', x)

# We insert the integer 3 and 4 between 2 and 5 in x. 
x = np.insert(x,2,[3,4])

# We print x with the inserted elements
print()
print('x = ', x)

# We print Y
print()
print('Original Y = \n', Y)

# We insert a row between the first and last row of y
w = np.insert(Y,1,[4,5,6],axis=0)

# We insert a column full of 5s between the first and second column of y
v = np.insert(Y,1,5, axis=1)

# We print w
print()
print('w = \n', w)

# We print v
print()
print('v = \n', v)
```


    Original x =  [1 2 5 6 7]
    
    x =  [1 2 3 4 5 6 7]
    
    Original Y = 
     [[1 2 3]
     [7 8 9]]
    
    w = 
     [[1 2 3]
     [4 5 6]
     [7 8 9]]
    
    v = 
     [[1 5 2 3]
     [7 5 8 9]]


### ndarray堆叠
#### np.hstack() 函数进行水平堆叠
#### np.vstack() 函数进行垂直堆叠
>NumPy 还允许我们将 ndarray 上下堆叠起来，或者左右堆叠。可以使用 **np.vstack() 函数进行垂直堆叠**，或使用 **np.hstack() 函数进行水平堆叠**。请务必注意，为了堆叠 ndarray，ndarray 的形状必须相符。我们来看一些示例：


```python
# We create a rank 1 ndarray 
x = np.array([1,2])

# We create a rank 2 ndarray 
Y = np.array([[3,4],[5,6]])

# We print x
print()
print('x = ', x)

# We print Y
print()
print('Y = \n', Y)

# We stack x on top of Y
z = np.vstack((x,Y))

# We stack x on the right of Y. We need to reshape x in order to stack it on the right of Y. 
w = np.hstack((Y,x.reshape(2,1)))

# We print z
print()
print('z = \n', z)

# We print w
print()
print('w = \n', w)
```


    x =  [1 2]
    
    Y = 
     [[3 4]
     [5 6]]
    
    z = 
     [[1 2]
     [3 4]
     [5 6]]
    
    w = 
     [[3 4 1]
     [5 6 2]]


## 4. ndarray切片

>正如之前提到的，我们除了能够一次访问一个元素之外，NumPy 还提供了访问 ndarray 子集的方式，称之为切片。切片方式是在方括号里用冒号 : 分隔起始和结束索引。通常，你将遇到三种类型的切片：
```
1. ndarray[start:end]
2. ndarray[start:]
3. ndarray[:end]
```
>现在我们将查看一些示例，了解如何使用上述方法从秩为 2 的 ndarray 中选择不同的子集。


```python
# We create a 4 x 5 ndarray that contains integers from 0 to 19
X = np.arange(20).reshape(4, 5)

# We print X
print()
print('X = \n', X)
print()

# We select all the elements that are in the 2nd through 4th rows and in the 3rd to 5th columns
Z = X[1:4,2:5]

# We print Z
print('Z = \n', Z)

# We can select the same elements as above using method 2
W = X[1:,2:5]

# We print W
print()
print('W = \n', W)

# We select all the elements that are in the 1st through 3rd rows and in the 3rd to 5th columns
Y = X[:3,2:5]

# We print Y
print()
print('Y = \n', Y)

# We select all the elements in the 3rd row
v = X[2,:]

# We print v
print()
print('v = ', v)

# We select all the elements in the 3rd column
q = X[:,2]

# We print q
print()
print('q = ', q)

# We select all the elements in the 3rd column but return a rank 2 ndarray
R = X[:,2:3]

# We print R
print()
print('R = \n', R)
```


    X = 
     [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]
     [15 16 17 18 19]]
    
    Z = 
     [[ 7  8  9]
     [12 13 14]
     [17 18 19]]
    
    W = 
     [[ 7  8  9]
     [12 13 14]
     [17 18 19]]
    
    Y = 
     [[ 2  3  4]
     [ 7  8  9]
     [12 13 14]]
    
    v =  [10 11 12 13 14]
    
    q =  [ 2  7 12 17]
    
    R = 
     [[ 2]
     [ 7]
     [12]
     [17]]


>注意，当我们选择第 3 列中的所有元素，即上述变量 q，切片返回一个秩为 1 的 ndarray，而不是秩为 2 的 ndarray。但是，如果以稍微不同的方式切片X，即上述变量 R，实际上可以获得秩为 2 的 ndarray。

>请务必注意，如果对 ndarray 进行切片并将结果保存到新的变量中，就像之前一样，数据不会复制到新的变量中。初学者对于这一点经常比较困惑。因此，我们将深入讲解这方面的知识。
在上述示例中，当我们进行赋值时，例如：

>```Z = X[1:4,2:5]```

>原始数组 X 的切片没有复制到变量 Z 中。X 和 Z 现在只是同一个 ndarray 的两个不同名称。我们提到，切片只是创建了原始数组的一个视图。也就是说，如果对 Z 做出更改，也会更改 X 中的元素。我们来看一个示例：


```python
# We create a 4 x 5 ndarray that contains integers from 0 to 19
X = np.arange(20).reshape(4, 5)

# We print X
print()
print('X = \n', X)
print()

# We select all the elements that are in the 2nd through 4th rows and in the 3rd to 4th columns
Z = X[1:4,2:5]

# We print Z
print()
print('Z = \n', Z)
print()

# We change the last element in Z to 555
Z[2,2] = 555

# We print X
print()
print('X = \n', X)
print()
```


    X = 
     [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]
     [15 16 17 18 19]]


​    
​    Z = 
​     [[ 7  8  9]
​     [12 13 14]
​     [17 18 19]]


​    
​    X = 
​     [[  0   1   2   3   4]
​     [  5   6   7   8   9]
​     [ 10  11  12  13  14]
​     [ 15  16  17  18 555]]


​    

### np.copy() 函数
>可以从上述示例中清晰地看出，如果对 Z 做出更改，X 也会更改。

>但是，如果我们想创建一个新的 ndarray，其中包含切片中的值的副本，需要使用 `np.copy()` 函数。`np.copy(ndarray) `函数会创建给定 ndarray 的一个副本。此函数还可以当做方法使用，就像之前使用 `reshape` 函数一样。我们来看看之前的相同示例，但是现在创建数组副本。我们将 `copy` 同时当做函数和方法。


```python
# We create a 4 x 5 ndarray that contains integers from 0 to 19
X = np.arange(20).reshape(4, 5)

# We print X
print()
print('X = \n', X)
print()

# create a copy of the slice using the np.copy() function
Z = np.copy(X[1:4,2:5])

#  create a copy of the slice using the copy as a method
W = X[1:4,2:5].copy()

# We change the last element in Z to 555
Z[2,2] = 555

# We change the last element in W to 444
W[2,2] = 444

# We print X
print()
print('X = \n', X)

# We print Z
print()
print('Z = \n', Z)

# We print W
print()
print('W = \n', W)
```


    X = 
     [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]
     [15 16 17 18 19]]


​    
​    X = 
​     [[ 0  1  2  3  4]
​     [ 5  6  7  8  9]
​     [10 11 12 13 14]
​     [15 16 17 18 19]]
​    
​    Z = 
​     [[  7   8   9]
​     [ 12  13  14]
​     [ 17  18 555]]
​    
​    W = 
​     [[  7   8   9]
​     [ 12  13  14]
​     [ 17  18 444]]


>可以清晰地看出，通过使用 copy 命令，我们创建了完全相互独立的新 ndarray。

>通常，我们会使用一个 ndarray 对另一个 ndarray 进行切片、选择或更改另一个 ndarray 的元素。我们来看一些示例：


```python
# We create a 4 x 5 ndarray that contains integers from 0 to 19
X = np.arange(20).reshape(4, 5)

# We create a rank 1 ndarray that will serve as indices to select elements from X
indices = np.array([1,3])

# We print X
print()
print('X = \n', X)
print()

# We print indices
print('indices = ', indices)
print()

# We use the indices ndarray to select the 2nd and 4th row of X
Y = X[indices,:]

# We use the indices ndarray to select the 2nd and 4th column of X
Z = X[:, indices]

# We print Y
print()
print('Y = \n', Y)

# We print Z
print()
print('Z = \n', Z)
```


    X = 
     [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]
     [15 16 17 18 19]]
    
    indices =  [1 3]


​    
​    Y = 
​     [[ 5  6  7  8  9]
​     [15 16 17 18 19]]
​    
​    Z = 
​     [[ 1  3]
​     [ 6  8]
​     [11 13]
​     [16 18]]


### np.diag(ndarray, k=N) 函数会以 N 定义的对角线提取元素
>NumPy 还提供了从 ndarray 中选择特定元素的内置函数。例如，`np.diag(ndarray, k=N) `函数会以 N 定义的对角线提取元素。默认情况下，`k=0`，表示主对角线。`k > 0` 的值用于选择在主对角线之上的对角线中的元素，`k < 0` 的值用于选择在主对角线之下的对角线中的元素。我们来看一个示例：


```python
# We create a 4 x 5 ndarray that contains integers from 0 to 19
X = np.arange(25).reshape(5, 5)

# We print X
print()
print('X = \n', X)
print()

# We print the elements in the main diagonal of X
print('z =', np.diag(X))
print()

# We print the elements above the main diagonal of X
print('y =', np.diag(X, k=1))
print()

# We print the elements below the main diagonal of X
print('w = ', np.diag(X, k=-1))
```


    X = 
     [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]
     [15 16 17 18 19]
     [20 21 22 23 24]]
    
    z = [ 0  6 12 18 24]
    
    y = [ 1  7 13 19]
    
    w =  [ 5 11 17 23]


### np.unique() 函数查找 ndarray 中的唯一元素
>通常我们都会从 ndarray 中提取唯一的元素。我们可以使用 `np.unique() `函数查找 ndarray 中的唯一元素。`np.unique(ndarray) `函数会返回给定 ndarray 中的 唯一元素，如以下示例所示：


```python
# Create 3 x 3 ndarray with repeated values
X = np.array([[1,2,3],[5,2,8],[1,2,3]])

# We print X
print()
print('X = \n', X)
print()

# We print the unique elements of X 
print('The unique elements in X are:',np.unique(X))
```


    X = 
     [[1 2 3]
     [5 2 8]
     [1 2 3]]
    
    The unique elements in X are: [1 2 3 5 8]


## 5. 布尔索引、集合运算和排序
### 布尔索引
>这时候就要用到布尔型索引，对于布尔型索引，我们将使用逻辑参数（而不是确切的索引）选择元素。我们来看一些示例：


```python
# We create a 5 x 5 ndarray that contains integers from 0 to 24
X = np.arange(25).reshape(5, 5)

# We print X
print()
print('Original X = \n', X)
print()

# We use Boolean indexing to select elements in X:
print('The elements in X that are greater than 10:', X[X > 10])
print('The elements in X that lees than or equal to 7:', X[X <= 7])
print('The elements in X that are between 10 and 17:', X[(X > 10) & (X < 17)])

# We use Boolean indexing to assign the elements that are between 10 and 17 the value of -1
X[(X > 10) & (X < 17)] = -1

# We print X
print()
print('X = \n', X)
print()
```


    Original X = 
     [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 11 12 13 14]
     [15 16 17 18 19]
     [20 21 22 23 24]]
    
    The elements in X that are greater than 10: [11 12 13 14 15 16 17 18 19 20 21 22 23 24]
    The elements in X that lees than or equal to 7: [0 1 2 3 4 5 6 7]
    The elements in X that are between 10 and 17: [11 12 13 14 15 16]
    
    X = 
     [[ 0  1  2  3  4]
     [ 5  6  7  8  9]
     [10 -1 -1 -1 -1]
     [-1 -1 17 18 19]
     [20 21 22 23 24]]


​    

### 集合运算
>除了布尔型索引之外，NumPy 还允许进行集合运算。可以用来比较 ndarray，例如查找两个 ndarray 中的相同元素。我们来看一些示例：

- `np.intersect1d(x,y)` ,交集，返回x和y中的公共元素，并返回有序结构
- `np.setdiff1d(x,y)`  差集，集合的差，即在x中且不在y中
- `np.union1d(x,y)`  并集，计算x,y的并集（无重复），并返回有序据结果过
- `np.setxor1d(x,y)`  异或集，返回仅在一个（而不是两个）输入数组中的已排序的唯一值。


```python
# We create a rank 1 ndarray
x = np.array([1,2,3,4,5])

# We create a rank 1 ndarray
y = np.array([6,7,2,8,4])

# We print x
print()
print('x = ', x)

# We print y
print()
print('y = ', y)

# We use set operations to compare x and y:
print()
print('The elements that are both in x and y:', np.intersect1d(x,y))
print('The elements that are in x that are not in y:', np.setdiff1d(x,y))
print('All the elements of x and y:',np.union1d(x,y))
print('The elements that are in x or y, but not both:',np.setxor1d(x,y))
```


    x =  [1 2 3 4 5]
    
    y =  [6 7 2 8 4]
    
    The elements that are both in x and y: [2 4]
    The elements that are in x that are not in y: [1 3 5]
    All the elements of x and y: [1 2 3 4 5 6 7 8]
    The elements that are in x or y, but not both: [1 3 5 6 7 8]


### np.sort() 函数以不同的方式对秩为 1 和 2 的 ndarray 进行排序
>我们还可以在 NumPy 中对 ndarray 进行排序。我们将了解如何使用 `np.sort()` 函数以不同的方式对秩为 1 和 2 的 ndarray 进行排序。和我们之前看到的其他函数一样，`sort` 函数也可以当做方法使用。但是，对于此函数来说，数据在内存中的存储方式有很大变化。当 `np.sort() `当做函数使用时，它不会对ndarray进行就地排序，即不更改被排序的原始 ndarray。但是，如果将 `sort`当做方法，`ndarray.sort() `会就地排序 ndarray，即原始数组会变成排序后的数组。我们来看一些示例：




```python
# We create an unsorted rank 1 ndarray
x = np.random.randint(1,11,size=(10,))

# We print x
print()
print('Original x = ', x)

# We sort x and print the sorted array using sort as a function.
print()
print('Sorted x (out of place):', np.sort(x))

# When we sort out of place the original array remains intact. To see this we print x again
print()
print('x after sorting:', x)
```


    Original x =  [ 8  7  9  4  2  4  1 10  1  9]
    
    Sorted x (out of place): [ 1  1  2  4  4  7  8  9  9 10]
    
    x after sorting: [ 8  7  9  4  2  4  1 10  1  9]


>注意，`np.sort()` 会对数组进行排序，但是如果被排序的 ndarray 具有重复的值，`np.sort()` 将在排好序的数组中保留这些值。但是，我们可以根据需要，同时使用 `sort` 函数和 `unique` 函数仅对 x 中的唯一元素进行排序。我们来看看如何对上述 x 中的唯一元素进行排序：


```python
# We sort x but only keep the unique elements in x
print(np.sort(np.unique(x)))
```

    [ 1  2  4  7  8  9 10]


>最后，我们来看看如何将 sort 当做方法，原地对 ndarray 进行排序：


```python
# We create an unsorted rank 1 ndarray
x = np.random.randint(1,11,size=(10,))

# We print x
print()
print('Original x = ', x)

# We sort x and print the sorted array using sort as a method.
x.sort()

# When we sort in place the original array is changed to the sorted array. To see this we print x again
print()
print('x after sorting:', x)
```


    Original x =  [ 5  3  6  4 10  5  9  5  2  6]
    
    x after sorting: [ 2  3  4  5  5  5  6  6  9 10]


>在对秩为 2 的 ndarray 进行排序时，我们需要在 np.sort() 函数中指定是按行排序，还是按列排序。为此，我们可以使用关键字 axis。我们来看一些示例：


```python
# We create an unsorted rank 2 ndarray
X = np.random.randint(1,11,size=(5,5))

# We print X
print()
print('Original X = \n', X)
print()

# We sort the columns of X and print the sorted array
print()
print('X with sorted columns :\n', np.sort(X, axis = 0))

# We sort the rows of X and print the sorted array
print()
print('X with sorted rows :\n', np.sort(X, axis = 1))
```


    Original X = 
     [[ 2  6  2  2 10]
     [ 1  7  7  3  4]
     [ 6  2 10  5  8]
     [ 3  5  5  6  2]
     [ 9 10  7  7  2]]


​    
​    X with sorted columns :
​     [[ 1  2  2  2  2]
​     [ 2  5  5  3  2]
​     [ 3  6  7  5  4]
​     [ 6  7  7  6  8]
​     [ 9 10 10  7 10]]
​    
​    X with sorted rows :
​     [[ 2  2  2  6 10]
​     [ 1  3  4  7  7]
​     [ 2  5  6  8 10]
​     [ 2  3  5  5  6]
​     [ 2  7  7  9 10]]


## 6. 算术运算和广播
### 运算 add(), subtract(), multiply(), divide()

>NumPy 允许对 ndarray 执行元素级运算以及矩阵运算。在这节课，我们将仅了解如何对 ndarray 进行元素级运算。为了进行元素级运算，NumPy 有时候会用到广播功能。广播一词用于描述 NumPy 如何对具有不同形状的 ndarray 进行元素级算术运算。例如，在标量和 ndarray 之间进行算术运算时，会隐式地用到广播。

>我们先在 ndarray 之间进行元素级加减乘除运算。为此，我们可以在 NumPy 中使用 np.add() 等函数，或者使用 + 等算术符号，后者与数学方程式的写法更像。这两种形式都执行相同的运算，唯一的区别是如果采用函数方式，函数通常都具有各种选项，可以通过关键字调整这些选项。请注意，在进行元素级运算时，对其执行运算的 ndarray 必须具有相同的形状或者可以广播。我们将在这节课的稍后阶段详细讲解这方面的知识。我们先对秩为 1 的 ndarray 执行元素级算术运算：


```python
# We create two rank 1 ndarrays
x = np.array([1,2,3,4])
y = np.array([5.5,6.5,7.5,8.5])

# We print x
print()
print('x = ', x)

# We print y
print()
print('y = ', y)
print()

# We perfrom basic element-wise operations using arithmetic symbols and functions
print('x + y = ', x + y)
print('add(x,y) = ', np.add(x,y))
print()
print('x - y = ', x - y)
print('subtract(x,y) = ', np.subtract(x,y))
print()
print('x * y = ', x * y)
print('multiply(x,y) = ', np.multiply(x,y))
print()
print('x / y = ', x / y)
print('divide(x,y) = ', np.divide(x,y))
```


    x =  [1 2 3 4]
    
    y =  [5.5 6.5 7.5 8.5]
    
    x + y =  [ 6.5  8.5 10.5 12.5]
    add(x,y) =  [ 6.5  8.5 10.5 12.5]
    
    x - y =  [-4.5 -4.5 -4.5 -4.5]
    subtract(x,y) =  [-4.5 -4.5 -4.5 -4.5]
    
    x * y =  [ 5.5 13.  22.5 34. ]
    multiply(x,y) =  [ 5.5 13.  22.5 34. ]
    
    x / y =  [0.18181818 0.30769231 0.4        0.47058824]
    divide(x,y) =  [0.18181818 0.30769231 0.4        0.47058824]


>我们还可以对秩为 2 的 ndarray 执行相同的元素级算术运算。同样，为了执行这些运算，ndarray 的形状必须一样或者可广播。


```python
# We create two rank 2 ndarrays
X = np.array([1,2,3,4]).reshape(2,2)
Y = np.array([5.5,6.5,7.5,8.5]).reshape(2,2)

# We print X
print()
print('X = \n', X)

# We print Y
print()
print('Y = \n', Y)
print()

# We perform basic element-wise operations using arithmetic symbols and functions
print('X + Y = \n', X + Y)
print()
print('add(X,Y) = \n', np.add(X,Y))
print()
print('X - Y = \n', X - Y)
print()
print('subtract(X,Y) = \n', np.subtract(X,Y))
print()
print('X * Y = \n', X * Y)
print()
print('multiply(X,Y) = \n', np.multiply(X,Y))
print()
print('X / Y = \n', X / Y)
print()
print('divide(X,Y) = \n', np.divide(X,Y))
```


    X = 
     [[1 2]
     [3 4]]
    
    Y = 
     [[5.5 6.5]
     [7.5 8.5]]
    
    X + Y = 
     [[ 6.5  8.5]
     [10.5 12.5]]
    
    add(X,Y) = 
     [[ 6.5  8.5]
     [10.5 12.5]]
    
    X - Y = 
     [[-4.5 -4.5]
     [-4.5 -4.5]]
    
    subtract(X,Y) = 
     [[-4.5 -4.5]
     [-4.5 -4.5]]
    
    X * Y = 
     [[ 5.5 13. ]
     [22.5 34. ]]
    
    multiply(X,Y) = 
     [[ 5.5 13. ]
     [22.5 34. ]]
    
    X / Y = 
     [[0.18181818 0.30769231]
     [0.4        0.47058824]]
    
    divide(X,Y) = 
     [[0.18181818 0.30769231]
     [0.4        0.47058824]]


### 数学函数，例如 sqrt(x)
>我们还可以同时对 ndarray 的所有元素应用数学函数，例如 sqrt(x)。


```python
# We create a rank 1 ndarray
x = np.array([1,2,3,4])

# We print x
print()
print('x = ', x)

# We apply different mathematical functions to all elements of x
print()
print('EXP(x) =', np.exp(x))
print()
print('SQRT(x) =',np.sqrt(x))
print()
print('POW(x,2) =',np.power(x,2)) # We raise all elements to the power of 2
```


    x =  [1 2 3 4]
    
    EXP(x) = [ 2.71828183  7.3890561  20.08553692 54.59815003]
    
    SQRT(x) = [1.         1.41421356 1.73205081 2.        ]
    
    POW(x,2) = [ 1  4  9 16]


>NumPy 的另一个重要特性是具有大量不同的统计学函数。统计学函数为我们提供了关于 ndarray 中元素的统计学信息。我们来看一些示例：
- `x.mean()` 平均值
- `x.sum()` 求和
- `x.std()` 标准差
- `x.median()` 中位数
- `x.max()` 最大值
- `x.min()` 最小值


```python
# We create a 2 x 2 ndarray
X = np.array([[1,2], [3,4]])

# We print x
print()
print('X = \n', X)
print()

print('Average of all elements in X:', X.mean())
print('Average of all elements in the columns of X:', X.mean(axis=0))
print('Average of all elements in the rows of X:', X.mean(axis=1))
print()
print('Sum of all elements in X:', X.sum())
print('Sum of all elements in the columns of X:', X.sum(axis=0))
print('Sum of all elements in the rows of X:', X.sum(axis=1))
print()
print('Standard Deviation of all elements in X:', X.std())
print('Standard Deviation of all elements in the columns of X:', X.std(axis=0))
print('Standard Deviation of all elements in the rows of X:', X.std(axis=1))
print()
print('Median of all elements in X:', np.median(X))
print('Median of all elements in the columns of X:', np.median(X,axis=0))
print('Median of all elements in the rows of X:', np.median(X,axis=1))
print()
print('Maximum value of all elements in X:', X.max())
print('Maximum value of all elements in the columns of X:', X.max(axis=0))
print('Maximum value of all elements in the rows of X:', X.max(axis=1))
print()
print('Minimum value of all elements in X:', X.min())
print('Minimum value of all elements in the columns of X:', X.min(axis=0))
print('Minimum value of all elements in the rows of X:', X.min(axis=1))
```


    X = 
     [[1 2]
     [3 4]]
    
    Average of all elements in X: 2.5
    Average of all elements in the columns of X: [2. 3.]
    Average of all elements in the rows of X: [1.5 3.5]
    
    Sum of all elements in X: 10
    Sum of all elements in the columns of X: [4 6]
    Sum of all elements in the rows of X: [3 7]
    
    Standard Deviation of all elements in X: 1.118033988749895
    Standard Deviation of all elements in the columns of X: [1. 1.]
    Standard Deviation of all elements in the rows of X: [0.5 0.5]
    
    Median of all elements in X: 2.5
    Median of all elements in the columns of X: [2. 3.]
    Median of all elements in the rows of X: [1.5 3.5]
    
    Maximum value of all elements in X: 4
    Maximum value of all elements in the columns of X: [3 4]
    Maximum value of all elements in the rows of X: [2 4]
    
    Minimum value of all elements in X: 1
    Minimum value of all elements in the columns of X: [1 2]
    Minimum value of all elements in the rows of X: [1 3]


>最后，我们来看看 NumPy 如何使 ndarray 中的所有元素与单个数字相加，而不使用复杂的循环。

### 广播 broadcast


```python
# We create a 2 x 2 ndarray
X = np.array([[1,2], [3,4]])

# We print x
print()
print('X = \n', X)
print()

print('3 * X = \n', 3 * X)
print()
print('3 + X = \n', 3 + X)
print()
print('X - 3 = \n', X - 3)
print()
print('X / 3 = \n', X / 3)
```


    X = 
     [[1 2]
     [3 4]]
    
    3 * X = 
     [[ 3  6]
     [ 9 12]]
    
    3 + X = 
     [[4 5]
     [6 7]]
    
    X - 3 = 
     [[-2 -1]
     [ 0  1]]
    
    X / 3 = 
     [[0.33333333 0.66666667]
     [1.         1.33333333]]


>在上述示例中，NumPy 在后台对 ndarray 广播 3，使它们具有相同的形状。这样我们仅使用一行代码，就可以使 X 的每个元素加 3。

>Numpy 可以对两个形状不同的 ndarray 执行相同的操作，但是存在一些限制，如下所示。


```python
# We create a rank 1 ndarray
x = np.array([1,2,3])

# We create a 3 x 3 ndarray
Y = np.array([[1,2,3],[4,5,6],[7,8,9]])

# We create a 3 x 1 ndarray
Z = np.array([1,2,3]).reshape(3,1)

# We print x
print()
print('x = ', x)
print()

# We print Y
print()
print('Y = \n', Y)
print()

# We print Z
print()
print('Z = \n', Z)
print()

print('x + Y = \n', x + Y)
print()
print('Z + Y = \n',Z + Y)
```


    x =  [1 2 3]


​    
​    Y = 
​     [[1 2 3]
​     [4 5 6]
​     [7 8 9]]


​    
​    Z = 
​     [[1]
​     [2]
​     [3]]
​    
​    x + Y = 
​     [[ 2  4  6]
​     [ 5  7  9]
​     [ 8 10 12]]
​    
​    Z + Y = 
​     [[ 2  3  4]
​     [ 6  7  8]
​     [10 11 12]]


>和之前一样，NumPy 能够通过沿着大的 ndarray 对更小的 ndarray 进行广播，将 1 x 3 和 3 x 1 ndarray 加到 3 x 3 ndarray 上。通常，NumPy 能够这么操作的前提是，更小的 ndarray（例如我们的示例中的 1 x 3 ndarray）可以扩展成更大的 ndarray 的形状，并且生成的广播很清晰明确。
