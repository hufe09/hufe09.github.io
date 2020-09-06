---
layout: post
title: "「数据争论」Module 02: Gathering Data"
subtitle: '使用Python从各种来源和各种文件格式中收集数据'
author: "Hufe"
header-img: "img/post-bg-datas.jpg"
header-mask: 0.3
mathjax: true
tags:

  - Python
  - Data Analysis
  - Data Wrangling
---


## 来源：本地文件
### 平面文件

平面文件包含纯文本格式的表格数据，每行一个数据记录，每个记录或行包含一个或多个字段。这些字段由分隔符分隔，如逗号，制表符或冒号。

**平面文件的优点**包括：

- 它们是文本文件，因此是人类可读的。
- 轻巧。
- 简单易懂。
- 可以读/写文本文件的软件无处不在，就像文本编辑器一样。
- 非常适合小型数据集。

例如，与关系数据库相比，**平面文件的缺点**包括：

- 缺乏标准。
- 数据冗余。
- 共享数据可能很麻烦。
- 对于大型数据集来说并不是很好。

### 寻找最好的电影
- 烂番茄最受欢迎的100部电影TSV文件


```python
import pandas as pd
```


```python
df_bestofrt = pd.read_csv('bestofrt.tsv', sep='\t')
```


```python
df_bestofrt.head()
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
      <th>ranking</th>
      <th>critic_score</th>
      <th>title</th>
      <th>number_of_critic_ratings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>99</td>
      <td>The Wizard of Oz (1939)</td>
      <td>110</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>100</td>
      <td>Citizen Kane (1941)</td>
      <td>75</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>100</td>
      <td>The Third Man (1949)</td>
      <td>77</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>99</td>
      <td>Get Out (2017)</td>
      <td>282</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>97</td>
      <td>Mad Max: Fury Road (2015)</td>
      <td>370</td>
    </tr>
  </tbody>
</table>
</div>




```python
df_bestofrt.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 100 entries, 0 to 99
    Data columns (total 4 columns):
    ranking                     100 non-null int64
    critic_score                100 non-null int64
    title                       100 non-null object
    number_of_critic_ratings    100 non-null int64
    dtypes: int64(3), object(1)
    memory usage: 3.2+ KB



```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.scatter(df_bestofrt.number_of_critic_ratings, df_bestofrt.critic_score)
```


    <matplotlib.collections.PathCollection at 0x7fc395438a20>




![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.eukxdddmbnm.png)

### Python解析HTML文件


```python
import zipfile
with zipfile.ZipFile('rt-html.zip', 'r') as myzip:
    myzip.extractall()
```


