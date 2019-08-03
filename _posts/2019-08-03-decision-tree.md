---
layout: post
title: "「数据挖掘」决策树"
subtitle: 'Data Mining : Decision Tree'
author: "Hufe"
header-img: "img/post-bg-datas.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Python
  - Data Analysis
  - Data Mining
---



# 决策树 Decision Tree

## 决策树的工作原理

决策树基本上就是把我们以前的经验总结出来。我给你准备了一个打篮球的训练集。如果我们要出门打篮球，一般会根据“天气”、“温度”、“湿度”、“刮风”这几个条件来判断，最后得到结果：去打篮球？还是不去？

![img](https://static001.geekbang.org/resource/image/dc/90/dca4224b342894f12f54a9cb41d8cd90.jpg)


上面这个图就是一棵典型的决策树。我们在做决策树的时候，会经历两个阶段：**构造和剪枝**。

### **构造**

什么是构造呢？构造就是生成一棵完整的决策树。简单来说，**构造的过程就是选择什么属性作为节点的过程**，那么在构造过程中，会存在三种节点：

1. **根节点**：就是树的最顶端，最开始的那个节点。在上图中，“天气”就是一个根节点；
2. **内部节点**：就是树中间的那些节点，比如说“温度”、“湿度”、“刮风”；
3. **叶节点**：就是树最底部的节点，也就是决策结果。

节点之间存在父子关系。比如根节点会有子节点，子节点会有子子节点，但是到了叶节点就停止了，叶节点不存在子节点。那么在构造过程中，你要解决三个重要的问题：

1. 选择哪个属性作为根节点；
2. 选择哪些属性作为子节点；
3. 什么时候停止并得到目标状态，即叶节点。

### **剪枝**

决策树构造出来之后是不是就万事大吉了呢？也不尽然，我们可能还需要对决策树进行剪枝。剪枝就是给决策树瘦身，这一步想实现的目标就是，不需要太多的判断，同样可以得到不错的结果。之所以这么做，是为了防止“过拟合”（Overfitting）现象的发生。

“过拟合”这个概念你一定要理解，它指的就是模型的训练结果“太好了”，以至于在实际应用的过程中，会存在“死板”的情况，导致分类错误。

欠拟合，和过拟合就好比是下面这张图中的第一个和第三个情况一样，训练的结果“太好“，反而在实际应用过程中会导致分类错误。

![img](https://static001.geekbang.org/resource/image/d3/df/d30bfa3954ffdf5baf47ce53df9366df.jpg)

造成过拟合的原因之一就是因为训练集中样本量较小。如果决策树选择的属性过多，构造出来的决策树一定能够“完美”地把训练集中的样本分类，但是这样就会把训练集中一些数据的特点当成所有数据的特点，但这个特点不一定是全部数据的特点，这就使得这个决策树在真实的数据分类中出现错误，也就是模型的“泛化能力”差。

泛化能力指的分类器是通过训练集抽象出来的分类能力，你也可以理解是举一反三的能力。如果我们太依赖于训练集的数据，那么得到的决策树容错率就会比较低，泛化能力差。因为训练集只是全部数据的抽样，并不能体现全部数据的特点。

既然要对决策树进行剪枝，具体有哪些方法呢？一般来说，剪枝可以分为**“预剪枝”（Pre-Pruning）和“后剪枝”（Post-Pruning）**。

- 预剪枝是在决策树构造时就进行剪枝。方法是在构造的过程中对节点进行评估，如果对某个节点进行划分，在验证集中不能带来准确性的提升，那么对这个节点进行划分就没有意义，这时就会把当前节点作为叶节点，不对其进行划分。

- 后剪枝就是在生成决策树之后再进行剪枝，通常会从决策树的叶节点开始，逐层向上对每个节点进行评估。如果剪掉这个节点子树，与保留该节点子树在分类准确性上差别不大，或者剪掉该节点子树，能在验证集中带来准确性的提升，那么就可以把该节点子树进行剪枝。方法是：用这个节点子树的叶子节点来替代该节点，类标记为这个节点子树中最频繁的那个类。

## **纯度** 、 **信息熵** 和 **信息增益**

### 纯度
- 集合 1：6 次都去打篮球；
- 集合 2：4 次去打篮球，2 次不去打篮球；
- 集合 3：3 次去打篮球，3 次不去打篮球。

按照纯度指标来说，集合 1> 集合 2> 集合 3。因为集合 1 的分歧最小，集合 3 的分歧最大。

### 信息熵（entropy）
**它表示了信息的不确定度**。
计算信息熵的数学公式：

$$
\text {Entropy}(t)=-\sum_{i=0}^{c-1} p(i | t) \log _{2} p(i | t)
$$

$p(i \| t)$ 代表了节点  $t$  为分类  $i$  的概率，其中  $\log _{2}$  为取以 2 为底的对数。

- 集合 1：5 次去打篮球，1 次不去打篮球；

$$
\text {Entropy}(t)=-(1 / 6) \log _{2}(1 / 6)-(5 / 6) \log _{2}(5 / 6)=0.65
$$

- 集合 2：3 次去打篮球，3 次不去打篮球。

$$
\text {Entropy(t)}=-(3 / 6) \log _{2}(3 / 6)-(3 / 6) \log _{2}(3 / 6)=1
$$

### 信息增益
**信息增益**，指的就是划分可以带来纯度的提高，信息熵的下降。它的计算公式，是父亲节点的信息熵减去所有子节点的信息熵。在计算的过程中，我们会计算每个子节点的归一化信息熵，即按照每个子节点在父节点中出现的概率，来计算这些子节点的信息熵。

- 信息增益的公式

$$
\operatorname{Gain}(D, a)=\text { Entropy }(D)-\sum_{i=1}^{k} \frac{\left|D_{i}\right|}{|D|} \text { Entropy }\left(D_{i}\right)
$$

![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.m6prp9zof4l.png)

$$
\operatorname{Gain}(D, a)=\operatorname{Entropy}(D)-\left(\frac{3}{10} \operatorname{Entropy}\left(D_{1}\right)+\frac{7}{10} \operatorname{Entropy}\left(D_{2}\right)\right)
$$

## ID3算法

ID3 算法计算的是**信息增益**，信息增益指的就是划分可以带来纯度的提高，信息熵的下降。它的计算公式，是父亲节点的信息熵减去所有子节点的信息熵。在计算的过程中，我们会计算每个子节点的归一化信息熵，即按照每个子节点在父节点中出现的概率，来计算这些子节点的信息熵。

基于 ID3 的算法规则，完整地计算下我们的训练集，训练集中一共有 7 条数据，3 个打篮球，4 个不打篮球，所以根节点的信息熵是：

$$
\operatorname{Ent}(D)=-\sum_{k=1}^{2} p_{k} \log _{2} p_{k}=-\left(\frac{3}{7} \log _{2} \frac{3}{7}+\frac{4}{7} \log _{2} \frac{4}{7}\right)=0.985
$$

![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/image.atas6ldmqt.png)

将天气作为属性的划分，会有三个叶子节点 D1、D2 和 D3，分别对应的是**晴天、阴天和小雨**。我们用 + 代表去打篮球，- 代表不去打篮球。那么第一条记录，晴天不去打篮球，可以记为 1-，于是我们可以用下面的方式来记录 D1，D2，D3：

D1(天气 = 晴天)={1-,2-,6+}

D2(天气 = 阴天)={3+,7-}

D3(天气 = 小雨)={4+,5-}

- 先计算天气属性的信息熵

$$
\begin{aligned} \operatorname{Entropy}\left(\mathrm{D=天气}\right)=&-\left(\frac{3}{7} \log _{2} \frac{3}{7} + \frac{4}{7} \log _{2} \frac{4}{7}\right)=0.985  \end{aligned}
$$

- 再分别计算三个叶子节点的信息熵：

$$
\begin{aligned} \operatorname{Ent}\left(\mathrm{D}_{晴}\right)=&-\left(\frac{1}{3} \log _{2} \frac{1}{3}+\frac{2}{3} \log _{2} \frac{2}{3}\right)=0.918 \\ \operatorname{Ent}\left(\mathrm{D}_{阴}\right) &=-\left(\frac{1}{2} \log _{2} \frac{1}{2}+\frac{1}{2} \log _{2} \frac{1}{2}\right)=1.0 \\ \operatorname{Ent}\left(\mathrm{D}_{雨}\right) &=-\left(\frac{1}{2} \log _{2} \frac{1}{2}+\frac{1}{2} \log _{2} \frac{1}{2}\right)=1.0 \end{aligned}
$$


```python
import math
a = (-3/7) * (math.log((3/7), 2)) + (-4/7)*(math.log((4/7), 2))
b = (-1/3) * (math.log((1/3), 2)) + (-2/3)*(math.log((2/3), 2))
c = (-1/2) * (math.log((1/2), 2)) + (-1/2)*(math.log((1/2), 2))
d = (-1/4) * (math.log((1/4), 2)) + (-3/4)*(math.log((3/4), 2))
a, b, c, d
```


    (0.9852281360342516, 0.9182958340544896, 1.0, 0.8112781244591328)



因为 D1 有 3 个记录，D2 有 2 个记录，D3 有 2 个记录，所以 D 中的记录一共是 3+2+2=7，即总数为 7。
所以 D1 在 D（父节点）中的概率是 3/7，D2 在父节点的概率是 2/7，D3 在父节点的概率是 2/7。
- 计算归一化信息熵

$$
\left(\frac{3}{7} \operatorname{Entropy}\left(D_{晴}\right)+\frac{2}{7} \operatorname{Entropy}\left(D_{阴}\right)+\frac{2}{7} \operatorname{Entropy}\left(D_{雨}\right)\right) =  \\ =\left(\frac{3}{7}*0.918+\frac{2}{7}*1.0+\frac{2}{7}*1.0\right) = 0.965
$$

- 计算信息增益

$$
\operatorname{Gain}(D, 天气)  =\operatorname{Entropy}(D=天气)-\left(\frac{3}{7} \operatorname{Entropy}\left(D_{晴}\right)+\frac{2}{7} \operatorname{Entropy}\left(D_{阴}\right)+\frac{2}{7} \operatorname{Entropy}\left(D_{雨}\right)\right) \\ =0.985-0.965 =0.202
$$

$Gain(D , 温度)=0.128$

$Gain(D , 湿度)=0.020$

$Gain(D , 刮风)=0.020$

- 温度

|**温度**|高 | 高 | 高 | 高 | 低 | 中 | 中|
|---| --- | --- | --- | --- | --- | ---|---|
|**是否去打球**|-|-|+|+|-|+|-|



```python
e = 4/7 * c + 1/7 * 0 + 2/7 * c
a - e
```


    0.12808527889139454



- 刮风

|**刮风**|否 | 是 | 否 | 否 | 否 | 是 | 是|
|---| --- | --- | --- | --- | --- | ---|---|
|**是否去打球**|-|-|+|+|-|+|-|


```python
f = 4/7 * c + 3/7 * b
a - f
```


    0.02024420715375619



然后我们要将上图中第一个叶节点，也就是 D1={1-,2-,3+,4+}进一步进行分裂，往下划分，计算其不同属性（天气、湿度、刮风）作为节点的信息增益，可以得到：
![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.kfd26lvvn88.png)

$Gain(D , 湿度)=1$

$Gain(D , 天气)=1$

$Gain(D , 刮风)=0.3113$

- 刮风

$Enttopy(晴=高) = 1$


```python
1 - (3/4 * b + 1/4 * 0)
```


    0.31127812445913283



可以看到**湿度**，或者**天气**为 D1(温度=高)的节点都可以得到最大的信息增益，这里我们选取湿度作为节点的属性划分。同理，我们可以按照上面的计算步骤得到完整的决策树，结果如下：
![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.wbsafj8wp7.png)

## ID3 算法来给出好苹果的决策树
![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.2p6i3vjuxx5.png)


```python
from sklearn import tree
import sys
import os
import graphviz
import numpy as np

# 创建数据[红，大]，1==是，0==否
data = np.array([[1, 1], [1, 0], [0, 1], [0, 0]])
# 数据标注为，1==好苹果，0==坏苹果
target = np.array([1, 1, 0, 0])

clf = tree.DecisionTreeClassifier()  # 创建决策树分类器模型
clf = clf.fit(data, target)  # 拟合数据

# 最后利用graphviz库打印出决策树图
dot_data = tree.export_graphviz(
    clf, out_file=None, filled=True, rounded=True, special_characters=True)
dot_data = tree.export_graphviz(clf, out_file=None)
graph = graphviz.Source(dot_data)
graph
```



![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.022jw7kauv19.png)

# C4.5 算法
**在 ID3 算法上进行改进的 C4.5 算法**

那么 C4.5 都在哪些方面改进了 ID3 呢？

**1. 采用信息增益率**

因为 ID3 在计算的时候，倾向于选择取值多的属性。为了避免这个问题，C4.5 采用信息增益率的方式来选择属性。信息增益率 = 信息增益 / 属性熵。

$Gain\_ratio(D)=\frac{信息增益}{属性熵} = \frac{Gain(D,a)}{Entropy(D)}$

当属性有很多值的时候，相当于被划分成了许多份，虽然信息增益变大了，但是对于 C4.5 来说，属性熵也会变大，所以整体的信息增益率并不大。

**2. 采用悲观剪枝**

ID3 构造决策树的时候，容易产生过拟合的情况。在 C4.5 中，会在决策树构造之后采用悲观剪枝（PEP），这样可以提升决策树的泛化能力。

悲观剪枝是后剪枝技术中的一种，通过递归估算每个内部节点的分类错误率，比较剪枝前后这个节点的分类错误率来决定是否对其进行剪枝。这种剪枝方法不再需要一个单独的测试数据集。

**3. 离散化处理连续属性**

C4.5 可以处理连续属性的情况，对连续的属性进行离散化的处理。比如打篮球存在的“湿度”属性，不按照“高、中”划分，而是按照湿度值进行计算，那么湿度取什么值都有可能。该怎么选择这个阈值呢，**C4.5 选择具有最高信息增益的划分所对应的阈值**。

**4. 处理缺失值**

针对数据集不完整的情况，C4.5 也可以进行处理。

假如我们得到的是如下的数据，你会发现这个数据中存在两点问题。第一个问题是，数据集中存在数值缺失的情况，如何进行属性选择？第二个问题是，假设已经做了属性划分，但是样本在这个属性上有缺失值，该如何对样本进行划分？

![img](https://static001.geekbang.org/resource/image/50/95/50b43c1820c03561f3ca3e627b454995.png)

我们不考虑缺失的数值，可以得到
- 温度 D={2-,3+,4+,5-,6+,7-}。
- 温度 = 高：D1={2-,3+,4+} 
- 温度 = 中：D2={6+,7-}
- 温度 = 低：D3={5-}

这里 + 号代表打篮球，- 号代表不打篮球。比如 ID=2 时，决策是不打篮球，我们可以记录为 2-。

$Gain\_ratio(D)=\frac{信息增益}{属性熵} = \frac{Gain(D,a)}{Entropy(D)}$

针对将属性选择为温度的信息增益为：

$Gain(D′, 温度)=Ent(D′)-0.792=1.0-0.792=0.208$

属性熵 =1.459, 

- 信息增益率

$Gain\_ratio(D′, 温度)=0.208/1.459=0.1426$

D′的样本个数为 6，而 D 的样本个数为 7，所以所占权重比例为 6/7，所以 Gain(D′，温度) 所占权重比例为 6/7，所以：

$Gain\_ratio(D, 温度)=6/7*0.1426=0.122$

这样即使在温度属性的数值有缺失的情况下，我们依然可以计算信息增益，并对属性进行选择。

# 总结下 ID3 和 C4.5 算法
首先 ID3 算法的优点是方法简单，缺点是对噪声敏感。训练数据如果有少量错误，可能会产生决策树分类错误。

C4.5 在 ID3 的基础上，用**信息增益率**代替了信息增益，解决了噪声敏感的问题，并且可以对构造树进行剪枝、处理连续数值以及数值缺失等情况，但是由于 C4.5 需要对数据集进行多次扫描，算法效率相对较低。

![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.v3ky64ybr2f.png)

# CART算法


ID3 和 C4.5 算法可以生成二叉树或多叉树，而 CART 只支持二叉树。同时 CART 决策树比较特殊，**既可以作分类树，又可以作回归树**。

**构造一棵决策树**
- 想要基于数据判断这个人的职业身份，这个就属于**分类树**，因为是从几个分类中来做选择。
- 如果是给定了数据，想要预测这个人的年龄，那就属于**回归树**。

决策树的核心就是寻找纯净的划分，因此引入了纯度的概念。在属性选择上，我们是通过统计“不纯度”来做判断的，
- ID3 是基于信息增益做判断
- C4.5 在 ID3 的基础上做了改进，提出了**信息增益率**的概念
实际上**CART 分类树**与 **C4.5 算法**类似，只是属性选择的指标采用的是**基尼系数**。

## Gini系数计算

$$
GINI(t)=1-\sum_{k}\left[p\left(C_{k} | t\right)\right]^{2}
$$

$p(Ck \| t)$ 表示节点 $t$ 属于类别 $Ck$ 的概率，节点 $t$ 的基尼系数为 1 减去各类别 $Ck$ 概率平方和。

集合 1：6 个都去打篮球；

集合 2：3 个去打篮球，3 个不去打篮球。

针对集合 1，所有人都去打篮球，所以 $p(Ck \| t)=1$，因此 $GINI(t)=1-1=0$。

针对集合 2，有一半人去打篮球，而另一半不去打篮球，所以， $p(C1 \| t)=0.5$ ， $p(C2 \| t)=0.5$ ，$G I N I(t)=1-(0.5 * 0.5+0.5 * 0.5)=0.5$

![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.283agl734at.png)
节点 D 的基尼系数等于子节点 D1 和 D2 的归一化基尼系数之和，用公式表示为：

$$
G I N I(D, A)=\frac{D_{1}}{D} G I M I\left(D_{1}\right)+\frac{D_{2}}{D} G I N I\left(D_{2}\right)
$$

归一化基尼系数代表的是每个子节点的基尼系数乘以该节点占整体父亲节点 D 中的比例。

上面我们已经计算了集合 D1 和集合 D2 的 GINI 系数，得到：

$$
\begin{array}{l}{G I N I\left(D_{1}\right)=0} \\ {G I N I\left(D_{2}\right)=0.5}\end{array}
$$

所以在属性 A 的划分下，节点 D 的基尼系数为：

$$
G I N I(D, A)=\frac{6}{12} G I N I\left(D_{1}\right)+\frac{6}{12} G I N I\left(D_{2}\right)=0.25
$$

节点 D 被属性 A 划分后的基尼系数越大，样本集合的不确定性越大，也就是不纯度越高。

## CART 分类树

在 Python 的 sklearn 中，如果要创建 CART 分类树，可以直接使用 `DecisionTreeClassifier` 这个类。创建这个类的时候，默认情况下 `criterion` 这个参数等于 `gini`，也就是按照基尼系数来选择属性划分，即默认采用的是 CART 分类树。


```python
# encoding=utf-8
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score
from sklearn.tree import DecisionTreeClassifier
from sklearn.datasets import load_iris
from sklearn import tree
import graphviz
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
dot_data = tree.export_graphviz(
    clf, out_file=None, feature_names=iris.feature_names, class_names=iris.target_names, filled=True, rounded=True, special_characters=True)
graph = graphviz.Source(dot_data)
graph.render("iris_cart_classifier")
graph
```

![image](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/Picee/image.39w0xoohai3.png)

## CART 回归树

在 CART 分类树中采用的是基尼系数作为标准，那么在 CART 回归树中，如何评价“不纯度”呢？实际上我们要根据样本的混乱程度，也就是样本的离散程度来评价“不纯度”。

样本的离散程度具体的计算方式是，先计算所有样本的均值，然后计算每个样本值到均值的差值。我们假设 $x$ 为样本的个体，均值为 $u$。为了统计样本的离散程度，我们可以取差值的绝对值，或者方差。

其中差值的绝对值为样本值减去样本均值的绝对值：
$$
|x-\mu|
$$

方差为每个样本值减去样本均值的平方和除以样本个数：
$$
s=\frac{1}{n} \sum(x-\mu)^{2}
$$

### LAD & LSD
所以这两种节点划分的标准，分别对应着两种目标函数最优化的标准，即用最小绝对偏差（LAD），或者使用最小二乘偏差（LSD）。

这两种方式都可以让我们找到节点划分的方法，通常使用最小二乘偏差的情况更常见一些。


```python
# encoding=utf-8
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.datasets import load_boston
from sklearn.metrics import r2_score, mean_absolute_error, mean_squared_error
from sklearn.tree import DecisionTreeRegressor
from sklearn import tree
import graphviz
# 准备数据集
boston = load_boston()
# 探索数据
print(boston.feature_names)
# 获取特征集和房价
features = boston.data
prices = boston.target
# 随机抽取 33% 的数据作为测试集，其余为训练集
train_features, test_features, train_price, test_price = train_test_split(
    features, prices, test_size=0.33)
# 创建 CART 回归树
dtr = DecisionTreeRegressor()
# 拟合构造 CART 回归树
dtr.fit(train_features, train_price)
# 预测测试集中的房价
predict_price = dtr.predict(test_features)
# 测试集的结果评价
print('回归树二乘偏差均值:', mean_squared_error(test_price, predict_price))
print('回归树绝对值偏差均值:', mean_absolute_error(test_price, predict_price))
```

    ['CRIM' 'ZN' 'INDUS' 'CHAS' 'NOX' 'RM' 'AGE' 'DIS' 'RAD' 'TAX' 'PTRATIO'
     'B' 'LSTAT']
    回归树二乘偏差均值: 14.179700598802393
    回归树绝对值偏差均值: 2.6233532934131736



```python
# 最后利用graphviz库打印出决策树图
dot_data = tree.export_graphviz(dtr,
                                out_file=None, rounded=True, special_characters=True, filled=True)
graph = graphviz.Source(dot_data)
graph.render("boston_cart_regressor")
graph
```

[查看决策树图](https://hufe09.github.io/html/boston_cart_regressor.pdf)