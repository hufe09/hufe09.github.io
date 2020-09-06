---
layout: post
title: "「爬虫」Selenium 配置及使用"
subtitle: 'Selenium 库使用'
author: "Hufe"
header-img: "img/post-bg-python.jpg"
header-mask: 0.3
mathjax: true
tags:
  - Python
  - Crawler
  - Selenium
---

## 1. Selenium

**Selenium** 是 Web 应用的测试工具，可以直接运行在浏览器中，它的原理是模拟用户在进行操作，支持当前多种主流的浏览器。

这里我们模拟 Chrome 浏览器的页面访问。

你需要先引用 Selenium 中的 WebDriver 库。WebDriver 实际上就是 Selenium 2，是一种用于 Web 应用程序的自动测试工具，提供了一套友好的 API，方便我们进行操作。

然后通过 WebDriver 创建一个 Chrome 浏览器的 drive，再通过 drive 获取访问页面的完整 HTML。

当你获取到完整的 HTML 时，就可以对 HTML 中的 XPath 进行提取，在这里我们需要找到图片地址 srcs 和电影名称 titles。这里通过 XPath 语法匹配到了多个元素，因为是多个元素，所以我们需要用 for 循环来对每个元素进行提取。

**但 Selenium 用的时候有个麻烦事，就是环境的相关配置，得安装好相关浏览器，比如 Chrome、Firefox 等等，然后还要到官方网站去下载对应的驱动，最重要的还需要安装对应的 Python Selenium 库，确实是不是很方便，另外如果要做大规模部署的话，环境配置的一些问题也是个头疼的事情。**

- Chrome 点击下载chrome的webdriver： <http://chromedriver.storage.googleapis.com/index.html>
  不同的 Chrome 的版本对应的 chromedriver.exe 版本也不一样，下载时不要搞错了。如果是最新的 Chrome, 下载最新的 chromedriver.exe 就可以了。
  把 chromedriver 的路径也加到环境变量里。
- Firefox Firefox 驱动下载地址为：<https://github.com/mozilla/geckodriver/releases/>
  根据自己的操作系统下载对应的驱动即可，使用的话，需要把驱动的路径和火狐浏览器的路径加入到环境变量里面才可以
- IE IE浏览器驱动下载地址为：<http://selenium-release.storage.googleapis.com/index.html>
  根据自己 selenium 版本下载对应版本的驱动即可，python 的话，下载里面的 IEDriverServerxxx.zip 即可，这个是区分32和64位系统的，根据自己的系统下载即可，需要注意的是，如果要打开IE浏览器的话，需要在浏览器的 Internet 选项中的安全页里有4个安全选项，Internet、本地Internet、受信任的站点、受限制的站点，这4个里面都有一个启用保护模式，都需要勾选上才可以，还得把驱动的路径加入到环境变量中。



