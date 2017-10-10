---
title: Python爬虫(11):Scrapy框架的安装和基本使用
date: 2017-07-27 15:22:09
tags:
  - Python
  - 爬虫
categories: Python
keywords:
  - Python
  - 爬虫
  - Scrapy
urltitle: python-spider-Scrapy-install-and-basic
---
大家好，本篇文章我们来看一下强大的`Python`爬虫框架`Scrapy`。`Scrapy`是一个使用简单，功能强大的异步爬虫框架，我们先来看看他的安装。
<!-- more -->

## Scrapy的安装

`Scrapy`的安装是很麻烦的，对于一些想使用`Scrapy`的人来说，它的安装常常就让很多人死在半路。在此我将我的安装过程和网络上整理的安装方法，分享给大家，希望大家能够安装顺利。

### Windows安装

开始之前，我们要确定自己安装了`Python`，本篇文章我们以`Python3.5`为例。`Scrapy`有很多依赖的包，我们来一一安装。

- 首先，使用`pip -v`，查看`pip`是否安装正常，如果正常，那么我们进行下一步；
- `pip install wheel`这个包我们之前的文章介绍过，安装好他我们就可以安装一些`wheel`件；
- `lxml`安装，之前的文章说过他的安装，那么我们这里在重新整理一下。whl文件地址：[here](http://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml)。找到自己对应版本的文件，下载好后，找到文件位置，右键点击文件属性，点击安全标签，复制他的所在路径。打开管理员工具(cmd)，`pip install <粘贴whl路径>`；
- `PyOpenssl` 的`whl`文件地址：[here](http://www.python.org/pypi/pyOpenSSL#downloads)。点击下载，`whl`文件安装方式同上；
- `Twisted`框架这个框架是一个异步网络库，是`Scrapy`的核心。`whl`文件地址：[here](http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted)；
- `Pywin32`这是一个`Pywin32`兼容的库，下载地址：[here](https://sourceforge.net/projects/pywin32/files/pywon32/Build%20220)，选好版本进行下载；
- 如果上面的库全都安装好了，那么我们就可以安装我们的`Scrapy`了，`pip install scrapy`

是不是很麻烦呢，如果大家不喜欢折腾，那么在`Windows`下也可以很方便的安装。那就要使用我们之前提到的`Anaconda`了。具体安装大家自己找找，或者在之前的文章中找。那么他的安装`Scrapy`只需要一行：

```
conda install scrapy
```

### Linux安装

`Linux`系统安装起来就要简单一点：

```
sudo apt-get install build-essential python3-dev libssl-dev libffi-dev libxml2 libxml2-dev libxslt1-dev zlib1g-dev
```

### Mac OS安装

我们需要先安装一些`C++`的依赖库，`xcode-select --install`

需要安装命令行开发工具，我们点击安装。安装完成，那么依赖库也就安装完成了。

然后我们直接使用`pip`安装`pip install scrapy`

以上，我们的`Scrapy`库的安装基本上就解决了。

## Scrapy的基本使用

`Scrapy`的中文文档地址：[here](http://scrapy-chs.readthedocs.io/zh_CN/0.24/intro/overview.html)

> Scrapy是一个为了爬取网站数据，提取结构性数据而编写的应用框架。 可以应用在包括数据挖掘，信息处理或存储历史数据等一系列的程序中。

他的基本项目流程为：

- 创建一个`Scrapy`项目
- 定义提取的`Item`
- 编写爬取网站的`spider`并提取`Item`
- 编写`Item Pipeline`来存储提取到的`Item`(即数据)

而一般我们的爬虫流程为：

- 抓取索引页：请求索引页的`URL`并得到源代码，进行下一步分析；
- 获取内容和下一页链接：分析源代码，提取索引页数据，并且获取下一页链接，进行下一步抓取；
- 翻页爬取：请求下一页信息，分析内容并请求在下一页链接；
- 保存爬取结果：将爬取结果保存为特定格式和文本，或者保存数据库。

我们一步一步来看看如何使用。

### 创建项目

在开始爬取之前，您必须创建一个新的`Scrapy`项目。 进入您打算存储代码的目录中，运行下列命令（以知乎日报为例）:

```Python
scrapy startproject zhihurb
```

该命令将会创建包含下列内容的 zhihu 目录:

```
zhihurb/
    scrapy.cfg
    zhihurb/
        __init__.py
        items.py
        pipelines.py
        settings.py
        spiders/
            __init__.py
            ...
```

这些文件分别是:

> scrapy.cfg: 项目的配置文件
> zhihurb/: 该项目的python模块。之后您将在此加入代码。
> zhihurb/items.py: 项目中的item文件.
> zhihurb/pipelines.py: 项目中的pipelines文件.
> zhihurb/settings.py: 项目的设置文件.
> zhihurb/spiders/: 放置spider代码的目录.

### 定义Item

这一步是定义我们需要获取到的数据信息，比如我们需要获得网站里的一些`url`，网站文章的内容，文章的作者等。这一步定义的地方就在我们的`items.py`文件。

```Python
import scrapy

class ZhihuItem(scrapy.Item):
    name = scrapy.Field()
    article = scrapy.Field()
```

### 编写Spider

这一步就是写我们最熟悉的爬虫了，而我们的`Scrapy`框架可以让我们不需要去考虑实现的方法，只需要写出爬取的逻辑就可以了。

首先我们需要在 spiders/ 文件夹下创建我们的爬虫文件，比如就叫`spider.py`。写爬虫前，我们需要先定义一些内容。我们以知乎日报为例：`https://daily.zhihu.com/`

```Python
from scrapy import Spider

class ZhihuSpider(Spider):
    name = "zhihu"
    allowed_domains = ["zhihu.com"]
	start_urls = ['https://daily.zhihu.com/']
```

这里我们定义了什么呢?首先我们导入了`Scrapy`的`Spider`组件。然后创建一个爬虫类，在类里我们定义了我们的爬虫名称：zhihu（注意：爬虫名称独一无二的，是不可以和别的爬虫重复的）。还定义了一个网址范围，和一个起始 url 列表，说明起始 url 可以是多个。

然后我们定义一个解析函数：

```Python
def parse(self, response):
    print(response.text)
```

我们直接打印一下，看看这个解析函数是什么。

### 运行爬虫

```Python
scrapy crawl zhihu
```

由于`Scrapy`是不支持在`IDE`中执行，所以我们必须在命令行里执行命令，我们要确定是不是`cd`到爬虫目录下。然后执行，这里的命令顾名思义，`crawl`是蜘蛛的意思，`zhihu`就是我们定义的爬虫名称了。

查看输出，我们先看到的是一些爬虫类的输出，可以看到输出的`log`中包含定义在 `start_urls` 的初始URL，并且与`spider`中是一一对应的。我们接着可以看到打印出了网页源代码。可是我们似乎并没有做什么，就得到了网页的源码，这是`Scrapy`比较方便的一点。

### 提取数据

接着就可以使用解析工具解析源码，拿到数据了。

由于`Scrapy`内置了`CSS`和`xpath`选择器，而我们虽然可以使用`Beautifulsoup`，但是`BeautifulSoup`的缺点就是慢，这不符合我们`Scrapy`的风格，所有我还是建议大家使用`CSS`或者`Xpath`。

由于之前我并没有写过关于`Xpath`或者`CSS`选择器的用法，那么首先这个并不难，而且熟悉浏览器的用法，可以很简单的掌握他们。

我们以提取知乎日报里的文章`url`为例：

```Python
from scrapy import Request

def parse(self, response):
    urls = response.xpath('//div[@class="box"]/a/@href').extract()
	for url in urls:
	    yield Request(url, callback=self.parse_url)
```

这里我们使用`xpath`解析出所有的`url`（extract()是获得所有URL集合，extract_first()是获得第一个）。然后将`url`利用`yield`语法糖，回调函数给下一个解析`url`的函数。

### 使用item

后面详细的组件使用留在下一章讲解，这里假如我们解析出了文章内容和标题，我们要将提取的数据保存到`item`容器。

`Item `对象相当于是自定义的`python`字典。 您可以使用标准的字典语法来获取到其每个字段的值。(字段即是我们之前用Field赋值的属性)。

```Python
# 假如我们下一个解析函数解析出了数据
def parse_url(self, response):
    # name = xxxx
	# article = xxxx
	# 保存
	item = DmozItem()
	item['name'] = name
	item['article'] = article
	
	# 返回item
	yield item
```

### 保存爬取到的数据

这里我们需要在管道文件`pipelines.py`里去操作数据，比如我们要将这些数据的文章标题只保留 5 个字，然后保存在文本里。或者我们要将数据保存到数据库里，这些都是在管道文件里面操作。我们后面在详细讲解。

那么最简单的存储方法是使用命令行命令：

`scrapy crawl zhihu -o items.json`

这条命令就会完成我们的数据保存在根目录的`json`文件里，我们还可以将他格式保存为`msv`,`pickle`等。改变命令后面的格式就可以了。

## 最后

本篇教程仅介绍了`Scrapy`的基础，还有很多特性没有涉及到，那么我会在下一篇文章分享一下我对于`Scrapy`组件的学习理解。

谢谢阅读
