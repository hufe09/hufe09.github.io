---
layout: post
title: "「数据分析」Module 02: Gathering Data"
subtitle: 'Data Analysis : Data Wrangling'
author: "Hufe"
header-img: "img/post-bg-python.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Python
  - Data Analysis
---

# Gathering


```python
import pandas as pd
```


```python
df_bestofrt = pd.read_csv('bestofrt.tsv', sep='\t')
```


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




    <matplotlib.collections.PathCollection at 0x24b9b0330b8>





![title](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/gitnote/2019/04/28/output_4_1-1556459299807.png)

## 爬取在线网页数据


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
soup
```



```
<!DOCTYPE html>
<html dit="ltr" lang="en" xmlns:fb="http://www.facebook.com/2008/fbml" xmlns:og="http://opengraphprotocol.org/schema/">

<title>E.T. The Extra-Terrestrial (1982) - Rotten Tomatoes</title>
    

<body>
<div class="mop-ratings-wrap__half">
<h1 class="mop-ratings-wrap__score">
<a class="unstyled articleLink" href="#contentReviews" id="tomato_meter_link">
<span class="mop-ratings-wrap__icon meter-tomato icon big medium-xs certified_fresh"></span>
<span class="mop-ratings-wrap__percentage">
                                        98%
                                    </span>
</a>
</h1>

<div class="mop-ratings-wrap__half">
<h1 class="mop-ratings-wrap__score">
<a class="unstyled articleLink" href="#audience_reviews">
<span class="mop-ratings-wrap__icon meter-tomato icon big medium-xs upright"></span>
<span class="mop-ratings-wrap__percentage mop-ratings-wrap__percentage--audience">
                                        72%
                                        <br/>
<strong class="mop-ratings-wrap__text--small">liked it</strong>
</span>
</a>
</h1>

</body>
</html>
```
*只保留下面用到的部分页面内容，删除其余部分。*


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
soup.find('span', class_= 'mop-ratings-wrap__percentage').contents
```




    ['\n                                        98%\n                                    ']




```python
soup.find('span', class_= 'mop-ratings-wrap__percentage').contents[0].strip()[:-1]
```




    '98'




```python
soup.find('span', class_= 'mop-ratings-wrap__percentage--audience').contents[0].strip()
```




    '72%'