## 2. ChromeDriver 
[ChromeDriver](https://docs.seleniumhq.org/docs/03_webdriver.jsp#chromedriver) 由 [Chromium](https://bugs.chromium.org/p/chromium/issues/list) 项目 iteslf 维护/支持。WebDriver 通过 chromedriver 二进制文件与 Chrome 一起使用（可在 chrome 项目的下载页面上找到）。您需要安装 chromedriver 和 Chrome 浏览器版本。chromedriver 需要放在系统路径的某个位置，以便 WebDriver 自动发现它。Chrome 浏览器本身是由 chromedriver 在默认安装路径中发现的。这两者都可以被环境变量覆盖。 

### 版本选择
我们维护 ChromeDriver 的多个版本。选择哪个版本取决于您使用的 Chrome 版本。特别：

- ChromeDriver 使用与 Chrome 相同的版本号方案。有关详细信息，请参阅 https://www.chromium.org/developers/version-numbers
- 每个版本的 ChromeDriver 都支持 Chrome，其中包含主要版本，次要版本和版本号。例如，ChromeDriver 73.0.3683.20支持所有以 73.0.3683 开头的 Chrome 版本。
- 在 Chrome 的新主要版本进入 Beta 之前，将发布匹配版本的 ChromeDriver。
- 在初始发布新主要版本后，我们将根据需要发布补丁。这些修补程序可能与 Chrome 的更新一致，也可能不一致。

以下是选择要下载的 ChromeDriver 版本的步骤：

- 首先，找出您正在使用的 Chrome 版本。Linux 使用 `google-chrome --version`查看，图形界面在 Chrome 浏览器 【帮助】->【关于 Google Chrome】查看。假设你有 Chrome 72.0.3626.81。
- 获取 Chrome 版本号，删除最后一部分，然后将结果附加到网址 https://chromedriver.storage.googleapis.com/LATEST_RELEASE 例如，使用 Chrome 版本 72.0.3626.81，您将获得一个网址 https://chromedriver.storage.googleapis.com/LATEST_RELEASE_72.0.3626
- 使用在上一步中创建的 URL 来检索包含要使用的 ChromeDriver 版本的小文件。例如，上面的 URL 将获得包含“72.0.3626.69”的文件。（当然，实际数字可能会在未来发生变化。）
- 使用从上一步骤检索到的版本号构建下载 ChromeDriver 的 URL。对于版本 72.0.3626.69，URL 将为 https://chromedriver.storage.googleapis.com/index.html?path=72.0.3626.69/
- 初始下载后，建议您偶尔再次执行上述过程，看看是否有任何错误修复版本。

>摘录自 [ChromeDriver 官方页面](https://sites.google.com/a/chromium.org/chromedriver/downloads/version-selection)


```python
import requests
requests.get("https://chromedriver.storage.googleapis.com/LATEST_RELEASE").text
```
    Google Chrome 76.0.3809.132 


### ChromeDriver 下载

确定 Chrome 浏览器的版本后，下载 [ChromeDriver](https://sites.google.com/a/chromium.org/chromedriver/) 可执行文件。

- 将 chromedriver 添加进环境变量
  - [Windows](https://blog.csdn.net/qq_41429288/article/details/80472064)
      - 将 chromedriver.exe 复制到 Chrome 浏览器安装目录，C:\Program Files (x86)\Google\Chrome\Application 目录，将此目录加入环境变量。
  
      - 将 chromedriver.exe 加入 python 程序同目录（亲测有效）。
  - [Mac](https://blog.csdn.net/ywj_486/article/details/80940087)
  - Linux
    在命令行中进入下载文件所在路径，将其移动到 `/usr/bin`
    
    `sudo mv chromedriver /usr/bin`


然后按照 [Wiki页面](https://github.com/SeleniumHQ/selenium/wiki/ChromeDriver) 上的其他说明进行操作。

**Requirements**

The server expects you to have Chrome installed in the default location for each system:

| **OS**        | **Expected Location of Chrome**                              |
| ------------- | ------------------------------------------------------------ |
| Linux         | /usr/bin/google-chrome                                      |
| Mac           | /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome |
| Windows XP    | %HOMEPATH%\Local Settings\Application Data\Google\Chrome\Application\chrome.exe |
| Windows Vista | C:\Users%USERNAME%\AppData\Local\Google\Chrome\Application\chrome.exe |


Ubutnu 安装 Chrome
- step1：将 Chrome 下载源加入到系统的源列表
```
sudo wget http://www.linuxidc.com/files/repo/google-chrome.list -P /etc/apt/sources.list.d/
```
- step2：导入谷歌软件的公钥，用于对之后下载的软件进行验证
```
wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -
```
- step3：对当前系统的可用更新列表进行更新，用于随时获得最新的软件版本信息 
```
sudo apt-get update
```
- step4：安装 Chrome 稳定版
```
sudo apt-get install google-chrome-stable
```
- 查看 Chrome 版本
```
google-chrome-stable --version
```
- 打开 google-chrome-stable
```
/usr/bin/google-chrome-stable
```

`$ google-chrome-stable --version`

    Google Chrome 76.0.3809.132 


`$ ls /usr/bin/ | grep google`

    google-chrome
    google-chrome-stable


## 3. Selenium-WebDriver
安装：

`$ pip install selenium`


```python
from selenium import webdriver
chromedriver_path = './chromedriver'
driver = webdriver.Chrome(chromedriver_path)
request_url = "http://www.baidu.com"
driver.get(request_url)
```

如果一切正常，此时会运行一个新的 Chrome 窗口来打开百度页面。
![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/gHVT8BrDj2nUzQh.png)

如果下载的 chromedriver 没有添加到环境变量里面，会报如下报错:

`WebDriverException: Message: 'chromedriver' executable needs to be in PATH. Please see https://sites.google.com/a/chromium.org/chromedriver/home`

解决方法有以下几种：
- 需要在 `webdriver.Chrome()` 中填写 chromedriver 路径。Chrome 浏览器安装目录。

例如：`driver = webdriver.Chrome('C:\Program Files (x86)\Google\Chrome\Application\chromedriver.exe')`

- 参照前面 ChromeDriver 下载时介绍的方法，将 chromedriver 加入环境变量或 Python 同目录。

## 4. Selenium 官方网站提供的测试案例

https://docs.seleniumhq.org/docs/03_webdriver.jsp#selenium-webdriver-api-commands-and-operations


```python
from selenium import webdriver
from selenium.common.exceptions import TimeoutException
from selenium.webdriver.support.ui import WebDriverWait # available since 2.4.0
from selenium.webdriver.support import expected_conditions as EC # available since 2.26.0

# Create a new instance of the Firefox driver
driver = webdriver.Chrome()

# go to the google home page
driver.get("http://www.google.com")

# the page is ajaxy so the title is originally this:
print(driver.title)

# find the element that's name attribute is q (the google search box)
inputElement = driver.find_element_by_name("q")

# type in the search
inputElement.send_keys("cheese!")

# submit the form (although google automatically searches now without submitting)
inputElement.submit()

try:
    # we have to wait for the page to refresh, the last thing that seems to be updated is the title
    WebDriverWait(driver, 10).until(EC.title_contains("cheese!"))

    # You should see "cheese! - Google Search"
    print(driver.title)

finally:
    driver.quit()
```

```
Google
cheese! - Google 搜索
```

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/oaVIJfUY8Sus9DL.png)

![title](https://gitee.com/hufe09/image_hosting/raw/master/PicGo/G6oj1fKcLnW7CZJ.gif)



## 5. headless 模式

如果不想让 Chrome 页面启动，可以配置为 headless 模式，这样在启动的时候我们便看不到任何界面了。
这次换百度，唯一变得就是百度 input 的 name 为 “wd”

``` html
<input id="kw" name="wd" class="s_ipt" value="" maxlength="255" autocomplete="off">
```


```python
from selenium.webdriver.chrome.options import Options


def chrome_headless():   
    chrome_options = Options()
    chrome_options.add_argument('--no-sandbox')
    # 使用headless模式：不打开浏览器
    chrome_options.add_argument('--headless')
    # 谷歌文档提到需要加上这个属性来规避bug
    chrome_options.add_argument('--disable-gpu')
    # 初始化实例
    driver = webdriver.Chrome(options=chrome_options)
    return driver
```


```python
from selenium import webdriver
from selenium.common.exceptions import TimeoutException
from selenium.webdriver.support.ui import WebDriverWait # available since 2.4.0
from selenium.webdriver.support import expected_conditions as EC # available since 2.26.0


# Create a new instance of the Firefox driver
# driver = webdriver.Chrome()
driver = chrome_headless()

# go to the baidu home page
driver.get("http://www.baidu.com")

# the page is ajaxy so the title is originally this:
print(driver.title)

# find the element that's name attribute is wd (the baidu search box)
inputElement = driver.find_element_by_name("wd")

# type in the search
inputElement.send_keys("cheese!")

# submit the form (although baidu automatically searches now without submitting)
inputElement.submit()

try:
    # we have to wait for the page to refresh, the last thing that seems to be updated is the title
    WebDriverWait(driver, 10).until(EC.title_contains("cheese!"))

    # You should see "cheese!_百度搜索"
    print(driver.title)

finally:
    driver.quit()
```

```
百度一下，你就知道
cheese!_百度搜索
```

[官方页面](https://docs.seleniumhq.org/docs/03_webdriver.jsp#selenium-webdriver-api-commands-and-operations)也提供了其他的 Selenium-WebDriver API命令和操作。

## 6. 总结

**Selenium 使用步骤**
1. 安装selenium `$ pip install selenium`
2. 得安装好相关浏览器，比如 Chrome、Firefox 等等
3. 根据选择的浏览器，下载浏览器版本对应的 WebDriver， 将 chromedriver 添加到环境变量
4. 使用，以 ChromeDriver 为例

``` python
from selenium import webdriver
driver = webdriver.Chrome()
request_url = "http://www.baidu.com"
driver.get(request_url)
```
5. headless模式，不显示浏览器窗口,后台执行

``` python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options

chrome_options = Options()
chrome_options.add_argument('--no-sandbox')
# 使用headless模式：不打开浏览器
chrome_options.add_argument('--headless')
# 谷歌文档提到需要加上这个属性来规避bug
chrome_options.add_argument('--disable-gpu')
# 初始化实例
driver = webdriver.Chrome(options=chrome_options)
request_url = "http://www.baidu.com"
driver.get(request_url)
```
6. 如果添加进环境变量执行失败，有以下错误，就在 `webdriver.Chrome()` 中填写 chromedriver 路径。

`WebDriverException: Message: 'chromedriver' executable needs to be in PATH.`

``` python
chromedriver_path = 'the/path/of/chromedriver'
driver = webdriver.Chrome(chromedriver_path)
```
