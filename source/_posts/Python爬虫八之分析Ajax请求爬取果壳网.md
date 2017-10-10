---
title: Python爬虫(8):分析Ajax请求爬取果壳网
date: 2017-07-17 16:38:39
tags:
  - Python
  - 爬虫
categories: Python
keywords:
  - Python
  - 爬虫
  - 分析Ajax
urltitle: python-spider-Ajax-guoke
---
本篇文章我们来研究一下怎么分析网页的`Ajax`请求。

我们在平时爬取网页的时候，可能都遇到过有些网页直接请求得到的 HTML 代码里面，并没有我们需要的数据，也就是我们在浏览器中看到的内容。

这就是因为这些信息是通过`Ajax`加载的，并且通过`js`渲染生成的。这个时候我们就需要分析这个网页的请求了。
<!-- more -->

## 什么是Ajax

> AJAX即“Asynchronous Javascript And XML”（异步JavaScript和XML），是指一种创建交互式网页应用的网页开发技术。
> AJAX = 异步 JavaScript和XML（标准通用标记语言的子集）。
> AJAX 是一种用于创建快速动态网页的技术。
> AJAX 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。

简单的说就是网页加载，浏览器地址栏的网址并没有变，是`javascript`异步加载的网页，应该是`ajax`。`AJAX`一般是通过` XMLHttpRequest` 对象接口发送请求的，`XMLHttpRequest` 一般被缩写为 `XHR`。

## 分析果壳网站点