## 爬取本地网页数据


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
df1 = pd.DataFrame(df_list, columns=['title', 'audience_score', 'number_of_audience_ratings'])
df1
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
      <td>12 Angry Men (Twelve Angry Men) (1957)</td>
      <td>97</td>
      <td>103672</td>
    </tr>
    <tr>
      <th>1</th>
      <td>The 39 Steps (1935)</td>
      <td>86</td>
      <td>23647</td>
    </tr>
    <tr>
      <th>2</th>
      <td>The Adventures of Robin Hood (1938)</td>
      <td>89</td>
      <td>33584</td>
    </tr>
    <tr>
      <th>3</th>
      <td>All About Eve (1950)</td>
      <td>94</td>
      <td>44564</td>
    </tr>
    <tr>
      <th>4</th>
      <td>All Quiet on the Western Front (1930)</td>
      <td>89</td>
      <td>17768</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Casablanca (1942)</td>
      <td>95</td>
      <td>355952</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Frankenstein (1931)</td>
      <td>87</td>
      <td>41140</td>
    </tr>
    <tr>
      <th>7</th>
      <td>King Kong (1933)</td>
      <td>86</td>
      <td>89669</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Laura (1944)</td>
      <td>91</td>
      <td>10481</td>
    </tr>
    <tr>
      <th>9</th>
      <td>M (1931)</td>
      <td>95</td>
      <td>35778</td>
    </tr>
    <tr>
      <th>10</th>
      <td>The Maltese Falcon (1941)</td>
      <td>91</td>
      <td>57268</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Metropolis (1927)</td>
      <td>92</td>
      <td>62018</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Rear Window (1954)</td>
      <td>95</td>
      <td>149458</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Rebecca (1940)</td>
      <td>92</td>
      <td>39026</td>
    </tr>
    <tr>
      <th>14</th>
      <td>A Streetcar Named Desire (1951)</td>
      <td>90</td>
      <td>54761</td>
    </tr>
    <tr>
      <th>15</th>
      <td>Touch of Evil (1958)</td>
      <td>92</td>
      <td>30867</td>
    </tr>
    <tr>
      <th>16</th>
      <td>High Noon (1952)</td>
      <td>89</td>
      <td>25065</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Snow White and the Seven Dwarfs (1937)</td>
      <td>78</td>
      <td>469510</td>
    </tr>
    <tr>
      <th>18</th>
      <td>12 Years a Slave (2013)</td>
      <td>90</td>
      <td>138789</td>
    </tr>
    <tr>
      <th>19</th>
      <td>The 400 Blows (Les Quatre cents coups) (1959)</td>
      <td>94</td>
      <td>38368</td>
    </tr>
    <tr>
      <th>20</th>
      <td>Alien (1979)</td>
      <td>94</td>
      <td>457186</td>
    </tr>
    <tr>
      <th>21</th>
      <td>Apocalypse Now (1979)</td>
      <td>94</td>
      <td>284606</td>
    </tr>
    <tr>
      <th>22</th>
      <td>Argo (2012)</td>
      <td>90</td>
      <td>207373</td>
    </tr>
    <tr>
      <th>23</th>
      <td>Army of Shadows (L'Armée des ombres) (1969)</td>
      <td>94</td>
      <td>7011</td>
    </tr>
    <tr>
      <th>24</th>
      <td>Arrival (2016)</td>
      <td>82</td>
      <td>78740</td>
    </tr>
    <tr>
      <th>25</th>
      <td>Baby Driver (2017)</td>
      <td>89</td>
      <td>48114</td>
    </tr>
    <tr>
      <th>26</th>
      <td>Battleship Potemkin (1925)</td>
      <td>85</td>
      <td>18709</td>
    </tr>
    <tr>
      <th>27</th>
      <td>A Hard Day's Night (1964)</td>
      <td>89</td>
      <td>50067</td>
    </tr>
    <tr>
      <th>28</th>
      <td>Bicycle Thieves (Ladri di biciclette) (1949)</td>
      <td>94</td>
      <td>33723</td>
    </tr>
    <tr>
      <th>29</th>
      <td>Boyhood (2014)</td>
      <td>81</td>
      <td>88478</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>70</th>
      <td>Selma (2015)</td>
      <td>86</td>
      <td>60533</td>
    </tr>
    <tr>
      <th>71</th>
      <td>Seven Samurai (Shichinin no Samurai) (1956)</td>
      <td>97</td>
      <td>90049</td>
    </tr>
    <tr>
      <th>72</th>
      <td>Singin' in the Rain (1952)</td>
      <td>95</td>
      <td>137643</td>
    </tr>
    <tr>
      <th>73</th>
      <td>Skyfall (2012)</td>
      <td>86</td>
      <td>372497</td>
    </tr>
    <tr>
      <th>74</th>
      <td>Spotlight (2015)</td>
      <td>93</td>
      <td>68142</td>
    </tr>
    <tr>
      <th>75</th>
      <td>Star Trek (2009)</td>
      <td>91</td>
      <td>746458</td>
    </tr>
    <tr>
      <th>76</th>
      <td>Star Wars: Episode VII - The Force Awakens (2015)</td>
      <td>89</td>
      <td>222789</td>
    </tr>
    <tr>
      <th>77</th>
      <td>Sunset Boulevard (1950)</td>
      <td>95</td>
      <td>52783</td>
    </tr>
    <tr>
      <th>78</th>
      <td>Taxi Driver (1976)</td>
      <td>93</td>
      <td>258405</td>
    </tr>
    <tr>
      <th>79</th>
      <td>The Babadook (2014)</td>
      <td>72</td>
      <td>37024</td>
    </tr>
    <tr>
      <th>80</th>
      <td>The Battle of Algiers (La Battaglia di Algeri)...</td>
      <td>95</td>
      <td>14267</td>
    </tr>
    <tr>
      <th>81</th>
      <td>The Big Sick (2017)</td>
      <td>90</td>
      <td>23391</td>
    </tr>
    <tr>
      <th>82</th>
      <td>The Cabinet of Dr. Caligari (Das Cabinet des D...</td>
      <td>89</td>
      <td>27163</td>
    </tr>
    <tr>
      <th>83</th>
      <td>The Conformist (1970)</td>
      <td>91</td>
      <td>8529</td>
    </tr>
    <tr>
      <th>84</th>
      <td>The Dark Knight (2008)</td>
      <td>94</td>
      <td>1827436</td>
    </tr>
    <tr>
      <th>85</th>
      <td>The Good, the Bad and the Ugly (1966)</td>
      <td>97</td>
      <td>238420</td>
    </tr>
    <tr>
      <th>86</th>
      <td>The Jungle Book (2016)</td>
      <td>86</td>
      <td>92856</td>
    </tr>
    <tr>
      <th>87</th>
      <td>The Third Man (1949)</td>
      <td>93</td>
      <td>53081</td>
    </tr>
    <tr>
      <th>88</th>
      <td>The Wizard of Oz (1939)</td>
      <td>89</td>
      <td>874425</td>
    </tr>
    <tr>
      <th>89</th>
      <td>The Wrestler (2008)</td>
      <td>88</td>
      <td>139795</td>
    </tr>
    <tr>
      <th>90</th>
      <td>Tokyo Story (Tôkyô monogatari) (1953)</td>
      <td>93</td>
      <td>11325</td>
    </tr>
    <tr>
      <th>91</th>
      <td>Toy Story (1995)</td>
      <td>92</td>
      <td>1107731</td>
    </tr>
    <tr>
      <th>92</th>
      <td>Toy Story 2 (1999)</td>
      <td>86</td>
      <td>998186</td>
    </tr>
    <tr>
      <th>93</th>
      <td>Toy Story 3 (2010)</td>
      <td>89</td>
      <td>605098</td>
    </tr>
    <tr>
      <th>94</th>
      <td>The Treasure of the Sierra Madre (1948)</td>
      <td>93</td>
      <td>25627</td>
    </tr>
    <tr>
      <th>95</th>
      <td>Up (2009)</td>
      <td>90</td>
      <td>1201878</td>
    </tr>
    <tr>
      <th>96</th>
      <td>Vertigo (1958)</td>
      <td>93</td>
      <td>101454</td>
    </tr>
    <tr>
      <th>97</th>
      <td>The Wages of Fear (1953)</td>
      <td>95</td>
      <td>8536</td>
    </tr>
    <tr>
      <th>98</th>
      <td>Wonder Woman (2017)</td>
      <td>90</td>
      <td>112955</td>
    </tr>
    <tr>
      <th>99</th>
      <td>Zootopia (2016)</td>
      <td>92</td>
      <td>98633</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 3 columns</p>
</div>




```python
import matplotlib.pyplot as plt
%matplotlib inline
plt.scatter(df1.audience_score, df_bestofrt.critic_score)
```




    <matplotlib.collections.PathCollection at 0x24b9c741f60>




![title](https://raw.githubusercontent.com/hufe09/GitNote-Images/master/gitnote/2019/04/28/output_20_1-1556459428097.png)


## 爬取影评


```python
import requests 
import pandas as pd
import os
```


```python
folder_name1 = 'ebert_reviews1'
if not os.path.exists(folder_name1):
    os.makedirs(folder_name1)
url = 'http://www.baidu.com'
response = requests.get(url)
response
```




    <Response [200]>




```python
response.content
```




    b'<!DOCTYPE html>\r\n<!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=http://s1.bdstatic.com/r/www/cache/bdorz/baidu.min.css><title>\xe7\x99\xbe\xe5\xba\xa6\xe4\xb8\x80\xe4\xb8\x8b\xef\xbc\x8c\xe4\xbd\xa0\xe5\xb0\xb1\xe7\x9f\xa5\xe9\x81\x93</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=\xe7\x99\xbe\xe5\xba\xa6\xe4\xb8\x80\xe4\xb8\x8b class="bg s_btn"></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>\xe6\x96\xb0\xe9\x97\xbb</a> <a href=http://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>\xe5\x9c\xb0\xe5\x9b\xbe</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>\xe8\xa7\x86\xe9\xa2\x91</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>\xe8\xb4\xb4\xe5\x90\xa7</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>\xe7\x99\xbb\xe5\xbd\x95</a> </noscript> <script>document.write(\'<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u=\'+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ \'" name="tj_login" class="lb">\xe7\x99\xbb\xe5\xbd\x95</a>\');</script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">\xe6\x9b\xb4\xe5\xa4\x9a\xe4\xba\xa7\xe5\x93\x81</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>\xe5\x85\xb3\xe4\xba\x8e\xe7\x99\xbe\xe5\xba\xa6</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>\xe4\xbd\xbf\xe7\x94\xa8\xe7\x99\xbe\xe5\xba\xa6\xe5\x89\x8d\xe5\xbf\x85\xe8\xaf\xbb</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>\xe6\x84\x8f\xe8\xa7\x81\xe5\x8f\x8d\xe9\xa6\x88</a>&nbsp;\xe4\xba\xacICP\xe8\xaf\x81030173\xe5\x8f\xb7&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>\r\n'




```python
urls = ['http://www.baidu.com', 'http://www.sina.com']
for url in urls:
    response = requests.get(url)
    with open(os.path.join(folder_name1, url.split('/')[-1]), mode = 'wb') as file:
        file.write(response.content)
```


```python
os.listdir(folder_name1)
```




    ['www.baidu.com', 'www.sina.com']




```python
import glob
```


```python
folder_name = 'ebert_reviews'
df_list = []
for ebert_review in glob.glob('ebert_reviews/*.txt'):
    with open(ebert_review, encoding='utf-8') as file:
        title = file.readline()[:-1]
        review_url = file.readline()[:-1]
        review_text = file.read()
        df_list.append({'title': title,
                       'review_url': review_url,
                       'review_text': review_text})
```


```python
df = pd.DataFrame(df_list, columns=['title', 'review_url', 'review_text'])
title_list = []
for t in df.title:
    title_list.append(t)
title_list
```




    ['The Wizard of Oz (1939)',
     'Metropolis (1927)',
     'Battleship Potemkin (1925)',
     'E.T. The Extra-Terrestrial (1982)',
     'Modern Times (1936)',
     "Singin' in the Rain (1952)",
     'Boyhood (2014)',
     'Casablanca (1942)',
     'Moonlight (2016)',
     'Psycho (1960)',
     'Laura (1944)',
     'Citizen Kane (1941)',
     'Nosferatu, a Symphony of Horror (Nosferatu, eine Symphonie des Grauens) (Nosferatu the Vampire) (1922)',
     'Snow White and the Seven Dwarfs (1937)',
     "A Hard Day's Night (1964)",
     'La Grande illusion (Grand Illusion) (1938)',
     'The Battle of Algiers (La Battaglia di Algeri) (1967)',
     'Dunkirk (2017)',
     'The Maltese Falcon (1941)',
     '12 Years a Slave (2013)',
     'The Third Man (1949)',
     'Gravity (2013)',
     'Sunset Boulevard (1950)',
     'King Kong (1933)',
     'Spotlight (2015)',
     'The Adventures of Robin Hood (1938)',
     'Rashômon (1951)',
     'Rear Window (1954)',
     'Selma (2015)',
     'Taxi Driver (1976)',
     'Toy Story 3 (2010)',
     'Get Out (2017)',
     'Argo (2012)',
     'Toy Story 2 (1999)',
     'The Big Sick (2017)',
     'The Bride of Frankenstein (1935)',
     'Zootopia (2016)',
     'M (1931)',
     'Wonder Woman (2017)',
     'Alien (1979)',
     'Bicycle Thieves (Ladri di biciclette) (1949)',
     'Mad Max: Fury Road (2015)',
     'Seven Samurai (Shichinin no Samurai) (1956)',
     'The Treasure of the Sierra Madre (1948)',
     'Up (2009)',
     '12 Angry Men (Twelve Angry Men) (1957)',
     'The 400 Blows (Les Quatre cents coups) (1959)',
     'Logan (2017)',
     "Army of Shadows (L'Armée des ombres) (1969)",
     'Arrival (2016)',
     'Baby Driver (2017)',
     'The Cabinet of Dr. Caligari (Das Cabinet des Dr. Caligari) (1920)',
     'A Streetcar Named Desire (1951)',
     'The Night of the Hunter (1955)',
     'Star Wars: Episode VII - The Force Awakens (2015)',
     'Manchester by the Sea (2016)',
     'Dr. Strangelove Or How I Learned to Stop Worrying and Love the Bomb (1964)',
     'Vertigo (1958)',
     'The Dark Knight (2008)',
     'Touch of Evil (1958)',
     'The Babadook (2014)',
     'All About Eve (1950)',
     "Rosemary's Baby (1968)",
     'Finding Nemo (2003)',
     'Brooklyn (2015)',
     'The Wrestler (2008)',
     'L.A. Confidential (1997)',
     'Gone With the Wind (1939)',
     'The Good, the Bad and the Ugly (1966)',
     'Inside Out (2015)',
     'Skyfall (2012)',
     'Tokyo Story (Tôkyô monogatari) (1953)',
     'Hell or High Water (2016)',
     'Pinocchio (1940)',
     'The Jungle Book (2016)',
     'La La Land (2016)',
     'Star Trek (2009)',
     'Apocalypse Now (1979)',
     'The Godfather (1972)',
     'On the Waterfront (1954)',
     'The Wages of Fear (1953)',
     'The Last Picture Show (1971)',
     'Harry Potter and the Deathly Hallows - Part 2 (2011)',
     'The Grapes of Wrath (1940)',
     'Man on Wire (2008)',
     'Jaws (1975)',
     'Toy Story (1995)',
     'The Godfather, Part II (1974)']



## API 
### rtsimple

“MediaWiki” 有许多不同的“访问库 ”来满足现有的各种编程语言。

- Wikipedia-API：易于使用的Python 3库。
- wptools：维基百科工具（适用于人类）。
- Pywikibot：一组python脚本和一个强大的机器人写作库。
- wikitools：围绕API提供几层抽象。不支持Python 3。
- mwclient：一个Python库，可以访问大多数API函数....
- 等等....

对于MediaWiki，调用Python中最新的和[人类可读]的wptools。例如，Twitter的类似关系是：  

- MediaWiki API→wptools
- Twitter API→tweepy
- wptools指南：（https://github.com/siznax/wptools）
- tweepy准则：（https://media.readthedocs.org/pdf/tweepy/latest/tweepy.pdf）


```python
import requests
from PIL import Image
from io import BytesIO

 url = 'https://en.wikipedia.org/wiki/E.T._the_Extra-Terrestrial#/media/File:E_t_the_extra_terrestrial_ver3.jpg'
r = requests.get(url)
i = Image.open(BytesIO(r.content))
```


```python
import rtsimple as rt
rt.API_KEY = 'your api key'
movie = rt.Movies('10489')
```


```python
import wptools
page = wptools.page('Mahatma_Gandhi')
page.data['image'][0]['size']
```

*wptools的原因，运行未成功，故只做记录*

![](https://user-images.githubusercontent.com/31917400/37098852-dc257a7a-2216-11e8-9805-8c54ad5da83d.jpg)

图像文件，使用PIL库（枕头）和非文本请求的io库。  
例如：


```python
import pandas as pd
import requests   #general web scraping to RAM package 
# import wptools   #'wikipedia-specific' web scraping package
import os   #filepath, folder editing package
from PIL import Image 
from io import BytesIO
```


```python
folder_name2 = 'bestofrt_posters'

if not os.path.exists(folder_name2):
    os.makedirs(folder_name2)
```


```python
df_list = []

image_errors = {}

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
        i.save(folder_name + "/" + str(ranking) + "_" + t + '.' + image_file_format)
        
        # Append to list of dictionaries
        df_list.append({'ranking': int(ranking),
                        'title': t,
                        'poster_url': first_image_url})
    
    # Not best practice to catch all exceptions but fine for this short script
    except Exception as e:
        print(str(ranking) + "_" + t + ": " + str(e))
        image_errors[str(ranking) + "_" + t] = images
```


    NameError: name 'wptools' is not defined

*wptools无法安装，运行不成功*


```python
for i in image_errors.keys(): 
    print(i)
```

## Writing JSON
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

with open('data.txt', 'w') as out_f:  
    json.dump(data, out_f)
```

## Reading  JSON
- `json.load()`：它从文件中读取字符串，解析JSON数据，使用数据填充Python dict并将其返回。
- `json.loads()`：略有变化`json.load()`。它允许我们直接处理str（因为很多时候你可能没有包含你的JSON的类文件对象）。


```python
with open('data.txt') as json_f:  
    data = json.load(json_f)
    for p in data['people']:
        print('Name: ' + p['name'])
        print('Website: ' + p['website'])
        print('From: ' + p['from'])
        print('')
```


```python
# ! pip install wordcloud
```


```python
from  wordcloud import WordCloud
wordcloud = WordCloud(background_color="white", 
width=800, 
height=600, 
font_path="msyh.ttc", 
max_words=200, 
max_font_size=80, 
#mask = mask, 
stopwords = excludes, 
).generate(newtxt)
```