```python
import os
from bs4 import BeautifulSoup
df_list = []
folder = 'rt_html'
for movie_html in os.listdir(folder):
    with open(os.path.join(folder, movie_html) ,encoding='utf-8') as file:
        soup = BeautifulSoup(file, 'lxml')
        title = soup.find('title').contents[0][:-len(' - Rotten Tomatoes')]
        audience_score = soup.find('div', class_='audience-score meter').find('span').contents[0][:-1]
        num_audience_ratings = soup.find('div', class_='audience-info hidden-xs superPageFontColor')
        num_audience_ratings = num_audience_ratings.find_all('div')[1].contents[2].strip().replace(',','')
        df_list.append({'title': title,
                       'audience_score': int(audience_score),
                       'number_of_audience_ratings': int(num_audience_ratings)})
df_html = pd.DataFrame(df_list, columns=['title', 'audience_score', 'number_of_audience_ratings'])
df_html
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
      <th>title</th>
      <th>audience_score</th>
      <th>number_of_audience_ratings</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The Grapes of Wrath (1940)</td>
      <td>88</td>
      <td>23954</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Gravity (2013)</td>
      <td>80</td>
      <td>301261</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Rear Window (1954)</td>
      <td>95</td>
      <td>149458</td>
    </tr>
    <tr>
      <th>3</th>
      <td>It Happened One Night (1934)</td>
      <td>93</td>
      <td>33106</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Finding Nemo (2003)</td>
      <td>86</td>
      <td>33355673</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Selma (2015)</td>
      <td>86</td>
      <td>60533</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Up (2009)</td>
      <td>90</td>
      <td>1201878</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Nosferatu, a Symphony of Horror (Nosferatu, ei...</td>
      <td>87</td>
      <td>47463</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Metropolis (1927)</td>
      <td>92</td>
      <td>62018</td>
    </tr>
    <tr>
      <th>9</th>
      <td>The 400 Blows (Les Quatre cents coups) (1959)</td>
      <td>94</td>
      <td>38368</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Vertigo (1958)</td>
      <td>93</td>
      <td>101454</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Jaws (1975)</td>
      <td>90</td>
      <td>942217</td>
    </tr>
    <tr>
      <th>12</th>
      <td>The Wrestler (2008)</td>
      <td>88</td>
      <td>139795</td>
    </tr>
    <tr>
      <th>13</th>
      <td>M (1931)</td>
      <td>95</td>
      <td>35778</td>
    </tr>
    <tr>
      <th>14</th>
      <td>The 39 Steps (1935)</td>
      <td>86</td>
      <td>23647</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Get Out (2017)</td>
      <td>87</td>
      <td>63837</td>
    </tr>
    <tr>
      <th>16</th>
      <td>King Kong (1933)</td>
      <td>86</td>
      <td>89669</td>
    </tr>
    <tr>
      <th>17</th>
      <td>The Battle of Algiers (La Battaglia di Algeri)...</td>
      <td>95</td>
      <td>14267</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Casablanca (1942)</td>
      <td>95</td>
      <td>355952</td>
    </tr>
    <tr>
      <th>19</th>
      <td>Spotlight (2015)</td>
      <td>93</td>
      <td>68142</td>
    </tr>
    <tr>
      <th>20</th>
      <td>The Adventures of Robin Hood (1938)</td>
      <td>89</td>
      <td>33584</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Brooklyn (2015)</td>
      <td>87</td>
      <td>45763</td>
    </tr>
    <tr>
      <th>22</th>
      <td>12 Angry Men (Twelve Angry Men) (1957)</td>
      <td>97</td>
      <td>103672</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Man on Wire (2008)</td>
      <td>87</td>
      <td>29827</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Dunkirk (2017)</td>
      <td>82</td>
      <td>53555</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Pinocchio (1940)</td>
      <td>72</td>
      <td>278682</td>
    </tr>
    <tr>
      <th>26</th>
      <td>On the Waterfront (1954)</td>
      <td>95</td>
      <td>51664</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Toy Story (1995)</td>
      <td>92</td>
      <td>1107731</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Repulsion (1965)</td>
      <td>87</td>
      <td>18655</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Open City (1946)</td>
      <td>92</td>
      <td>6128</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>A Streetcar Named Desire (1951)</td>
      <td>90</td>
      <td>54761</td>
    </tr>
    <tr>
      <th>71</th>
      <td>Citizen Kane (1941)</td>
      <td>90</td>
      <td>157274</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Baby Driver (2017)</td>
      <td>89</td>
      <td>48114</td>
    </tr>
    <tr>
      <th>73</th>
      <td>Arrival (2016)</td>
      <td>82</td>
      <td>78740</td>
    </tr>
    <tr>
      <th>74</th>
      <td>Dr. Strangelove Or How I Learned to Stop Worry...</td>
      <td>94</td>
      <td>208215</td>
    </tr>
    <tr>
      <th>75</th>
      <td>Logan (2017)</td>
      <td>91</td>
      <td>86540</td>
    </tr>
    <tr>
      <th>76</th>
      <td>Inside Out (2015)</td>
      <td>89</td>
      <td>133558</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Seven Samurai (Shichinin no Samurai) (1956)</td>
      <td>97</td>
      <td>90049</td>
    </tr>
    <tr>
      <th>78</th>
      <td>Mad Max: Fury Road (2015)</td>
      <td>86</td>
      <td>123937</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Apocalypse Now (1979)</td>
      <td>94</td>
      <td>284606</td>
    </tr>
    <tr>
      <th>80</th>
      <td>Rashômon (1951)</td>
      <td>93</td>
      <td>47657</td>
    </tr>
    <tr>
      <th>81</th>
      <td>Modern Times (1936)</td>
      <td>95</td>
      <td>39736</td>
    </tr>
    <tr>
      <th>82</th>
      <td>The Treasure of the Sierra Madre (1948)</td>
      <td>93</td>
      <td>25627</td>
    </tr>
    <tr>
      <th>83</th>
      <td>The Babadook (2014)</td>
      <td>72</td>
      <td>37024</td>
    </tr>
    <tr>
      <th>84</th>
      <td>The Godfather, Part II (1974)</td>
      <td>97</td>
      <td>409574</td>
    </tr>
    <tr>
      <th>85</th>
      <td>Snow White and the Seven Dwarfs (1937)</td>
      <td>78</td>
      <td>469510</td>
    </tr>
    <tr>
      <th>86</th>
      <td>The Third Man (1949)</td>
      <td>93</td>
      <td>53081</td>
    </tr>
    <tr>
      <th>87</th>
      <td>Alien (1979)</td>
      <td>94</td>
      <td>457186</td>
    </tr>
    <tr>
      <th>88</th>
      <td>North by Northwest (1959)</td>
      <td>94</td>
      <td>78793</td>
    </tr>
    <tr>
      <th>89</th>
      <td>Zootopia (2016)</td>
      <td>92</td>
      <td>98633</td>
    </tr>
    <tr>
      <th>90</th>
      <td>The Cabinet of Dr. Caligari (Das Cabinet des D...</td>
      <td>89</td>
      <td>27163</td>
    </tr>
    <tr>
      <th>91</th>
      <td>A Hard Day's Night (1964)</td>
      <td>89</td>
      <td>50067</td>
    </tr>
    <tr>
      <th>92</th>
      <td>Star Trek (2009)</td>
      <td>91</td>
      <td>746458</td>
    </tr>
    <tr>
      <th>93</th>
      <td>Touch of Evil (1958)</td>
      <td>92</td>
      <td>30867</td>
    </tr>
    <tr>
      <th>94</th>
      <td>Wonder Woman (2017)</td>
      <td>90</td>
      <td>112955</td>
    </tr>
    <tr>
      <th>95</th>
      <td>The Conformist (1970)</td>
      <td>91</td>
      <td>8529</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Army of Shadows (L'Armée des ombres) (1969)</td>
      <td>94</td>
      <td>7011</td>
    </tr>
    <tr>
      <th>97</th>
      <td>Rosemary's Baby (1968)</td>
      <td>87</td>
      <td>72669</td>
    </tr>
    <tr>
      <th>98</th>
      <td>E.T. The Extra-Terrestrial (1982)</td>
      <td>72</td>
      <td>32313030</td>
    </tr>
    <tr>
      <th>99</th>
      <td>L.A. Confidential (1997)</td>
      <td>94</td>
      <td>149772</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 3 columns</p>
</div>




```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.scatter(df_html.audience_score, df_bestofrt.critic_score)
```


    <matplotlib.collections.PathCollection at 0x7fc390c14240>




![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.42dyimmm43v.png)


```python
with open('rt_html/et_the_extraterrestrial.html') as file:
    soup = BeautifulSoup(file, 'lxml')
