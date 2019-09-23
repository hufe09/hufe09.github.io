---
layout: post
title: "「爬虫」今日头条爬虫"
subtitle: 'Python Web 爬虫'
author: "Hufe"
header-img: "img/post-bg-python.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Python
  - Crawler
---


# 1. 探索发现

## 页面查看

今日头条是动态渲染页面的典型网站，我们一起来看一下数据抓取的过程。

今日头条页面内容为瀑布型结构，即没有传统的按钮下一页，通过鼠标滑轮往下翻就可以一直加载。

通过浏览器开发者工具查看。

![title](https://i.loli.net/2019/08/25/5KnJwfeDHvEOLxW.png)

Request URL: https://www.toutiao.com/api/pc/feed/?max_behot_time=1566696918&category=__all__&utm_source=toutiao&widen=1&tadrequire=true&as=A1A5AD46B1EF5C6&cp=5D617F753C367E1&_signature=KanSeBAWdM.Bc.AFqJnbDCmp0m

**将上面得到的链接打开，即可看到json格式的数据。**

![title](https://i.loli.net/2019/08/25/OBngl8b7SQdoimU.png)

``` json
{
"single_mode": true,
"abstract": "“老夏分析师”为您解读在职场中，经常能够碰到领导对员工说：“辛苦了”。毫无疑问这是领导看到了员工的努力，对员工辛苦工作的认可与肯定。当领导对你说：“辛苦了”，千万不要想都没有想就开口乱回答，要根据所处的环境、领导的表情、肢体动作来进行回答。",
"middle_mode": true,
"more_mode": true,
"tag": "news_career",
"comments_count": 1089,
"tag_url": "search/?keyword=%E8%81%8C%E5%9C%BA",
"title": "领导说“辛苦了”该怎么回？低情商才说不辛苦，怎么回才显情商高？",
"chinese_tag": "职场",
"source": "悟空问答",
"group_source": 10,
"has_gallery": false,
"media_url": "/c/user/token/MS4wLjABAAAAG7bWj_1abQtsxfnvbooQLE7820Ll5jVxLyYUeIOUzw4/",
"media_avatar_url": "//p7.pstatp.com/large/6ee90002d5dcc84d8afc",
"image_list": [
{
"url": "//p9.pstatp.com/list/2b2a0000078e276915456"
},
{
"url": "//p1.pstatp.com/list/2b29600007897dd2f0f77"
},
{
"url": "//p1.pstatp.com/list/174f300080138dce628f4"
}
],
"source_url": "/group/6725288482740109572/",
"article_genre": "wenda",
"item_id": "6725288482740109572",
"is_feed_ad": false,
"behot_time": 1566696468,
"image_url": "//p9.pstatp.com/list/190x124/2b2a0000078e276915456",
"group_id": "6725288482740109572",
"middle_image": "http://p9.pstatp.com/list/2b2a0000078e276915456"
},
```

>使用[ json-viewe 插件 ](https://github.com/tulios/json-viewer)让json数据漂亮地显示

## 请求地址分析

```
https://www.toutiao.com/api/pc/feed/?
    max_behot_time=1566696918&
    category=__all__&
    utm_source=toutiao&
    widen=1&
    tadrequire=true&
    as=A1A5AD46B1EF5C6&
    cp=5D617F753C367E1&
    _signature=KanSeBAWdM.Bc.AFqJnbDCmp0m
    
```

滑动滚轮翻页再次得到一个获取数据的链接。
和之前的链接进行对比。

```
https://www.toutiao.com/api/pc/feed/?
    max_behot_time=1566694281&
    category=__all__&
    utm_source=toutiao&
    widen=1&
    tadrequire=true&
    as=A165ADB601CF5C7&
    cp=5D617FF55C276E1&
    _signature=KanSeBAWdM.Bc.AFqJknVymp0m
```
对比发现，`max_behot_time`、`as` 和 `cp` 参数有变化。

`max_behot_time`为时间戳，所以可以猜测，今日头条翻页是根据时间戳来实现。

# 2. 准备工作

导入使用的库


```python
import requests
import json
import random
import time
import pprint
import pandas as pd
from pyquery import PyQuery as pq
```


```python
print(time.time())
print(int(time.time()))
```

    1566727705.6936185
    1566727705



```python
url = 'https://www.toutiao.com/api/pc/feed/?max_behot_time=1566687351&category=__all__&utm_source=toutiao&widen=1&tadrequire=true&as=A125BD66A10FF9C&cp=5D614FEF391C8E1&_signature=KanSeBAWdM.Bc.AFqJmDoCmp0m'
response = requests.get(url)
print(response)
```

    <Response [200]>


返回状态码为200，代表请求成功。


```python
response.text
```

获取数据成功：
```
{"has_more": true, "message": "success", "data": [{"chinese_tag": "\u65f6\u653f", "media_avatar_url": "//p9.pstatp.com/large/ff1b0000332acf4a6c2e", "is_feed_ad": false, "tag_url": "search/?keyword=%E6%97%B6%E6%94%BF", "title": "\u603b\u4e66\u8bb0\u5173\u5fc3\u7684\u767e\u59d3\u8eab\u8fb9\u4e8b\uff5c\u517b\u8001\u9662\u65b0\u201c\u98ce\u666f\u201d\uff1a\u5927\u7231\u626e\u7f8e\u5915\u9633\u7ea2", "single_mode": false, "middle_mode": false, "abstract": "\u603b\u4e66\u8bb0\u5173\u5fc3\u7684\u767e\u59d3\u8eab\u8fb9\u4e8b\uff5c\u517b\u8001\u9662\u65b0\u201c\u98ce\u666f\u201d\uff1a\u5927\u7231\u626e\u7f8e\u5915\u9633\u7ea2", "tag": "news_politics", "behot_time": 1566686676, "source_url": "/group/6728927593506013708/", "source": "\u65b0\u534e\u7f51\u5ba2\u6237\u7aef",
...
```

获取数据失败：
```
'{"message": "error", "data": [], "has_more": false}'
```

成功获取到的数据，因为编码的问题，我们不能直观地查看信息。
为了观察方便，我们使用下面两种方法：
1. 所以采用 utf-8 编码，unicode_escape 转码。将 `\u53f2\uff1a\u4e70` 格式的信息转换为中文。
但是，展示时还是以一个字符串的方式整体展示。


```python
response.text.encode("utf-8").decode("unicode_escape")
```

```
...
"preload_web": 0, "ad_label": "广告", "user_repin": 0, "label_style": 3, "item_version": 0, "group_id": "6726811016866300171", "middle_image": {}, "gallary_image_count": 3}, {"single_mode": true, "abstract": "市值蒸发70亿！85后董事长“跑路”？这家上市公司“翻车”也许不是意外……", "middle_mode": true, "more_mode": true, "tag": "news_investment", "comments_count": 72, "tag_url": "search/?keyword=None", "title": "市值蒸发70亿！85后女董事长“跑路”？这家上市公司“翻车”也许不是意外……", "source": "光明网", "group_source": 2, "has_gallery": false, "media_url": "/c/user/token/MS4wLjABAAAA9Lz0MeLdJDmqpU26Xi9O_M-cYI9z530wjM7eDKvzZTw/", "media_avatar_url": "//p4.pstatp.com/large/2c68000006582322ad80", "image_list": [{"url": "//p3.pstatp.com/list/pgc-image/Ra6vM245i1bESq"}, {"url": "//p1.pstatp.com/list/pgc-image/Ra6vM2L3VM6Umk"}, {"url": "//p3.pstatp.com/list/pgc-image/Ra6vM2Y59EpqEu"}], 
...

```

2. 用json转换，文字及格式都转换了，层次分明，很漂亮。


```python
json.loads(response.text)
```

``` json
{'single_mode': True,
   'abstract': '市值蒸发70亿！85后董事长“跑路”？这家上市公司“翻车”也许不是意外……',
   'middle_mode': True,
   'more_mode': True,
   'tag': 'news_investment',
   'comments_count': 72,
   'tag_url': 'search/?keyword=None',
   'title': '市值蒸发70亿！85后女董事长“跑路”？这家上市公司“翻车”也许不是意外……',
   'source': '光明网',
   'group_source': 2,
   'has_gallery': False,
   'media_url': '/c/user/token/MS4wLjABAAAA9Lz0MeLdJDmqpU26Xi9O_M-cYI9z530wjM7eDKvzZTw/',
   'media_avatar_url': '//p4.pstatp.com/large/2c68000006582322ad80',
   'image_list': [{'url': '//p3.pstatp.com/list/pgc-image/Ra6vM245i1bESq'},
```

## 2.1 用户代理

![title](https://i.loli.net/2019/08/25/aQCN4RiKJXZGyLM.png)

user-agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.19 Safari/537.36 Edg/77.0.235.9

代表浏览器信息，爬虫时为了防止限制访问，需要不断改变浏览器信息，即 user-agent 。告诉服务端，请求是通过不同浏览器来发送的。一种身份伪装的意思。

### [fake-useragent](https://pypi.org/project/fake-useragent/)

`pip install fake-useragent`

Successfully installed fake-useragent-0.1.11


```python
from fake_useragent import UserAgent
ua = UserAgent()
```


```python
ua.ie
```


    'Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0; yie8)'




```python
ua.chrome
```


    'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36'




```python
ua.google
```


    'Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.93 Safari/537.36'

上面ua请求返回的结果其实能在这个链接 [http://fake-useragent.herokuapp.com/browsers/0.1.11](http://fake-useragent.herokuapp.com/browsers/0.1.11) 找到。
存储格式为 json，挑了一些拿出来。
``` json
{
  "browsers": {
    "chrome": [
      "Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2228.0 Safari/537.36",
      "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2227.1 Safari/537.36",
      ...
    ],
    "opera": [
      "Opera/9.80 (X11; Linux i686; Ubuntu/14.10) Presto/2.12.388 Version/12.16",
      "Opera/9.80 (Windows NT 6.0) Presto/2.12.388 Version/12.14",
      ...
    ],
    "firefox": [
      "Mozilla/5.0 (Windows NT 6.1; WOW64; rv:40.0) Gecko/20100101 Firefox/40.1",
      "Mozilla/5.0 (Windows NT 6.3; rv:36.0) Gecko/20100101 Firefox/36.0",
      ...
    ]
   },
  "randomize": {
    "0": "chrome",
    "741": "internetexplorer",
    "789": "firefox",
    "939": "safari",
    "975": "opera"
   }
}
```

## 2.2 ip代理

[https://www.kuaidaili.com/free/](https://www.kuaidaili.com/free/)

**目的：让被爬取的网站不知道我是谁**

| IP              | PORT  | 匿名度 | 类型 | 位置                                               | 响应速度 | 最后验证时间        |
| :-------------- | :---- | :----- | :--- | :------------------------------------------------- | :------- | :------------------ |
| 163.204.245.0   | 9999  | 高匿名 | HTTP | 广东省汕尾市 联通                                  | 2秒      | 2019-08-25 11:30:54 |
| 175.44.150.58   | 9000  | 高匿名 | HTTP | 中国 福建省 三明市 联通                            | 2秒      | 2019-08-25 10:30:58 |
| 1.198.72.133    | 9999  | 高匿名 | HTTP | 河南省济源市 电信                                  | 3秒      | 2019-08-25 09:31:00 |
| 123.206.54.52   | 8118  | 高匿名 | HTTP | 天津市天津市 腾讯云计算（北京）有限责任公司 腾讯云 | 1秒      | 2019-08-25 08:30:52 |
| 171.11.178.97   | 9999  | 高匿名 | HTTP | 河南省济源市 电信                                  | 3秒      | 2019-08-25 07:30:57 |
| 180.119.68.208  | 9999  | 高匿名 | HTTP | 江苏省扬州市 电信                                  | 3秒      | 2019-08-25 06:30:56 |
| 112.111.96.100  | 9000  | 高匿名 | HTTP | 中国 福建省 三明市 联通                            | 2秒      | 2019-08-25 05:30:51 |
| 163.204.245.89  | 9999  | 高匿名 | HTTP | 广东省汕尾市 联通                                  | 0.5秒    | 2019-08-25 04:30:59 |
| 163.204.241.38  | 9999  | 高匿名 | HTTP | 广东省汕尾市 联通                                  | 3秒      | 2019-08-25 03:30:55 |
| 163.204.243.200 | 9999  | 高匿名 | HTTP | 广东省汕尾市 联通                                  | 2秒      | 2019-08-25 02:31:01 |
| 121.232.194.117 | 9000  | 高匿名 | HTTP | 中国 江苏省 镇江市 电信                            | 1秒      | 2019-08-25 01:30:56 |
| 60.191.57.78    | 13629 | 高匿名 | HTTP | 浙江省杭州市 电信                                  | 1秒      | 2019-08-25 00:31:01 |
| 49.81.125.237   | 9000  | 高匿名 | HTTP | 江苏省徐州市 电信                                  | 3秒      | 2019-08-24 23:30:59 |
| 61.176.223.7    | 58822 | 高匿名 | HTTP | 辽宁省葫芦岛市 联通                                | 2秒      | 2019-08-24 22:30:58 |
| 180.118.86.86   | 9000  | 高匿名 | HTTP | 中国 江苏省 镇江市 电信                            | 2秒      | 2019-08-24 21:30:34 |

- get/post：请求方法
- url：我要去哪儿，去往西天取经
- proxies：我是从哪来，我从东土大唐而来
- headers：我是怎么来的，骑着白龙马来的还是开着奔驰来的


```python
headers = {
    "user_agent": ua.chrome
}
proxies = {
    "url": "http://112.111.96.100:9000"
}
response = requests.get(url, headers=headers, proxies=proxies)
json.loads(response.text)
```

## 2.3 将用户代理和IP代理存储在本地

### 用户代理本地存储（浏览器）


```python
def write_browser_info_to_file():
    """
    将远端的浏览器信息存储在本地，加快请求速度
    """
    agent_url = 'http://fake-useragent.herokuapp.com/browsers/0.1.11'
    try:
        my_user_agent = requests.get(agent_url).text
    except ConnectionError as e:
        print("ConnectionError:", e)
    agent_json = json.loads(my_user_agent)

    with open('browser_info.json', "w") as f:
        json.dump(my_user_agent, f)

    print("浏览器 json 已存储。")

# write_browser_info_to_file()
```

用with，保证文件关闭。
json.dump 会自动保存格式，写进文件就是 json格式，方便后面读取。

**json方法dump,dumps,load,loads区别于联系**

load 和 dump 是一对，它们用来操作文件

loads 和 dumps 是一对，它们用来 json 和字符串之间相互转换的

下面看下load 与 loads 的区别：


```python
with open('browser_info.json', 'r') as f:
    str_browser_json = json.load(f)
dict_browser_json = json.loads(str_browser_json)
type(str_browser_json), type(dict_browser_json)
```


    (str, dict)




```python
def get_browsers_from_file():
    """
    从本地json中获取浏览器信息
    """
    with open('browser_info.json', 'r') as f:
        browser_json = json.load(f)
        return browser_json
```


```python
def get_random_user_agent():
    """
    随机选择一个浏览器
    """
    agent_json = json.loads(get_browsers_from_file())
    browsers = tuple(agent_json['browsers'].keys())

    # 随机出一个浏览器类型
    i = random.randint(0, len(browsers)-1)
    browser_name = browsers[i]

    # 随机出一个具体的浏览器
    browser_details = agent_json['browsers'][browser_name]
    j = random.randint(0, len(browser_details)-1)
    select_browser = browser_details[j]
    return select_browser

get_random_user_agent()
```


    'Mozilla/5.0 (Windows; U; Windows NT 6.0; ja-JP) AppleWebKit/533.20.25 (KHTML, like Gecko) Version/5.0.4 Safari/533.20.27'



### IP代理本地存储


```python
def get_proxies_pool(proxies_html):
    """
    获取ip代理池
    """
    html = pq(proxies_html)
    theads = html("table")("thead")("tr").text().split("\n")
    tbody = html("table")("tbody")("tr")
    proxies_pool = []
    for tr in tbody.items():
        for td in tr.items():
            td_json = {}
            td_list = td.text().split("\n")
            for i in range(len(theads)):
                td_json[theads[i]] = td_list[i]
    
            proxies_pool.append(td_json)
    return proxies_pool
```


```python
def write_proxies_info_to_file():
    """
    将ip代理池存储在本地，加快请求速度
    """
    proxies_url = 'https://www.kuaidaili.com/free/'
    try:
        proxies_html = requests.get(proxies_url).text
    except ConnectionError as e:
        print("ConnectionError:", e)
        
    proxies_pool = get_proxies_pool(proxies_html)
    

    with open('ip_proxies.json', "w") as f:
        json.dump(proxies_pool, f)

    print("proxies 已存储。")

# write_proxies_info_to_file()
```


```python
def get_proxies_from_file():
    """
    从本地json中获取ip代理
    """
    with open('ip_proxies.json', 'r') as f:
        proxies_json = json.load(f)
        return proxies_json
```


```python
def get_random_proxies():
    """
    随机选择一个ip代理
    """
    proxies_list = get_proxies_from_file()
    
    # 随机出一个ip代理
    i = random.randint(0, len(proxies_list)-1)
    proxies = proxies_list[i]
    return f'http://{proxies["IP"]}:{proxies["PORT"]}'
    
get_random_proxies()
```


    'http://163.204.245.0:9999'



# 3. 爬取头条


```python
def get_request_url_and_headers():
    """
    构造请求工具，url, headers, proxies
    """
    user_agent = get_random_user_agent()
    ip_proxies = get_random_proxies()
    headers = {
        "user_agent": user_agent
    }
    proxies = {
        "url": ip_proxies
    }
    current_time = int(time.time())
    url = f'https://www.toutiao.com/api/pc/feed/?max_behot_time={current_time}&category=__all__&utm_source=toutiao&widen=1& \
          tadrequire=true&as=A125BD66A10FF9C&cp=5D614FEF391C8E1&_signature=KanSeBAWdM.Bc.AFqJmDoCmp0m'

    return url, headers, proxies
```


```python
def get_response_html():
    """
    请求链接，返回页面内容
    """
    url, headers, proxies = get_request_url_and_headers()
    try:
        response = requests.get(url, headers=headers, proxies=proxies)
        global response_json
        response_json = json.loads(response.text)
    except ConnectionError as e:
        print("ConnectionError:", e)
        
    if response_json['message'] == "error":
        get_response_html()
    return response_json
```

##### 注意如果将response_json设置为全局变量，会出现一坑，看下面的结果

``` python
def get_response_html():
    url, headers, proxies = get_request_url_and_headers()
    try:
        response = requests.get(url, headers=headers, proxies=proxies)
#         global response_json
        response_json = json.loads(response.text)
    except ConnectionError as e:
        print("ConnectionError:", e)
        
    if response_json['message'] == "error":
        get_response_html()
    return response_json


res_success = get_response_html()
print("-"*50)
print("res_success:\n", res_success)
```
运行结果:
```
{'message': 'error', 'data': [], 'has_more': False}
{'message': 'error', 'data': [], 'has_more': False}
{'message': 'error', 'data': [], 'has_more': False}

{'has_more': True,
 'message': 'success',
 'data': [{'log_extra': '{"ad_price":"XWI60gAVQdRdYjrSABVB1Jw2TurwFrBsmoKhqw","convert_component_suspend":0,"convert_id":0,"external_action":0,"orit":1,"req_id":"20190825153753010152041233635E2E2","rit":1}',
 ...
 }

--------------------------------------------
res_success:
 {'message': 'error', 'data': [], 'has_more': False}
```

上面例子中，前几次都发生错误，第4次请求成功了，但是最终打印 res_success 时，还是打印了错误的请求结果。

递归后回来的数据，将递归外面的值返回了出来。

解决办法：将 response_json 升级为全局变量 `global response_json`




```python
def data_to_file():
    """
    获取到的页面数据存储
    """
    data = get_response_html()["data"]
    
    for i in range(len(data)):
        data_dict = data[i]
        
        with open("toutiao.json", "a+") as f:
            json.dump(data_dict, f, ensure_ascii=False)
            f.write("\n")
            
data_to_file()    
```

### 存储结果


```python
df = pd.read_json("toutiao.json", lines=True)
df.to_excel("toutiao.xlsx")
```

![title](https://i.loli.net/2019/08/25/1ALUwlkR3nIQYHe.png)