我们目标网站就以果壳网来进行分析。[地址](http://www.guokr.com/scientific/)

我们可以看到这个网页并没有翻页按钮，而当我们一直往下拉请求，网页会自动的给我们加载出更多内容。但是，当我们观察网页`url`时，发现它并没有随着网页的加载请求而变化。而当我们直接请求这个`url`时，显然我们只能获得到第一页的`html`内容。

![image](http://imgout.ph.126.net/56711076/guoke3.jpg)

### 那我们要怎么获得所有页的数据呢？

我们在`Chrome`中打开开发者工具`(F12)`。我们点击`Network`，点击`XHR`标签。然后我们刷新网页，往下拉请求。这个时候我们就可以看到`XHR`标签，在网页每一次加载的时候就会跳出一个请求。

我们点击第一个请求，可以看到他的参数：

> retrieve_type:by_subject
> limit:20
> offset:18
> -:1500265766286

在点击第二个请求，参数如下：

> retrieve_type:by_subject
> limit:20
> offset:38
> -:1500265766287

`limit`参数是网页每一页限制加载的文章数，`offset`就是页数了。接着往下看，我们会发现每一个请求的`offset`参数都会加 20。

我们接着看每一个请求的响应内容，这是一个就是格式的数据。我们点开`result`键，可以看到一个 20 篇文章的数据信息。这样我们就成功找到我们需要的信息位置了，我们可以在请求头中看到存放`json`数据的`url`地址。`http://www.guokr.com/apis/minisite/article.json?retrieve_type=by_subject&amp;limit=20&amp;offset=18`

![image](http://imgout.ph.126.net/56710039/guoke6.jpg)

## 爬取流程

- 分析Ajax请求获得每一页的文章url信息；
- 解析每一篇文章，获得需要数据；
- 将获得的数据保存数据库；
- 开启多进程，大量抓取。

## 开始

我们的工具仍然使用`requests`请求，`BeautifulSoup`解析。

首先我们要通过分析`Ajax`请求，获得所有页的信息，通过对上面对网页的分析，可以得到`Ajax`加载的`json`数据的`URL`地址为：`http://www.guokr.com/apis/minisite/article.json?retrieve_type=by_subject&amp;limit=20&amp;offset=18`

我们需要构造这个 URL。

```Python
# 导入可能要用到的模块
import requests
from urllib.parse import urlencode
from requests.exceptions import ConnectionError

# 获得索引页的信息
def get_index(offset):
    base_url = 'http://www.guokr.com/apis/minisite/article.json?'
    data = {
        'retrieve_type': "by_subject",
        'limit': "20",
        'offset': offset
    }
    params = urlencode(data)
    url = base_url + params
    
    try:
        resp = requests.get(url)
        if resp.status_code == 200:
            return resp.text
        return None
    except ConnectionError:
        print('Error.')
        return None

```

我们把上面分析页面得到的请求参数构造成一个字典`data`，然后我们可以手动的构造这个`url`，但是`urllib`库已经给我们提供了一个编码方法，我们直接使用，就可以构造出完整的`url`了。然后是标准的`requests`请求页面内容。

```Python
import json

# 解析json，获得文章url
def parse_json(text):
    try:
        result = json.loads(text)
        if result:
            for i in result.get('result'):
                # print(i.get('url'))
                yield i.get('url')
    except:
        pass
```

我们使用`josn.loads`方法解析`json`，将其转化成一个`json`对象。然后直接通过字典的操作，获得文章的`url`地址。这里使用`yield`，每次请求返回一个`url`，降低内存的消耗。由于我在后面抓取的时候出跳出一个`json`解析的错误，这里直接过滤就好。

这里我们可以试着打印看看，是不是成功运行。

既然获得了文章的`url`，那么对于获得文章的数据就显得很简单了。这里不在进行详细的叙述。我们的目标是获得文章的标题，作者和内容。
由于有的文章里面包含一些图片，我们直接过滤掉文章内容里的图片就好了。

```Python
from bs4 import BeautifulSoup

# 解析文章页
def parse_page(text):
    try:
        soup = BeautifulSoup(text, 'lxml')
        content = soup.find('div', class_="content")
        title = content.find('h1', id="articleTitle").get_text()
        author = content.find('div', class_="content-th-info").find('a').get_text()
        article_content = content.find('div', class_="document").find_all('p')
        all_p = [i.get_text() for i in article_content if not i.find('img') and not i.find('a')]
        article = '\n'.join(all_p)
        # print(title,'\n',author,'\n',article)
        data = {
            'title': title,
            'author': author,
            'article': article
        }
        return data
    except:
        pass
```

这里在进行多进程抓取的时候，`BeautifulSoup`也会出现一个错误，依然直接过滤。我们把得到的数据保存为字典的形式，方便保存数据库。

接下来就是保存数据库的操作了，这里我们使用`Mongodb`进行数据的存储。具体的方法在上一篇文章里有说过。不在对他进行详细叙述。

```Python
import pymongo
from config import *

client = pymongo.MongoClient(MONGO_URL, 27017)
db = client[MONGO_DB]

def save_database(data):
    if db[MONGO_TABLE].insert(data):
        print('Save to Database successful', data)
        return True
    return False
```

我们把数据库的名字，和表名保存到`config`配置文件中，在把配置信息导入文件，这样会方便代码的管理。

最后呢，由于果壳网数据还是比较多的，如果想要大量的抓取，我们可以使用多进程。

```Python
from multiprocessing import Pool

# 定义一个主函数
def main(offset):
    text = get_index(offset)
    all_url = parse_json(text)
    for url in all_url:
        resp = get_page(url)
        data = parse_page(resp)
        if data:
            save_database(data)

if __name__ == '__main__':
    pool = Pool()
    offsets = ([0] + [i*20+18 for i in range(500)])
    pool.map(main, offsets)
    pool.close()
    pool.join()
```

函数的参数`offset`就是页数了。经过我的观察，果壳网最后一页页码是 12758，有 637 页。这里我们就抓取 500 页。进程池的`map`方法和`Python`内置的`map`方法使用类似。

![image](http://imgout.ph.126.net/56710040/guoke5.jpg)

好了，对于一些使用`Ajax`加载的网页，我们就可以这么抓取了。

## 项目地址

[here](https://github.com/Blackyukun/GuoKe)

如果觉得有帮助，不妨**star**。

谢谢阅读