```


```python
soup.find('title')
```


    <title>E.T. The Extra-Terrestrial (1982) - Rotten Tomatoes</title>




```python
soup.find('title').contents[0]
```


    'E.T. The Extra-Terrestrial\xa0(1982) - Rotten Tomatoes'




```python
soup.find('title').contents[0][:-len(' - Rotten Tomatoes')]
```


    'E.T. The Extra-Terrestrial\xa0(1982)'




```python
soup.find('title').contents[0][:-18]
```


    'E.T. The Extra-Terrestrial\xa0(1982)'




```python
soup.find(attrs={"class": "audience-score meter"})
```


    <div class="audience-score meter">
    <a class="unstyled articleLink" href="#audience_reviews"><div class="meter media">
    <div class="meter-tomato icon big medium-xs upright pull-left"></div>
    <div class="media-body" style="line-height:36px">
    <div class="meter-value">
    <span class="superPageFontColor" style="vertical-align:top">72%</span>
    </div>
    <div class="smaller bold hidden-xs superPageFontColor" style="padding-left:5px;line-height:12px">liked it</div>
    </div>
    </div>
    </a></div>




```python
soup.find('div', class_='audience-score meter').find('span').contents[0][:-1]
```


    '72'




```python
num_audience_ratings = soup.find("span", string="User Ratings:").parent.contents[2].strip()
num_audience_ratings = int(num_audience_ratings.replace(',', ''))
num_audience_ratings
```


    32313030



### 爬取在线网页数据
最近，烂番茄电影页面html结构有所改变，现在尝试在线解析。


```python
import requests
```


```python
url = 'https://www.rottentomatoes.com/m/et_the_extraterrestrial'
response = requests.get(url)
```


```python
from bs4 import BeautifulSoup
soup = BeautifulSoup(response.content, 'lxml')
```


```python
# soup
```


```python
soup.find('title').contents
```


    ['E.T. The Extra-Terrestrial (1982) - Rotten Tomatoes']




```python
soup.find('title').contents[0][:-len(' - Rotten Tomatoes')]
```


    'E.T. The Extra-Terrestrial (1982)'




```python
len(' - Rotten Tomatoes')
```


    18




```python
soup.find('title')
```


    <title>E.T. The Extra-Terrestrial (1982) - Rotten Tomatoes</title>




```python
soup.findAll('h2', class_= 'mop-ratings-wrap__score')
```


    [<h2 class="mop-ratings-wrap__score">
     <a class="unstyled articleLink mop-ratings-wrap__icon-link" href="#contentReviews" id="tomato_meter_link">
     <span class="mop-ratings-wrap__icon meter-tomato icon big medium-xs certified_fresh"></span>
     <span class="mop-ratings-wrap__percentage">
                         98%
                     </span>
     </a>
     </h2>, <h2 class="mop-ratings-wrap__score">
     <a class="unstyled articleLink mop-ratings-wrap__icon-link" href="#audience_reviews">
     <span class="mop-ratings-wrap__icon meter-tomato icon big medium-xs upright"></span>
     <span class="mop-ratings-wrap__percentage">
                         72%
                     </span>
     </a>
     </h2>]




```python
soup.find('span', class_= 'mop-ratings-wrap__percentage').contents
```


    ['\n                    98%\n                ']




```python
soup.find('span', class_= 'mop-ratings-wrap__percentage').contents[0].strip()[:-1]
```


    '98'




```python
soup.findAll('span', class_= 'mop-ratings-wrap__percentage')[1].contents
```


    ['\n                    72%\n                ']




```python
soup.findAll('span', class_= 'mop-ratings-wrap__percentage')[1].contents[0].strip()[:-1]
```


    '72'




```python
num_audience_ratings = soup.findAll("strong", class_= 'mop-ratings-wrap__text--small')[1].contents[0].strip()
num_audience_ratings = num_audience_ratings.split(":")
num_audience_ratings = int(num_audience_ratings[1].replace(',', ''))
num_audience_ratings
```


    32314349



## 来源：从Internet下载文件

### HTTP（超文本传输协议）

HTTP，即超文本传输协议，是Web浏览器（如Chrome或Safari）和Web服务器（基本上是存储网站内容的计算机）相互通信的语言。每次打开网页，下载文件或观看视频时，都可以使用HTTP。

HTTP是请求/响应协议：

- 计算机（即客户端）向服务器发送请求以获取某个文件。例如下面展示的过程：*“让我获取文件 **1-the-wizard-of-oz-1939-film.txt** ”*。
    GET是用于检索数据的HTTP请求方法（其中有多个）的名称。
- Web服务器发回响应。如果请求有效：*“这是您要求的文件：”*，然后返回**1-the-wizard-of-oz-1939-film.txt**文件本身的内容。

![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.zdbpufqrdul.png)


```python
import requests 
import pandas as pd
import os
```


```python
folder_name_example = 'folder_name_example'
if not os.path.exists(folder_name_example):
    os.makedirs(folder_name_example)
```


```python
urls = ['https://raw.githubusercontent.com/hufe09/data-analyst-nanodegree-lab/master/Gathering%20Data/ebert_reviews/1-the-wizard-of-oz-1939-film.txt',
        'https://raw.githubusercontent.com/hufe09/data-analyst-nanodegree-lab/master/Gathering%20Data/ebert_reviews/2-citizen-kane.txt']
for url in urls:
    response = requests.get(url)
    with open(os.path.join(folder_name_example, url.split('/')[-1]), mode = 'wb') as file:
        file.write(response.content)
