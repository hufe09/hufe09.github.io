---
layout: post
title: "「数据争论」Module 03: Assessing Data"
subtitle: '比较新口服胰岛素治疗糖尿病和注射胰岛素的有效性和安全性--评估'
author: "Hufe"
header-img: "img/post-bg-datas.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Python
  - Data Analysis
  - Data Wrangling
---



## Assessing
## 评估数据

**评估**数据是数据争论的第二步。在评估时，就像工作中的侦探，检查数据集有两件事：

- 数据质量问题（*即内容问题*）
- 数据整洁问题（*即结构问题*）

## 数据集介绍
>PS：此数据不是“真实的”

Auralin和Novodra **不是**真正的胰岛素产品。该临床试验数据是为了本此分析而制作的。在评估此数据时，将检测到（以及稍后清理）的问题旨在模拟真实世界的数据质量和整洁问题。



- 该数据集是在真实医生的咨询下构建的，以确保合理性。
- 这种替代胰岛素的临床试验数据受到启发，并且与[一种名为Afrezza的新吸入式胰岛素](http://care.diabetesjournals.org/content/38/12/2266.long)的真实临床试验密切相关。
- 此数据集中的数据质量问题模拟[医疗保健数据中](http://media.hypersites.com/clients/1446/filemanager/Articles/DocCenter_Problem_with_data.pdf)常见的实际数据质量问题。这些问题会影响护理质量，患者登记和收入。
- 此数据集中的患者是使用此[假名称生成器](http://www.fakenamegenerator.com/order.php)创建的，不包括真实姓名，地址，电话号码，电子邮件等。

## Gather


```python
import numpy as np
import pandas as pd
pd.set_option('display.max_columns', 100)  # 设置显示数据的最大列数，防止出现省略号…，导致数据显示不全
pd.set_option('expand_frame_repr', False)  # 当列太多时不自动换行
```


```python
patients = pd.read_csv('patients.csv')
treatments = pd.read_csv('treatments.csv')
adverse_reactions = pd.read_csv('adverse_reactions.csv')
```

## Assess

- ### patients/病人 表


```python
#显示前5行
patients.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>patient_id</th>
      <th>assigned_sex</th>
      <th>given_name</th>
      <th>surname</th>
      <th>address</th>
      <th>city</th>
      <th>state</th>
      <th>zip_code</th>
      <th>country</th>
      <th>contact</th>
      <th>birthdate</th>
      <th>weight</th>
      <th>height</th>
      <th>bmi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>female</td>
      <td>Zoe</td>
      <td>Wellish</td>
      <td>576 Brown Bear Drive</td>
      <td>Rancho California</td>
      <td>California</td>
      <td>92390.0</td>
      <td>United States</td>
      <td>951-719-9170ZoeWellish@superrito.com</td>
      <td>7/10/1976</td>
      <td>121.7</td>
      <td>66</td>
      <td>19.6</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>female</td>
      <td>Pamela</td>
      <td>Hill</td>
      <td>2370 University Hill Road</td>
      <td>Armstrong</td>
      <td>Illinois</td>
      <td>61812.0</td>
      <td>United States</td>
      <td>PamelaSHill@cuvox.de+1 (217) 569-3204</td>
      <td>4/3/1967</td>
      <td>118.8</td>
      <td>66</td>
      <td>19.2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>male</td>
      <td>Jae</td>
      <td>Debord</td>
      <td>1493 Poling Farm Road</td>
      <td>York</td>
      <td>Nebraska</td>
      <td>68467.0</td>
      <td>United States</td>
      <td>402-363-6804JaeMDebord@gustr.com</td>
      <td>2/19/1980</td>
      <td>177.8</td>
      <td>71</td>
      <td>24.8</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>male</td>
      <td>Liêm</td>
      <td>Phan</td>
      <td>2335 Webster Street</td>
      <td>Woodbridge</td>
      <td>NJ</td>
      <td>7095.0</td>
      <td>United States</td>
      <td>PhanBaLiem@jourrapide.com+1 (732) 636-8246</td>
      <td>7/26/1951</td>
      <td>220.9</td>
      <td>70</td>
      <td>31.7</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>male</td>
      <td>Tim</td>
      <td>Neudorf</td>
      <td>1428 Turkey Pen Lane</td>
      <td>Dothan</td>
      <td>AL</td>
      <td>36303.0</td>
      <td>United States</td>
      <td>334-515-7487TimNeudorf@cuvox.de</td>
      <td>2/18/1928</td>
      <td>192.3</td>
      <td>27</td>
      <td>26.1</td>
    </tr>
  </tbody>
</table>
</div>



`patients` 列:
- **patient_id**: 生产Auralin的制药公司的[主患者指数](https://en.wikipedia.org/wiki/Enterprise_master_patient_index)（即患者数据库）中每位患者的唯一标识符
- **assigned_sex**: 出生时每位患者的指定性别（男性或女性）
- **given_name**: 每个患者的名字
- **surname**: 每位患者的姓氏
- **address**: 每位患者的主要地址
- **city**: 每个病人主要地址的相应城市
- **state**: 每个患者主要地址的对应状态
- **zip_code**: 每个患者主要地址的相应邮政编码
- **country**: 每个患者主要地址的相应国家（本次临床试验的所有美国）
- **contact**: 每位患者的电话号码和电子邮件信息
- **birthdate**: 每位患者的出生日期（月/日/年）。该临床试验的纳入标准是年龄>=18 （没有最大年龄，因为糖尿病是老年人群中日益严重的问题）
- **weight**: 每位患者的体重（lbns/ 磅）
- **height**: 每位患者的身高（in/ 英寸）
- **bmi**: 每位患者的体重指数（BMI）。BMI是一个使用人的身高和体重的简单计算。公式为BMI = kg/m<sup>2</sup>，其中kg是一个人的体重（千克），m<sup>2</sup>是他们的身高（米）。BMI为25.0或以上是超重，而健康范围为18.5至24.9。该临床试验的纳入标准是16 >= BMI >= 38。


```python
# 简单摘要
patients.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 503 entries, 0 to 502
    Data columns (total 14 columns):
    patient_id      503 non-null int64
    assigned_sex    503 non-null object
    given_name      503 non-null object
    surname         503 non-null object
    address         491 non-null object
    city            491 non-null object
    state           491 non-null object
    zip_code        491 non-null float64
    country         491 non-null object
    contact         491 non-null object
    birthdate       503 non-null object
    weight          503 non-null float64
    height          503 non-null int64
    bmi             503 non-null float64
    dtypes: float64(3), int64(2), object(9)
    memory usage: 55.1+ KB



```python
# 各列汇总统计数据
patients.describe()
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>patient_id</th>
      <th>zip_code</th>
      <th>weight</th>
      <th>height</th>
      <th>bmi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>503.000000</td>
      <td>491.000000</td>
      <td>503.000000</td>
      <td>503.000000</td>
      <td>503.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>252.000000</td>
      <td>49084.118126</td>
      <td>173.434990</td>
      <td>66.634195</td>
      <td>27.483897</td>
    </tr>
    <tr>
      <th>std</th>
      <td>145.347859</td>
      <td>30265.807442</td>
      <td>33.916741</td>
      <td>4.411297</td>
      <td>5.276438</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>1002.000000</td>
      <td>48.800000</td>
      <td>27.000000</td>
      <td>17.100000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>126.500000</td>
      <td>21920.500000</td>
      <td>149.300000</td>
      <td>63.000000</td>
      <td>23.300000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>252.000000</td>
      <td>48057.000000</td>
      <td>175.300000</td>
      <td>67.000000</td>
      <td>27.200000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>377.500000</td>
      <td>75679.000000</td>
      <td>199.500000</td>
      <td>70.000000</td>
      <td>31.750000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>503.000000</td>
      <td>99701.000000</td>
      <td>255.900000</td>
      <td>79.000000</td>
      <td>37.700000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 随机样本
patients.sample(5)
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>patient_id</th>
      <th>assigned_sex</th>
      <th>given_name</th>
      <th>surname</th>
      <th>address</th>
      <th>city</th>
      <th>state</th>
      <th>zip_code</th>
      <th>country</th>
      <th>contact</th>
      <th>birthdate</th>
      <th>weight</th>
      <th>height</th>
      <th>bmi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>461</th>
      <td>462</td>
      <td>male</td>
      <td>Cannan</td>
      <td>Cabrera</td>
      <td>2102 Geraldine Lane</td>
      <td>New York</td>
      <td>NY</td>
      <td>10014.0</td>
      <td>United States</td>
      <td>646-289-4177CannanCabreraOrdonez@superrito.com</td>
      <td>10/12/1980</td>
      <td>209.7</td>
      <td>71</td>
      <td>29.2</td>
    </tr>
    <tr>
      <th>219</th>
      <td>220</td>
      <td>male</td>
      <td>Mỹ</td>
      <td>Quynh</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4/9/1978</td>
      <td>237.8</td>
      <td>69</td>
      <td>35.1</td>
    </tr>
    <tr>
      <th>497</th>
      <td>498</td>
      <td>male</td>
      <td>Masataka</td>
      <td>Murakami</td>
      <td>1179 Patton Lane</td>
      <td>Tulsa</td>
      <td>OK</td>
      <td>74116.0</td>
      <td>United States</td>
      <td>MasatakaMurakami@einrot.com+1 (918) 984-9171</td>
      <td>8/19/1937</td>
      <td>155.1</td>
      <td>72</td>
      <td>21.0</td>
    </tr>
    <tr>
      <th>380</th>
      <td>381</td>
      <td>female</td>
      <td>Silje</td>
      <td>Kristiansen</td>
      <td>4992 Moonlight Drive</td>
      <td>Pleasantville</td>
      <td>NJ</td>
      <td>8232.0</td>
      <td>United States</td>
      <td>SiljeAKristiansen@dayrep.com609-641-2070</td>
      <td>2/7/1996</td>
      <td>162.4</td>
      <td>64</td>
      <td>27.9</td>
    </tr>
    <tr>
      <th>272</th>
      <td>273</td>
      <td>female</td>
      <td>Klementyna</td>
      <td>Sokołowska</td>
      <td>202 Ingram Street</td>
      <td>Dayton</td>
      <td>OH</td>
      <td>45402.0</td>
      <td>United States</td>
      <td>KlementynaSokolowska@superrito.com+1 (937) 518...</td>
      <td>3/6/1955</td>
      <td>168.7</td>
      <td>60</td>
      <td>32.9</td>
    </tr>
  </tbody>
</table>
</div>




```python
#　检查缺失数据
patients[patients['address'].isnull()]
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>patient_id</th>
      <th>assigned_sex</th>
      <th>given_name</th>
      <th>surname</th>
      <th>address</th>
      <th>city</th>
      <th>state</th>
      <th>zip_code</th>
      <th>country</th>
      <th>contact</th>
      <th>birthdate</th>
      <th>weight</th>
      <th>height</th>
      <th>bmi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>209</th>
      <td>210</td>
      <td>female</td>
      <td>Lalita</td>
      <td>Eldarkhanov</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>8/14/1950</td>
      <td>143.4</td>
      <td>62</td>
      <td>26.2</td>
    </tr>
    <tr>
      <th>219</th>
      <td>220</td>
      <td>male</td>
      <td>Mỹ</td>
      <td>Quynh</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4/9/1978</td>
      <td>237.8</td>
      <td>69</td>
      <td>35.1</td>
    </tr>
    <tr>
      <th>230</th>
      <td>231</td>
      <td>female</td>
      <td>Elisabeth</td>
      <td>Knudsen</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9/23/1976</td>
      <td>165.9</td>
      <td>63</td>
      <td>29.4</td>
    </tr>
    <tr>
      <th>234</th>
      <td>235</td>
      <td>female</td>
      <td>Martina</td>
      <td>Tománková</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4/7/1936</td>
      <td>199.5</td>
      <td>65</td>
      <td>33.2</td>
    </tr>
    <tr>
      <th>242</th>
      <td>243</td>
      <td>male</td>
      <td>John</td>
      <td>O'Brian</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2/25/1957</td>
      <td>205.3</td>
      <td>74</td>
      <td>26.4</td>
    </tr>
    <tr>
      <th>249</th>
      <td>250</td>
      <td>male</td>
      <td>Benjamin</td>
      <td>Mehler</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10/30/1951</td>
      <td>146.5</td>
      <td>69</td>
      <td>21.6</td>
    </tr>
    <tr>
      <th>257</th>
      <td>258</td>
      <td>male</td>
      <td>Jin</td>
      <td>Kung</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/17/1995</td>
      <td>231.7</td>
      <td>69</td>
      <td>34.2</td>
    </tr>
    <tr>
      <th>264</th>
      <td>265</td>
      <td>female</td>
      <td>Wafiyyah</td>
      <td>Asfour</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>11/3/1989</td>
      <td>158.6</td>
      <td>63</td>
      <td>28.1</td>
    </tr>
    <tr>
      <th>269</th>
      <td>270</td>
      <td>female</td>
      <td>Flavia</td>
      <td>Fiorentino</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10/9/1937</td>
      <td>175.2</td>
      <td>61</td>
      <td>33.1</td>
    </tr>
    <tr>
      <th>278</th>
      <td>279</td>
      <td>female</td>
      <td>Generosa</td>
      <td>Cabán</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>12/16/1962</td>
      <td>124.3</td>
      <td>69</td>
      <td>18.4</td>
    </tr>
    <tr>
      <th>286</th>
      <td>287</td>
      <td>male</td>
      <td>Lewis</td>
      <td>Webb</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4/1/1979</td>
      <td>155.3</td>
      <td>68</td>
      <td>23.6</td>
    </tr>
    <tr>
      <th>296</th>
      <td>297</td>
      <td>female</td>
      <td>Chỉ</td>
      <td>Lâm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/14/1990</td>
      <td>181.1</td>
      <td>63</td>
      <td>32.1</td>
    </tr>
  </tbody>
</table>
</div>




```python
# 对surname列计数
patients.surname.value_counts()
```


    Doe            6
    Jakobsen       3
    Taylor         3
    Berg           2
    Batukayev      2
    Woźniak        2
    Lương          2
    Correia        2
    Kadyrov        2
    Liễu           2
    Gersten        2
    Aranda         2
    Grímsdóttir    2
    Dratchev       2
    Nilsen         2
    Tucker         2
    Lund           2
    Cabrera        2
    Ogochukwu      2
    Johnson        2
    Tạ             2
    Souza          2
    Schiavone      2
    Bùi            2
    Parker         2
    Silva          2
    Hueber         2
    Kowalczyk      2
    Lâm            2
    Collins        2
                  ..
    Vaneker        1
    Selassie       1
    Mathiesen      1
    Markus         1
    Villalpando    1
    Jørgensen      1
    Löfgren        1
    Wubbels        1
    Mobourne       1
    Zsinkó         1
    Filípek        1
    She            1
    Mikkelsen      1
    Wieczorek      1
    Zaitseva       1
    Sokołowska     1
    Miramontes     1
    Østergaard     1
    Leppäluoto     1
    Mehari         1
    Galić          1
    Scheltens      1
    Allaire        1
    Nyborg         1
    Cabán          1
    Knutsen        1
    Haugen         1
    Konovšek       1
    Cunha          1
    Ingason        1
    Name: surname, Length: 466, dtype: int64




```python
# 对weight列值排序
patients.weight.sort_values()
```


    210     48.8
    459    102.1
    335    102.7
    74     103.2
    317    106.0
    171    106.5
    51     107.1
    270    108.1
    198    108.5
    48     109.1
    478    109.6
    141    110.2
    38     111.8
    438    112.0
    14     112.0
    235    112.2
    307    112.4
    191    112.6
    408    113.1
    49     113.3
    326    114.0
    338    114.1
    253    117.0
    321    118.4
    168    118.8
    1      118.8
    350    119.0
    207    119.2
    265    120.0
    341    120.3
           ...  
    332    224.0
    252    224.2
    12     224.2
    222    224.8
    166    225.3
    111    225.9
    101    226.2
    150    226.6
    352    227.7
    428    227.7
    88     227.7
    13     228.4
    339    229.0
    182    230.3
    121    230.8
    257    231.7
    395    231.9
    246    232.1
    219    237.8
    11     238.7
    50     238.9
    441    239.1
    499    239.6
    439    242.0
    487    242.4
    144    244.9
    61     244.9
    283    245.5
    118    254.5
    485    255.9
    Name: weight, Length: 503, dtype: float64




```python
# 打印address列重复值
patients[patients.address.duplicated()]
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>patient_id</th>
      <th>assigned_sex</th>
      <th>given_name</th>
      <th>surname</th>
      <th>address</th>
      <th>city</th>
      <th>state</th>
      <th>zip_code</th>
      <th>country</th>
      <th>contact</th>
      <th>birthdate</th>
      <th>weight</th>
      <th>height</th>
      <th>bmi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>29</th>
      <td>30</td>
      <td>male</td>
      <td>Jake</td>
      <td>Jakobsen</td>
      <td>648 Old Dear Lane</td>
      <td>Port Jervis</td>
      <td>New York</td>
      <td>12771.0</td>
      <td>United States</td>
      <td>JakobCJakobsen@einrot.com+1 (845) 858-7707</td>
      <td>8/1/1985</td>
      <td>155.8</td>
      <td>67</td>
      <td>24.4</td>
    </tr>
    <tr>
      <th>219</th>
      <td>220</td>
      <td>male</td>
      <td>Mỹ</td>
      <td>Quynh</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4/9/1978</td>
      <td>237.8</td>
      <td>69</td>
      <td>35.1</td>
    </tr>
    <tr>
      <th>229</th>
      <td>230</td>
      <td>male</td>
      <td>John</td>
      <td>Doe</td>
      <td>123 Main Street</td>
      <td>New York</td>
      <td>NY</td>
      <td>12345.0</td>
      <td>United States</td>
      <td>johndoe@email.com1234567890</td>
      <td>1/1/1975</td>
      <td>180.0</td>
      <td>72</td>
      <td>24.4</td>
    </tr>
    <tr>
      <th>230</th>
      <td>231</td>
      <td>female</td>
      <td>Elisabeth</td>
      <td>Knudsen</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>9/23/1976</td>
      <td>165.9</td>
      <td>63</td>
      <td>29.4</td>
    </tr>
    <tr>
      <th>234</th>
      <td>235</td>
      <td>female</td>
      <td>Martina</td>
      <td>Tománková</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4/7/1936</td>
      <td>199.5</td>
      <td>65</td>
      <td>33.2</td>
    </tr>
    <tr>
      <th>237</th>
      <td>238</td>
      <td>male</td>
      <td>John</td>
      <td>Doe</td>
      <td>123 Main Street</td>
      <td>New York</td>
      <td>NY</td>
      <td>12345.0</td>
      <td>United States</td>
      <td>johndoe@email.com1234567890</td>
      <td>1/1/1975</td>
      <td>180.0</td>
      <td>72</td>
      <td>24.4</td>
    </tr>
    <tr>
      <th>242</th>
      <td>243</td>
      <td>male</td>
      <td>John</td>
      <td>O'Brian</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2/25/1957</td>
      <td>205.3</td>
      <td>74</td>
      <td>26.4</td>
    </tr>
    <tr>
      <th>244</th>
      <td>245</td>
      <td>male</td>
      <td>John</td>
      <td>Doe</td>
      <td>123 Main Street</td>
      <td>New York</td>
      <td>NY</td>
      <td>12345.0</td>
      <td>United States</td>
      <td>johndoe@email.com1234567890</td>
      <td>1/1/1975</td>
      <td>180.0</td>
      <td>72</td>
      <td>24.4</td>
    </tr>
    <tr>
      <th>249</th>
      <td>250</td>
      <td>male</td>
      <td>Benjamin</td>
      <td>Mehler</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10/30/1951</td>
      <td>146.5</td>
      <td>69</td>
      <td>21.6</td>
    </tr>
    <tr>
      <th>251</th>
      <td>252</td>
      <td>male</td>
      <td>John</td>
      <td>Doe</td>
      <td>123 Main Street</td>
      <td>New York</td>
      <td>NY</td>
      <td>12345.0</td>
      <td>United States</td>
      <td>johndoe@email.com1234567890</td>
      <td>1/1/1975</td>
      <td>180.0</td>
      <td>72</td>
      <td>24.4</td>
    </tr>
    <tr>
      <th>257</th>
      <td>258</td>
      <td>male</td>
      <td>Jin</td>
      <td>Kung</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/17/1995</td>
      <td>231.7</td>
      <td>69</td>
      <td>34.2</td>
    </tr>
    <tr>
      <th>264</th>
      <td>265</td>
      <td>female</td>
      <td>Wafiyyah</td>
      <td>Asfour</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>11/3/1989</td>
      <td>158.6</td>
      <td>63</td>
      <td>28.1</td>
    </tr>
    <tr>
      <th>269</th>
      <td>270</td>
      <td>female</td>
      <td>Flavia</td>
      <td>Fiorentino</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>10/9/1937</td>
      <td>175.2</td>
      <td>61</td>
      <td>33.1</td>
    </tr>
    <tr>
      <th>277</th>
      <td>278</td>
      <td>male</td>
      <td>John</td>
      <td>Doe</td>
      <td>123 Main Street</td>
      <td>New York</td>
      <td>NY</td>
      <td>12345.0</td>
      <td>United States</td>
      <td>johndoe@email.com1234567890</td>
      <td>1/1/1975</td>
      <td>180.0</td>
      <td>72</td>
      <td>24.4</td>
    </tr>
    <tr>
      <th>278</th>
      <td>279</td>
      <td>female</td>
      <td>Generosa</td>
      <td>Cabán</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>12/16/1962</td>
      <td>124.3</td>
      <td>69</td>
      <td>18.4</td>
    </tr>
    <tr>
      <th>282</th>
      <td>283</td>
      <td>female</td>
      <td>Sandy</td>
      <td>Taylor</td>
      <td>2476 Fulton Street</td>
      <td>Rainelle</td>
      <td>WV</td>
      <td>25962.0</td>
      <td>United States</td>
      <td>304-438-2648SandraCTaylor@dayrep.com</td>
      <td>10/23/1960</td>
      <td>206.1</td>
      <td>64</td>
      <td>35.4</td>
    </tr>
    <tr>
      <th>286</th>
      <td>287</td>
      <td>male</td>
      <td>Lewis</td>
      <td>Webb</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4/1/1979</td>
      <td>155.3</td>
      <td>68</td>
      <td>23.6</td>
    </tr>
    <tr>
      <th>296</th>
      <td>297</td>
      <td>female</td>
      <td>Chỉ</td>
      <td>Lâm</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>5/14/1990</td>
      <td>181.1</td>
      <td>63</td>
      <td>32.1</td>
    </tr>
    <tr>
      <th>502</th>
      <td>503</td>
      <td>male</td>
      <td>Pat</td>
      <td>Gersten</td>
      <td>2778 North Avenue</td>
      <td>Burr</td>
      <td>Nebraska</td>
      <td>68324.0</td>
      <td>United States</td>
      <td>PatrickGersten@rhyta.com402-848-4923</td>
      <td>5/3/1954</td>
      <td>138.2</td>
      <td>71</td>
      <td>19.3</td>
    </tr>
  </tbody>
</table>
</div>



BMI = kg/m<sup>2</sup>，其中kg是一个人的体重（千克），m<sup>2</sup>是他们的身高（米）。


```python
# 身高体重比例
weight_lbs =192.3
height_in = 72
703 * weight_lbs / (height_in*height_in)
```


    26.077719907407406



- ### treatments/治疗 表


```python
treatments.head()
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>given_name</th>
      <th>surname</th>
      <th>auralin</th>
      <th>novodra</th>
      <th>hba1c_start</th>
      <th>hba1c_end</th>
      <th>hba1c_change</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>veronika</td>
      <td>jindrová</td>
      <td>41u - 48u</td>
      <td>-</td>
      <td>7.63</td>
      <td>7.20</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>elliot</td>
      <td>richardson</td>
      <td>-</td>
      <td>40u - 45u</td>
      <td>7.56</td>
      <td>7.09</td>
      <td>0.97</td>
    </tr>
    <tr>
      <th>2</th>
      <td>yukitaka</td>
      <td>takenaka</td>
      <td>-</td>
      <td>39u - 36u</td>
      <td>7.68</td>
      <td>7.25</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>skye</td>
      <td>gormanston</td>
      <td>33u - 36u</td>
      <td>-</td>
      <td>7.97</td>
      <td>7.62</td>
      <td>0.35</td>
    </tr>
    <tr>
      <th>4</th>
      <td>alissa</td>
      <td>montez</td>
      <td>-</td>
      <td>33u - 29u</td>
      <td>7.78</td>
      <td>7.46</td>
      <td>0.32</td>
    </tr>
  </tbody>
</table>
</div>



`treatments` 列:
- **given_name**: 参与临床试验的主患者指数中每位患者的名字
- **surname**: 参与临床试验的主患者指数中每位患者的姓氏
- **auralin**: 在治疗的第24周测量的24周治疗结束时，转换为Auralin前一周的胰岛素基线中位日剂量（短跑前的数字）和胰岛素的终止中位日剂量（短划线后的数字）。两者都以单位（短'u'）来衡量，这是国际测量单位和胰岛素的标准测量。
- **novodra**: 与上述相同，但继续用Novodra治疗的患者除外
- **hba1c_start**: 患者在治疗第一周开始时的HbA1c水平。HbA1c代表血红蛋白A1c。该糖化血红蛋白测试测量的是平均血糖已经在过去的三个月。因此，它是一种了解糖尿病控制情况的有效方法。每个患有糖尿病的人都应该每年进行2到4次这种测试。以％计。
- **hba1c_end**: 治疗最后一周末患者的HbA1c水平
- **hba1c_change**: 患者HbA1c水平从治疗开始到结束的变化，即`hba1c_start` - `hba1c_end`。对于Auralin被认为有效，它必须与目前的胰岛素标准Novodra“非劣效”。这种“非劣效性”在统计学上定义为95％置信区间的上限小于0.4％，对于Novodra和Auralin（即Novodra减去Auralin）的平均HbA1c变化之间的差异。


```python
treatments.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 280 entries, 0 to 279
    Data columns (total 7 columns):
    given_name      280 non-null object
    surname         280 non-null object
    auralin         280 non-null object
    novodra         280 non-null object
    hba1c_start     280 non-null float64
    hba1c_end       280 non-null float64
    hba1c_change    171 non-null float64
    dtypes: float64(3), object(4)
    memory usage: 15.4+ KB



```python
treatments.describe()
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>hba1c_start</th>
      <th>hba1c_end</th>
      <th>hba1c_change</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>280.000000</td>
      <td>280.000000</td>
      <td>171.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>7.985929</td>
      <td>7.589286</td>
      <td>0.546023</td>
    </tr>
    <tr>
      <th>std</th>
      <td>0.568638</td>
      <td>0.569672</td>
      <td>0.279555</td>
    </tr>
    <tr>
      <th>min</th>
      <td>7.500000</td>
      <td>7.010000</td>
      <td>0.200000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>7.660000</td>
      <td>7.270000</td>
      <td>0.340000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>7.800000</td>
      <td>7.420000</td>
      <td>0.380000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>7.970000</td>
      <td>7.570000</td>
      <td>0.920000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>9.950000</td>
      <td>9.580000</td>
      <td>0.990000</td>
    </tr>
  </tbody>
</table>
</div>




```python
sum(treatments.auralin.isnull())
```


    0


```python
sum(treatments.novodra.isnull())
```


    0



- ### adverse_reactions/不良反应 表


```python
adverse_reactions.head()
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>given_name</th>
      <th>surname</th>
      <th>adverse_reaction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>berta</td>
      <td>napolitani</td>
      <td>injection site discomfort</td>
    </tr>
    <tr>
      <th>1</th>
      <td>lena</td>
      <td>baer</td>
      <td>hypoglycemia</td>
    </tr>
    <tr>
      <th>2</th>
      <td>joseph</td>
      <td>day</td>
      <td>hypoglycemia</td>
    </tr>
    <tr>
      <th>3</th>
      <td>flavia</td>
      <td>fiorentino</td>
      <td>cough</td>
    </tr>
    <tr>
      <th>4</th>
      <td>manouck</td>
      <td>wubbels</td>
      <td>throat irritation</td>
    </tr>
  </tbody>
</table>
</div>

`adverse_reactions` 列:

- **given_name**: 主要患者指数中参与临床试验且有不良反应的每位患者的名字（包括治疗Auralin和Novodra的患者）
- **surname**: 主患者指数中每位患者的姓氏，参与临床试验并有不良反应（包括治疗Auralin和Novodra的患者）
- **adverse_reaction**: 患者报告的不良反应

其他有用信息:
- 胰岛素抵抗因人而异，这就是为什么需要起始中位日剂量和终止中位日剂量，即计算剂量变化的原因。
- 在他们想要帮助的人中测试药物和医疗产品是很重要的。不同年龄，种族，性别和种族的人必须参加临床试验。这种多样性反映在`patients`表中。
- 确保列名称具有足够的描述性是熟悉数据的重要一步。“足够描述性”是主观的。理想情况下，您需要短列名称（因此它们更容易以代码形式键入和读取），但也需要完全描述性。长度与描述性是一种权衡和共同的争论（变量名称存在类似的争论）。该auralin和novodra列名也有可能不是足够的描述，但你会解决以后，所以不要担心现在。


```python
adverse_reactions.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 34 entries, 0 to 33
    Data columns (total 3 columns):
    given_name          34 non-null object
    surname             34 non-null object
    adverse_reaction    34 non-null object
    dtypes: object(3)
    memory usage: 896.0+ bytes

```python
adverse_reactions.describe()
```


<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>given_name</th>
      <th>surname</th>
      <th>adverse_reaction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>34</td>
      <td>34</td>
      <td>34</td>
    </tr>
    <tr>
      <th>unique</th>
      <td>34</td>
      <td>33</td>
      <td>6</td>
    </tr>
    <tr>
      <th>top</th>
      <td>jia li</td>
      <td>johnson</td>
      <td>hypoglycemia</td>
    </tr>
    <tr>
      <th>freq</th>
      <td>1</td>
      <td>2</td>
      <td>19</td>
    </tr>
  </tbody>
</table>
</div>




```python
all_colums = pd.Series(list(patients) + list(treatments) + list(adverse_reactions))
all_colums[all_colums.duplicated()]
```


    14    given_name
    15       surname
    21    given_name
    22       surname
    dtype: object



## Quality
####  *`treatment`表*
- 丢失数据(应该350名患者，实际280)
- 缺少hba1c_change
- 'auralin`和`novodra`列中的起始剂量和最终剂量旁边的'u'
- 小写名称
- 错误数据类型（zip_code, assigned sex, state, birthday）
- 错误的HbA1c change

#### `patients`表
- zip_code格式不佳（例如，四位数和浮点数据类型而不是五位数字和字符串或对象数据类型）
- 患者身高值不正确（例如，Tim Neudorf身高27英寸而不是72英寸）
- state不一致(有时是完整的州名，有时是缩写)
- Dsvid 拼写错误，应该是David
- 错误数据类型（auranlin and novodra colums）
- 话号码格式不一致
- 有不可恢复的无名氏记录
- Jakobsen, Gersten, Taylor有多项记录
- Zaitseva的重量是“kgs”而不是“lbs”（磅）

#### `adverse_reactions`表
- 小写名称

### 数据质量维度
数据质量维度有助于在评估和清洁时指导您的思维过程。四个主要数据质量维度是：

- **Completeness 完整性**：我们是否拥有所有应该记录的记录？我们是否缺少记录？是否缺少特定的行，列或单元格？
- **Validity 有效性**：我们有记录，但它们无效，即它们不符合已定义的模式。模式是一组定义的数据规则。这些规则可以是现实世界的约束（例如，负高度是不可能的）和特定于表的约束（例如表中的唯一键约束）。
- **Accuracy 准确性**：不准确的数据是有效的错误数据。它遵循已定义的架构，但仍然不正确。例如：患者的体重因重量过大而重5磅太重。
- **Consistency 一致性**：不一致的数据既有效又准确，但有多种正确的方法可以引用相同的内容。期望在表中和/或表内表示相同数据的列中的一致性，即标准格式。

### Tidiness
- 联系人列表中，电话和邮箱应该分为两列
- 治疗表中，将auranlin and novodra 分为三个变量（treatment, start does and end does）

### 脏数据的来源
**脏数据=低质量数据=内容问题**

脏数据有很多来源。基本上，只要人类参与其中，就会有脏数据。我们有很多方法可以处理我们使用的数据。

- 我们将有用户输入错误。
- 在某些情况下，我们不会有任何数据编码标准，或者我们确实有标准，它们将很难应用，导致结果数据出现问题
- 我们可能必须集成数据，其中不同的模式已用于相同类型的项目。
- 我们将拥有遗留数据系统，当光盘和内存限制比现在更严格时，数据不会被编码。随着时间的推移系统发展。需求变化，数据变化。
- 我们的一些数据不具备应有的唯一标识符。
- 在从一种格式到另一种格式的转换中，其他数据将丢失。
- 然后，当然，程序员总是会出错。
- 最后，数据可能在宇宙射线或其他物理现象的传输或存储中被破坏。嘿，这不是我们的错。

### 凌乱数据的来源
- **凌乱的数据=不整洁的数据=结构问题**

- **凌乱的数据通常是数据规划不佳的结果**。或者缺乏对整洁数据的好处的认识。幸运的是，凌乱的数据通常比上面提到的大多数脏数据源更容易解决。

>[Jupyter Notebook](https://nbviewer.jupyter.org/github/hufe09/pydata_practice/blob/master/data_wrangling/Assess/Assessing.ipynb)