```


```python
os.listdir(folder_name_example)
```


    ['2-citizen-kane.txt', '1-the-wizard-of-oz-1939-film.txt']



### 下载影评


```python
film_review_urls = ['https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9900_1-the-wizard-of-oz-1939-film/1-the-wizard-of-oz-1939-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9901_2-citizen-kane/2-citizen-kane.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9901_3-the-third-man/3-the-third-man.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9902_4-get-out-film/4-get-out-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9902_5-mad-max-fury-road/5-mad-max-fury-road.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9902_6-the-cabinet-of-dr.-caligari/6-the-cabinet-of-dr.-caligari.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9903_7-all-about-eve/7-all-about-eve.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9903_8-inside-out-2015-film/8-inside-out-2015-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9903_9-the-godfather/9-the-godfather.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9904_10-metropolis-1927-film/10-metropolis-1927-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9904_11-e.t.-the-extra-terrestrial/11-e.t.-the-extra-terrestrial.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9904_12-modern-times-film/12-modern-times-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9904_14-singin-in-the-rain/14-singin-in-the-rain.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9905_15-boyhood-film/15-boyhood-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9905_16-casablanca-film/16-casablanca-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9905_17-moonlight-2016-film/17-moonlight-2016-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9906_18-psycho-1960-film/18-psycho-1960-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9906_19-laura-1944-film/19-laura-1944-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9906_20-nosferatu/20-nosferatu.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9907_21-snow-white-and-the-seven-dwarfs-1937-film/21-snow-white-and-the-seven-dwarfs-1937-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9907_22-a-hard-day27s-night-film/22-a-hard-day27s-night-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9907_23-la-grande-illusion/23-la-grande-illusion.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9908_25-the-battle-of-algiers/25-the-battle-of-algiers.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9908_26-dunkirk-2017-film/26-dunkirk-2017-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9908_27-the-maltese-falcon-1941-film/27-the-maltese-falcon-1941-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9909_29-12-years-a-slave-film/29-12-years-a-slave-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9909_30-gravity-2013-film/30-gravity-2013-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9909_31-sunset-boulevard-film/31-sunset-boulevard-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990a_32-king-kong-1933-film/32-king-kong-1933-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990a_33-spotlight-film/33-spotlight-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990a_34-the-adventures-of-robin-hood/34-the-adventures-of-robin-hood.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990b_35-rashomon/35-rashomon.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990b_36-rear-window/36-rear-window.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990b_37-selma-film/37-selma-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990c_38-taxi-driver/38-taxi-driver.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990c_39-toy-story-3/39-toy-story-3.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990c_40-argo-2012-film/40-argo-2012-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990d_41-toy-story-2/41-toy-story-2.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990d_42-the-big-sick/42-the-big-sick.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990d_43-bride-of-frankenstein/43-bride-of-frankenstein.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990d_44-zootopia/44-zootopia.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990e_45-m-1931-film/45-m-1931-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990e_46-wonder-woman-2017-film/46-wonder-woman-2017-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990e_48-alien-film/48-alien-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990f_49-bicycle-thieves/49-bicycle-thieves.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990f_50-seven-samurai/50-seven-samurai.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad990f_51-the-treasure-of-the-sierra-madre-film/51-the-treasure-of-the-sierra-madre-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9910_52-up-2009-film/52-up-2009-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9910_53-12-angry-men-1957-film/53-12-angry-men-1957-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9910_54-the-400-blows/54-the-400-blows.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9911_55-logan-film/55-logan-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9911_57-army-of-shadows/57-army-of-shadows.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9912_58-arrival-film/58-arrival-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9912_59-baby-driver/59-baby-driver.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9913_60-a-streetcar-named-desire-1951-film/60-a-streetcar-named-desire-1951-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9913_61-the-night-of-the-hunter-film/61-the-night-of-the-hunter-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9913_62-star-wars-the-force-awakens/62-star-wars-the-force-awakens.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9913_63-manchester-by-the-sea-film/63-manchester-by-the-sea-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9914_64-dr.-strangelove/64-dr.-strangelove.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9914_66-vertigo-film/66-vertigo-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9914_67-the-dark-knight-film/67-the-dark-knight-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9915_68-touch-of-evil/68-touch-of-evil.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9915_69-the-babadook/69-the-babadook.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9915_72-rosemary27s-baby-film/72-rosemary27s-baby-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9916_73-finding-nemo/73-finding-nemo.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9916_74-brooklyn-film/74-brooklyn-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9917_75-the-wrestler-2008-film/75-the-wrestler-2008-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9917_77-l.a.-confidential-film/77-l.a.-confidential-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9918_78-gone-with-the-wind-film/78-gone-with-the-wind-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9918_79-the-good-the-bad-and-the-ugly/79-the-good-the-bad-and-the-ugly.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9918_80-skyfall/80-skyfall.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9919_82-tokyo-story/82-tokyo-story.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9919_83-hell-or-high-water-film/83-hell-or-high-water-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9919_84-pinocchio-1940-film/84-pinocchio-1940-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad9919_85-the-jungle-book-2016-film/85-the-jungle-book-2016-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991a_86-la-la-land-film/86-la-la-land-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991b_87-star-trek-film/87-star-trek-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991b_89-apocalypse-now/89-apocalypse-now.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991c_90-on-the-waterfront/90-on-the-waterfront.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991c_91-the-wages-of-fear/91-the-wages-of-fear.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991c_92-the-last-picture-show/92-the-last-picture-show.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991d_93-harry-potter-and-the-deathly-hallows-part-2/93-harry-potter-and-the-deathly-hallows-part-2.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991d_94-the-grapes-of-wrath-film/94-the-grapes-of-wrath-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991d_96-man-on-wire/96-man-on-wire.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991e_97-jaws-film/97-jaws-film.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991e_98-toy-story/98-toy-story.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991e_99-the-godfather-part-ii/99-the-godfather-part-ii.txt',
                     'https://d17h27t6h515a5.cloudfront.net/topher/2017/September/59ad991e_100-battleship-potemkin/100-battleship-potemkin.txt']
```

``` Python
# Make directory if it doesn't already exist
folder_name = 'film_reviews'
if not os.path.exists(folder_name):
    os.makedirs(folder_name)
    
for url in film_review_urls:
    response = requests.get(url)
    with open(os.path.join(folder_name, url.split('/')[-1]), mode='wb') as file:
        file.write(response.content)
```

要打开并读取文档，我们需要循环迭代此文件夹中的所有文件，此处有两种方法可用，一种是通过OS库完成另一种是使用名为glob的库完成
- glob库允许Unix风格的路径名模式扩展

`glob.glob()` 它会返回一个与pathname匹配的路径名称

`*` 指匹配任何长度的字符串


```python
import glob
```


```python
folder_name = 'film_reviews'
df_list = []
for film_review in glob.glob('film_reviews/*.txt'):
    with open(film_review, encoding='utf-8') as file:
        title = file.readline()[:-1]
        review_url = file.readline()[:-1]
        review_text = file.read()
        df_list.append({'title': title,
                       'review_url': review_url,
                       'review_text': review_text})
```


```python
df = pd.DataFrame(df_list, columns=['title', 'review_url', 'review_text'])
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
      <th>title</th>
      <th>review_url</th>
      <th>review_text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The Maltese Falcon (1941)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Among the movies we not only love but treasure...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Up (2009)</td>
      <td>http://www.rogerebert.com/reviews/up-2009</td>
      <td>"Up" is a wonderful film, with characters who ...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Taxi Driver (1976)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Are you talkin' to me? Well, I'm the only one ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>The Wages of Fear (1953)</td>
      <td>http://www.rogerebert.com/reviews/the-wages-of...</td>
      <td>When the great French thriller "The Wages of F...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sunset Boulevard (1950)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Billy Wilder's "Sunset Boulevard” is the portr...</td>
    </tr>
    <tr>
      <th>5</th>
      <td>The Battle of Algiers (La Battaglia di Algeri)...</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>The most common form of warfare since 1945 has...</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Bicycle Thieves (Ladri di biciclette) (1949)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>"The Bicycle Thief" is so well-entrenched as a...</td>
    </tr>
    <tr>
      <th>7</th>
      <td>The Last Picture Show (1971)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>The best scene in "The Last Picture Show" take...</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Toy Story (1995)</td>
      <td>http://www.rogerebert.com/reviews/toy-story-1995</td>
      <td>"Toy Story" creates a universe out of a couple...</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Tokyo Story (Tôkyô monogatari) (1953)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>No story could be simpler. An old couple come ...</td>
    </tr>
    <tr>
      <th>10</th>
      <td>The Bride of Frankenstein (1935)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>To a new world of gods and monsters.\n\nSo int...</td>
    </tr>
    <tr>
      <th>11</th>
      <td>King Kong (1933)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>On good days I consider "Citizen Kane" the sem...</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Star Wars: Episode VII - The Force Awakens (2015)</td>
      <td>http://www.rogerebert.com/reviews/star-wars-ep...</td>
      <td>“Star Wars: Episode VII - The Force Awakens” i...</td>
    </tr>
    <tr>
      <th>13</th>
      <td>The 400 Blows (Les Quatre cents coups) (1959)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>I demand that a film express either the joy of...</td>
    </tr>
    <tr>
      <th>14</th>
      <td>Laura (1944)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>I've seen Otto Preminger's “Laura” three or fo...</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Casablanca (1942)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>If we identify strongly with the characters in...</td>
    </tr>
    <tr>
      <th>16</th>
      <td>Rosemary's Baby (1968)</td>
      <td>http://www.rogerebert.com/reviews/rosemarys-ba...</td>
      <td>Roman Polanski's "Rosemary's Baby" is a broodi...</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Get Out (2017)</td>
      <td>http://www.rogerebert.com/reviews/get-out-2017</td>
      <td>With the ambitious and challenging “Get Out,” ...</td>
    </tr>
    <tr>
      <th>18</th>
      <td>Metropolis (1927)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>The opening shots of the restored “Metropolis”...</td>
    </tr>
    <tr>
      <th>19</th>
      <td>On the Waterfront (1954)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Conscience. That stuff can drive you nuts.\n\n...</td>
    </tr>
    <tr>
      <th>20</th>
      <td>The Big Sick (2017)</td>
      <td>http://www.rogerebert.com/reviews/the-big-sick...</td>
      <td>It sounds impossible—too melodramatic, too cra...</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Toy Story 3 (2010)</td>
      <td>http://www.rogerebert.com/reviews/toy-story-3-...</td>
      <td>The first two "Toy Story" movies centered on t...</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Snow White and the Seven Dwarfs (1937)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>If Walt Disney's “Snow White and the Seven Dwa...</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Alien (1979)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>At its most fundamental level, "Alien" is a mo...</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Seven Samurai (Shichinin no Samurai) (1956)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Akira Kurosawa's "Seven Samurai" (1954) is not...</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Battleship Potemkin (1925)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>"The Battleship Potemkin” has been so famous f...</td>
    </tr>
    <tr>
      <th>26</th>
      <td>La La Land (2016)</td>
      <td>http://www.rogerebert.com/reviews/la-la-land-2016</td>
      <td>Musicals made me a romantic. They taught me th...</td>
    </tr>
    <tr>
      <th>27</th>
      <td>Gone With the Wind (1939)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>"Gone With the Wind” presents a sentimental vi...</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Vertigo (1958)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>“Did he train you? Did he rehearse you? Did he...</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Man on Wire (2008)</td>
      <td>http://www.rogerebert.com/reviews/man-on-wire-...</td>
      <td>I am afraid of heights. Now you know. That is ...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>58</th>
      <td>12 Angry Men (Twelve Angry Men) (1957)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>In form, "12 Angry Men" is a courtroom drama. ...</td>
    </tr>
    <tr>
      <th>59</th>
      <td>Nosferatu, a Symphony of Horror (Nosferatu, ei...</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>To watch F.W. Murnau's "Nosferatu" (1922) is t...</td>
    </tr>
    <tr>
      <th>60</th>
      <td>The Jungle Book (2016)</td>
      <td>http://www.rogerebert.com/reviews/the-jungle-b...</td>
      <td>I saw the newest Disney version of "The Jungle...</td>
    </tr>
    <tr>
      <th>61</th>
      <td>Hell or High Water (2016)</td>
      <td>http://www.rogerebert.com/reviews/hell-or-high...</td>
      <td>After a summer filled with retreads, ripoffs a...</td>
    </tr>
    <tr>
      <th>62</th>
      <td>Spotlight (2015)</td>
      <td>http://www.rogerebert.com/reviews/spotlight-2015</td>
      <td>On January 6, 2002, Boston Globe subscribers p...</td>
    </tr>
    <tr>
      <th>63</th>
      <td>Singin' in the Rain (1952)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>There is no movie musical more fun than "Singi...</td>
    </tr>
    <tr>
      <th>64</th>
      <td>Citizen Kane (1941)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>“I don't think any word can explain a man's li...</td>
    </tr>
    <tr>
      <th>65</th>
      <td>Gravity (2013)</td>
      <td>http://www.rogerebert.com/reviews/gravity-2013</td>
      <td>Alfonso Cuarón's "Gravity," about astronauts c...</td>
    </tr>
    <tr>
      <th>66</th>
      <td>The Dark Knight (2008)</td>
      <td>http://www.rogerebert.com/reviews/the-dark-kni...</td>
      <td>“Batman” isn’t a comic book anymore. Christoph...</td>
    </tr>
    <tr>
      <th>67</th>
      <td>The Wrestler (2008)</td>
      <td>http://www.rogerebert.com/reviews/the-wrestler...</td>
      <td>"The Wrestler" is about a man who can do one t...</td>
    </tr>
    <tr>
      <th>68</th>
      <td>A Streetcar Named Desire (1951)</td>
      <td>http://www.rogerebert.com/reviews/a-streetcar-...</td>
      <td>Marlon Brando didn't win the Academy Award in ...</td>
    </tr>
    <tr>
      <th>69</th>
      <td>Modern Times (1936)</td>
      <td>http://www.rogerebert.com/reviews/modern-times...</td>
      <td>A lot of movies are said to be timeless, but s...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>The Cabinet of Dr. Caligari (Das Cabinet des D...</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>The first thing everyone notices and best reme...</td>
    </tr>
    <tr>
      <th>71</th>
      <td>The Wizard of Oz (1939)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>As a child I simply did not notice whether a m...</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Manchester by the Sea (2016)</td>
      <td>http://www.rogerebert.com/reviews/manchester-b...</td>
      <td>"Manchester by the Sea," about a self-punishin...</td>
    </tr>
    <tr>
      <th>73</th>
      <td>Rear Window (1954)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>The hero of Alfred Hitchcock's "Rear Window" i...</td>
    </tr>
    <tr>
      <th>74</th>
      <td>Logan (2017)</td>
      <td>http://www.rogerebert.com/reviews/logan-2017</td>
      <td>Is “Logan” more powerful because of what the s...</td>
    </tr>
    <tr>
      <th>75</th>
      <td>RashÃ´mon (1951)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Shortly before filming was to begin on "Rashom...</td>
    </tr>
    <tr>
      <th>76</th>
      <td>La Grande illusion (Grand Illusion) (1938)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Apart from its other achievements, Jean Renoir...</td>
    </tr>
    <tr>
      <th>77</th>
      <td>All About Eve (1950)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Growing older was a smart career move for Bett...</td>
    </tr>
    <tr>
      <th>78</th>
      <td>Argo (2012)</td>
      <td>http://www.rogerebert.com/reviews/argo-2012</td>
      <td>It's the same the world over. A Hollywood prod...</td>
    </tr>
    <tr>
      <th>79</th>
      <td>Touch of Evil (1958)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Come on, read my future for me.You haven't got...</td>
    </tr>
    <tr>
      <th>80</th>
      <td>Harry Potter and the Deathly Hallows - Part 2 ...</td>
      <td>http://www.rogerebert.com/reviews/harry-potter...</td>
      <td>After seven earlier films reaching back a deca...</td>
    </tr>
    <tr>
      <th>81</th>
      <td>Moonlight (2016)</td>
      <td>http://www.rogerebert.com/reviews/moonlight-2016</td>
      <td>“Who is you, man?” Dramatic film has long been...</td>
    </tr>
    <tr>
      <th>82</th>
      <td>Wonder Woman (2017)</td>
      <td>http://www.rogerebert.com/reviews/wonder-woman...</td>
      <td>Ever since William Moulton Marston created her...</td>
    </tr>
    <tr>
      <th>83</th>
      <td>A Hard Day's Night (1964)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>When it opened in September, 1964, "A Hard Day...</td>
    </tr>
    <tr>
      <th>84</th>
      <td>The Night of the Hunter (1955)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>Charles Laughton's "The Night of the Hunter” (...</td>
    </tr>
    <tr>
      <th>85</th>
      <td>The Adventures of Robin Hood (1938)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>"The Adventures of Robin Hood" was made with s...</td>
    </tr>
    <tr>
      <th>86</th>
      <td>Inside Out (2015)</td>
      <td>http://www.rogerebert.com/reviews/inside-out-2015</td>
      <td>"Inside Out," a comedy-adventure set inside th...</td>
    </tr>
    <tr>
      <th>87</th>
      <td>L.A. Confidential (1997)</td>
      <td>http://www.rogerebert.com/reviews/great-movie-...</td>
      <td>"L.A. Confidential" finished at No. 1 in a lis...</td>
    </tr>
  </tbody>
</table>
<p>88 rows × 3 columns</p>
</div>




```python
title_list = []
for t in df.title:
    title_list.append(t)
len(title_list)
```


    88



### WordCloud


```python
toy_story_review = './film_reviews/98-toy-story.txt'
with open(toy_story_review, encoding='utf-8') as file:
    title = file.readline()[:-1]
    review_url = file.readline()[:-1]
    review_text = file.read()
    df_list.append({'title': title,
                   'review_url': review_url,
                   'review_text': review_text})
```


```python
#-*- coding:utf-8 -*-
from wordcloud import WordCloud, STOPWORDS
import matplotlib.pyplot as plt
import numpy as np
from PIL import Image

# 生成词云
def create_word_cloud(f):
    print('根据词频计算词云')  
    # 设置停用词
    stopwords = set(STOPWORDS)
    stopwords.update(["hello", "hi"])
    # 设置词云形状
    mask_img = np.array(Image.open(r"./toy-story.png"))
    
    wc = WordCloud(
     background_color='white',# 设置背景颜色
#      font_path='../../fonts/SimHei.ttf',  # 设置字体，针对中文的情况需要设置中文字体，否则显示乱码
     max_font_size=150,# 设置字体最大值
     width=1080,# 设置画布的宽度
     height=1920,# 设置画布的高度
     random_state=30,# 设置多少种随机状态，即多少种颜色
     mask = mask_img, 
     stopwords = stopwords
    )
    wordcloud = wc.generate(f)
     # 写词云图片
    wordcloud.to_file("./wordcloud.jpg")
     # 显示词云文件
    plt.imshow(wordcloud)
    plt.axis("off")
    plt.show()
create_word_cloud(review_text)
```

    根据词频计算词云




![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.005h4j4qc242.png)

## JSON文件结构
JSON（Javascript Object Notation）顾名思义JavaScript对象标记而XML代表可延伸标记语言

API的大部分数据都是JSON或XML格式
### Writing JSON
将Python dict对象转换为序列化的JSON字符串。  
- `json.dump()`：它将dict对象以JSON格式写入“文本文件”。
- `json.dumps()`：略有变化json.dump()。它返回实际的JSON字符串，并在JSON str中提供更多控件。


```python
import json

data = {}  
data['people'] = []  
data['people'].append({'name': 'Scott', 'website': 'stackabuse.com', 'from': 'Nebraska'})
data['people'].append({'name': 'Larry', 'website': 'google.com', 'from': 'Michigan'})
data['people'].append({'name': 'Tim', 'website': 'apple.com', 'from': 'Alabama'})

with open('json_data.txt', 'w') as out_f:  
    json.dump(data, out_f)
```

### Reading  JSON
- `json.load()`：它从文件中读取字符串，解析JSON数据，使用数据填充Python dict并将其返回。
- `json.loads()`：略有变化`json.load()`。它允许我们直接处理str（因为很多时候你可能没有包含你的JSON的类文件对象）。


```python
with open('json_data.txt') as json_f:  
    data = json.load(json_f)
data
```


    {'people': [{'name': 'Scott', 'website': 'stackabuse.com', 'from': 'Nebraska'},
      {'name': 'Larry', 'website': 'google.com', 'from': 'Michigan'},
      {'name': 'Tim', 'website': 'apple.com', 'from': 'Alabama'}]}




```python
for p in data['people']:
    print('Name: ' + p['name'])
    print('Website: ' + p['website'])
    print('From: ' + p['from'])
    print('')
```

    Name: Scott
    Website: stackabuse.com
    From: Nebraska
    
    Name: Larry
    Website: google.com
    From: Michigan
    
    Name: Tim
    Website: apple.com
    From: Alabama



## 来源：API（应用程序编程接口）
### MediaWiki API:   Wptools

MediaWiki有许多不同的库，可以满足各种编程语言的需求。对于MediaWiki，Python中最新的称为[wptools](https://github.com/siznax/wptools)。

例如，Twitter的类似关系是：  

- MediaWiki API→wptools
- Twitter API→tweepy
- wptools指南：（https://github.com/siznax/wptools）
- tweepy准则：（https://media.readthedocs.org/pdf/tweepy/latest/tweepy.pdf）

>wptools在国内无法使用，需要梯子。还有另外一种方法，通过[Kaggle](https://www.kaggle.com/)创建一个Kernel来使用。


```python
# !pip install wptools
```


```python
import wptools

page = wptools.page('E.T._the_Extra-Terrestrial').get()
images = page.data['image']
images[0]['url']
```

    en.wikipedia.org (query) E.T._the_Extra-Terrestrial
    en.wikipedia.org (parse) 73441
    www.wikidata.org (wikidata) Q11621
    www.wikidata.org (labels) P5786|P3417|Q1748409|P344|Q488645|Q1368...
    www.wikidata.org (labels) Q443775|P725|Q849124|P1712|P161|P4276|Q...
    www.wikidata.org (labels) Q720068|P577|Q230390|P136|Q7341915|P310...
    www.wikidata.org (labels) Q1757366|Q752245|P3844|P2529|Q354873|P2...
    en.wikipedia.org (restbase) /page/summary/E.T._the_Extra-Terrestrial
    en.wikipedia.org (imageinfo) File:E t the extra terrestrial ver3....
    E.T. the Extra-Terrestrial (en) data
    {
      WARNINGS: <dict(1)> extracts
      aliases: <list(2)> E.T., ET
      assessments: <dict(4)> United States, Film, Science Fiction, Lib...
      claims: <dict(95)> P1562, P57, P272, P345, P31, P161, P373, P480...
      description: <str(63)> 1982 American science fiction film direct...
      exhtml: <str(570)> <p><i><b>E.T. the Extra-Terrestrial</b></i> i...
      exrest: <str(549)> E.T. the Extra-Terrestrial is a 1982 American...
      extext: <str(1720)> _**E.T. the Extra-Terrestrial**_ is a 1982 A...
      extract: <str(1824)> <p class="mw-empty-elt"></p><p><i><b>E.T. t...
      image: <list(4)> {'kind': 'parse-image', 'file': 'File:E t the e...
      infobox: <dict(18)> name, image, caption, director, producers, w...
      iwlinks: <list(6)> https://commons.wikimedia.org/wiki/Category:E...
      label: E.T. the Extra-Terrestrial
      labels: <dict(175)> P5786, P3417, Q1748409, P344, Q488645, Q1368...
      length: 79,927
      links: <list(464)> 12 Monkeys, 12 Years a Slave (film), 1941 (fi...
      modified: <dict(2)> page, wikidata
      pageid: 73441
      parsetree: <str(99699)> <root><template><title>Redirect</title><...
      random: The Combine Harvester
      redirects: <list(38)> 'pageid': 177061, 'ns': 0, 'title': 'E.T....
      requests: <list(9)> query, parse, wikidata, labels, labels, labe...
      title: E.T._the_Extra-Terrestrial
      url: https://en.wikipedia.org/wiki/E.T._the_Extra-Terrestrial
      url_raw: <str(67)> https://en.wikipedia.org/wiki/E.T._the_Extra-...
      watchers: 294
      what: film
      wikibase: Q11621
      wikidata: <dict(95)> AllMovie movie ID (P1562), director (P57), ...
      wikidata_pageid: 13150
      wikidata_url: https://www.wikidata.org/wiki/Q11621
      wikitext: <str(79622)> Redirect|E.T.|other uses|ET (disambigua...
    }



    'https://upload.wikimedia.org/wikipedia/en/6/66/E_t_the_extra_terrestrial_ver3.jpg'



图像文件，使用PIL(Python Image Library)


```python
import requests
from PIL import Image
from io import BytesIO

r = requests.get(images[0]['url'])
i = Image.open(BytesIO(r.content))
i
```





![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.xbfl72w4yi.png)


```python
import pandas as pd
import requests   #general web scraping to RAM package 
import wptools   #'wikipedia-specific' web scraping package
import os   #filepath, folder editing package
from PIL import Image 
from io import BytesIO

folder_name = 'bestofrt_posters'

if not os.path.exists(folder_name):
    os.makedirs(folder_name)

df_list = []

image_errors = {}
title_list =["Avengers: Endgame", "Wolf Warriors", "Spirited Away"]
for t in title_list:
    try:
        # This cell is slow so print ranking to gauge time remaining
        ranking = title_list.index(t) + 1
        print(ranking)
        pagee = wptools.page(t, silent=True)
        images = pagee.get().data['image']

        # First image is usually the poster. That's the image we want. Get the Url.
        first_image_url = images[0]['url']

        #Now we obtain the image file in our RAM !
        res = requests.get(first_image_url)

        # Download movie poster image
        i = Image.open(BytesIO(res.content))
        image_file_format = first_image_url.split('.')[-1]
        local_url = folder_name + "/" + str(ranking) + "_" + t + '.' + image_file_format
        i.save(local_url)

        # Append to list of dictionaries
        df_list.append({'ranking': int(ranking),
                        'title': t,
                        'img': i,
                        'poster_url': first_image_url})

    # Not best practice to catch all exceptions but fine for this short script
    except Exception as e:
        print(str(ranking) + "_" + t + ": " + str(e))
        image_errors[str(ranking) + "_" + t] = images
df_list
```

    1
    2
    3



    [{'ranking': 1,
      'title': 'Avengers: Endgame',
      'img': <PIL.JpegImagePlugin.JpegImageFile image mode=RGB size=220x326 at 0x7F76A830B4A8>,
      'poster_url': 'https://upload.wikimedia.org/wikipedia/en/0/0d/Avengers_Endgame_poster.jpg'},
     {'ranking': 2,
      'title': 'Wolf Warriors',
      'img': <PIL.JpegImagePlugin.JpegImageFile image mode=RGB size=264x377 at 0x7F76A831C160>,
      'poster_url': 'https://upload.wikimedia.org/wikipedia/en/1/11/Wolf_Warriors_poster.jpg'},
     {'ranking': 3,
      'title': 'Spirited Away',
      'img': <PIL.PngImagePlugin.PngImageFile image mode=RGBA size=265x376 at 0x7F76A832F2B0>,
      'poster_url': 'https://upload.wikimedia.org/wikipedia/en/d/db/Spirited_Away_Japanese_poster.png'}]




```python
for i in df_list:
    print(i["title"])
    display(i["img"])
```


- Avengers: Endgame
![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.69m3cvq008e.png)

- Wolf Warriors
![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.9i0vxf8v2i.png)

- Spirited Away
![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.51qz44eaukg.png)

## Python & 关系数据库

### 数据争论和关系数据库

在数据争论的背景下，建议数据库和SQL仅用于收集数据或存储数据。

- **连接到数据库并将数据**导入pandas DataFrame（或首选编程语言中的类似数据结构），然后评估和清理该数据，或者
- **连接到数据库并存储**刚刚收集的**数据**（可能来自数据库），评估和清理

当您拥有大量数据时，这些任务尤为必要，这是SQL和其他数据库优于平面文件的地方。

上述两种情况可以进一步细分为三个主要任务：

- 用Python连接数据库
- **From** pandas DataFrame存储数据 **to** 已连接的数据库中
- **From** 已连接的数据库导入数据 **to** pandas DataFrame 

连接到数据库。使用[SQLAlchemy](https://www.sqlalchemy.org/)连接到关系数据库，SQLAlchemy是Python的数据库工具包。

使用方法参考[SQLAlchemy使用/ Pandas操作SQLAlchemy/ PyMySQL操作MySQL](https://hufe09.github.io/2019/04/02/Python-&-SQL/)

## Python & NoSQL
非关系型数据库在如今的大数据环境下越来越受到重用。相比传统的关系型数据库，非关系型数据库在越来越多的使用场景下极大地提升了生产力。

非关系型数据库的佼佼者——文档型数据库MongoDB与键值数据库Redis，比较流行。

MongoDB 是目前最流行的 NoSQL 数据库之一，使用的数据类型 BSON（类似 JSON）。Python 要连接 MongoDB 需要 MongoDB 驱动，这里我们使用 PyMongo 驱动来连接。
使用方法参考[MongoDB简单使用](https://hufe09.github.io/2019/06/01/MongoDB/)

## 数据获取手段：数据仓库(Data Warehouse)
数据仓库，英文名称Data Warehouse，简写为DW。数据仓库顾名思义，是一个很大的数据存储集合，出于企业的分析性报告和决策支持目的而创建，对多样的业务数据进行筛选与整合。它为企业提供一定的BI（商业智能）能力，指导业务流程改进、监视时间、成本、质量以及控制。

![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.smeetkbcv9.png)

数据仓库的输入方是各种各样的数据源，最终的输出用于企业的数据分析、数据挖掘、数据报表等方向。

不同数据源的数据集成，所依靠的是**ETL**

### 什么是ETL？
ETL的英文全称是 Extract-Transform-Load 的缩写，用来描述将数据从来源迁移到目标的几个过程：
- 1.Extract，数据抽取，也就是把数据从数据源读出来。
- 2.Transform，数据转换，把原始数据转换成期望的格式和维度。如果用在数据仓库的场景下，Transform也包含数据清洗，清洗掉噪音数据。
- 3.Load  数据加载，把处理后的数据加载到目标处，比如数据仓库。
![image](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/image.k6hjxuxqynm.png)

在国内常用，是一款基于Hadoop的开源数据仓库，名叫**Hive**.

知名商业数据仓库有很多，比如 Oracle，Db2等等。其中业界老大，要属**Teradata**.

参考[什么是数据仓库？](https://blog.csdn.net/bjweimengshu/article/details/79256504)

### **数据库 VS 数据仓库**
- 数据库面向业务存储，仓库面向主题存储（主题较高层次上对分析对象数据的一个完整并且一致的描述）
- 数据库针对应用（OLTP），仓库针对分析（OLAP）
- 数据库组织规范，仓库可能冗余，相对变化大，数据量大

## 其他文件格式

本文中介绍的的文件类型是绝大多数争论项目进行交互的文件类型。例如：

- 平面文件（例如CSV和TSV）
- HTML文件
- JSON文件
- TXT文件
- 关系数据库文件

其他不太常见的文件格式包括：

- [Excel files](https://www.lifewire.com/what-is-an-xlsx-file-2622540)
- [Pickle files](https://stackoverflow.com/questions/7501947/understanding-pickling-in-python)
- [HDF5 files](http://neondataskills.org/HDF5/About)
- [SAS files](http://whatis.techtarget.com/fileformat/SAS-SAS-program-file)
- [STATA files](http://faculty.econ.ucdavis.edu/faculty/cameron/stata/stataintro.html)

Pandas有[函数](https://pandas.pydata.org/pandas-docs/stable/reference/index.html)来读取（和写入，大多数）这些文件。

## 总结
本文介绍了几种获取数据的方式
- 本地文件，包括平面文件等
- 从网络下载，例如爬虫等
- API接口获取，很多API返回数据都是JSON
- 从数据库获取，包括关系型数据库和NoSQL
- 从数据仓库获取
- 从其他一些不常见的文件格式获取

> [Jupyter Notebook](<https://nbviewer.jupyter.org/github/hufe09/DataWrangling/blob/master/Gather/Gathering.ipynb>)
>
> [Github](<https://github.com/hufe09/DataWrangling>)
