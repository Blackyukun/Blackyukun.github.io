---
title: Python爬虫(4):Beautiful Soup的常用方法
date: 2017-06-01 14:25:07
tags:
  - Python
  - 爬虫
categories: Python
keywords:
  - Python
  - 爬虫
  - BeautifulSoup
urltitle: python-spider-BeautifulSoup-basic
---
`Requests`库的用法大家肯定已经熟练掌握了，但是当我们使用`Requests`获取到网页的 HTML 代码信息后，我们要怎样才能抓取到我们想要的信息呢？我相信大家肯定尝试过很多办法，比如字符串的 find 方法，还有高级点的正则表达式。虽然正则可以匹配到我们需要的信息，但是我相信大家在匹配某个字符串一次一次尝试着正则匹配的规则时，一定很郁闷。

那么，我们就会想有没有方便点的工具呢。答案是肯定的，我们还有一个强大的工具，叫`BeautifulSoup`。有了它我们可以很方便地提取出`HTML`或`XML`标签中的内容，这篇文章就让我们了解下`BeautifulSoup`的常用方法吧。
<!-- more -->

## 什么是BeautifulSoup？

`Python`的网页解析可以用正则表达式去完成，那么我们在写的时候，要挨个的去把代码拿出来匹配，而且还要写匹配的规则，整体实现起来就很复杂。`BeautifulSoup`呢，它是一个方便的网页解析库，处理高效，支持多种解析器。大部分情况下，利用它我们不在需要编写正则表达式就可以方便的实现网页信息的提取。

[官方文档](http://beautifulsoup.readthedocs.io/zh_CN/latest/)

安装：`$ pip install beautifulsoup4`

`BeautifulSoup`是一个网页解析库，它支持很多解析器，不过最主流的有两个。一个是`Python`标准库，一个是`lxml` HTML 解析器。两者的使用方法相似：

```Python
from bs4 import BeautifulSoup

# Python的标准库
BeautifulSoup(html, 'html.parser')

# lxml
BeautifulSoup(html, 'lxml')
```

`Python`内置标准库的执行速度一般，但是低版本的`Python`中，中文的容错能力比较差。`lxml `HTML 解析器的执行速度快，但是需要安装 C语言的依赖库。

## lxml的安装

由于`lxml`安装需要依赖C语言库，所以当`lxml`在`Windows`上安装时，我们会发现各种奇怪的报错，当然脸好的使用`pip install lxml`

安装也是可以成功的。不过大部分人都是会倒在这里。

这里推荐大家使用`lxml`的`.whl`文件来安装。首先我们需要安装一下`wheel`库，有了这个库我们才可以正常安装`.whl`文件。`pip install wheel`

从官方网站下载与系统，`Python`版本匹配的lxml文件：[地址](https://pypi.python.org/pypi/lxml/3.6.0)。

另外，不知道自己系统和`python`版本信息的伙伴。需要进入系统管理员工具（CMD）或者python的 IDLE，输入以下代码：

```Python
import pip

print(pip.pep425tags.get_supported())
```

这时我们就可以看到打印出来的`Python`版本信息了。
下载好`lxml`的文件后，我们需要找到文件的位置，然后进入管理员工具，使用`pip`安装：`pip install whl文件的全名`

安装完成后，可以进入`Python`，`import`一下，如果没有报错，那么恭喜你安装成功。
如果有的伙伴觉得麻烦，那我推荐大家安装`anaconda` [下载地址](https://www.continuum.io/downloads)（如果安装速度慢，可以找国内镜像），不知道是什么的小伙伴可以谷歌一下，有了他，那些在`windows`上`pip`安装出错的问题将不再存在。

## BeautifulSoup的基本标签选择方法

虽然`Python`内置的标准库解析器还不错，但是我还是推荐大家使用`lxml`，因为它够快。那么后面的代码我们都是用`lxml`解析器来进行演示。
我们先导入官方文档的例子:

```Python
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
```

HTML 代码,我们能够得到一个`BeautifulSoup`的对象,并能按照标准的缩进格式的结构输出:

```Python
from bs4 import BeautifulSoup
soup = BeautifulSoup(html_doc, 'lxml')
```

我们可以看到上面的 HTML 代码并不完整，接下来我们使用`prettify()`方法来进行自动补全，注释部分就是运行的输出：

```Python
print(soup.prettify())
# <html>
#  <head>
#   <title>
#    The Dormouse's story
#   </title>
#  </head>
#  <body>
#   <p class="title">
#    <b>
#     The Dormouse's story
#    </b>
#   </p>
#   <p class="story">
#    Once upon a time there were three little sisters; and their names were
#    <a class="sister" href="http://example.com/elsie" id="link1">
#     Elsie
#    </a>
#    ,
#    <a class="sister" href="http://example.com/lacie" id="link2">
#     Lacie
#    </a>
#    and
#    <a class="sister" href="http://example.com/tillie" id="link2">
#     Tillie
#    </a>
#    ; and they lived at the bottom of a well.
#   </p>
#   <p class="story">
#    ...
#   </p>
#  </body>
# </html>
```

### 获取标签

```Python
print(soup.title)
# <title>The Dormouse's story</title>
```

通过输出结果，我们可以看到获取内容的属性，实际上就是 HTML 代码里的一个`title`标签。

### 获取名称

```Python
print(soup.title.name)
# 'title'
```

实际上就是标签的名称。

### 获取属性

```Python
print(soup.p.attrs['class'])
# 'title'

print(soup.p['class'])
# 'title'
```

获取标签的属性我们可以使用`attrs`方法，传给他属性名，就可以得到标签的属性。通过结果我们可以看到，直接传给p标签属性名，一样可以获取到标签属性。

### 获取内容

```Python
print(soup.title.string)
# 'The Dormouse's story'
```

我们还可以使用嵌套的选择，比如我们获得body标签里面p标签的内容：

```Python
print(soup.body.p.string)
# 'The Dormouse's story'
```

## 常见用法

### 标准选择器

虽然`BeautifulSoup`的基本用法，标签获取，内容获取，可以解析一些 html代码。但是在遇到很多复杂的页面时，上面的方法是完全不足的，或者是很繁琐的，因为有时候有的标签会有几个属性（class、id等）。

索性`BeautifulSoup`给我们提供了很方便的标准选择器，也就是 API 方法，这里着重介绍2个: `find()` 和 `find_all()` 。其它方法的参数和用法类似,大家举一反三吧。

### find_all()

`find_all(name, attrs, recursive, text, **kwargs)`可以根据标签，属性，内容查找文档。
`find_all()`其实和正则表达式的原理很相似，他能找出所有能满足匹配模式的结果，在把结果以列表的形式返回。
仍然是文档的例子：

```Python
html_doc = """
<html><head><title>The Dormouse's story</title></head>
<body>
<p class="title"><b>The Dormouse's story</b></p>

<p class="story">Once upon a time there were three little sisters; and their names were
<a href="http://example.com/elsie" class="sister" id="link1">Elsie</a>,
<a href="http://example.com/lacie" class="sister" id="link2">Lacie</a> and
<a href="http://example.com/tillie" class="sister" id="link3">Tillie</a>;
and they lived at the bottom of a well.</p>

<p class="story">...</p>
"""
from bs4 import BeautifulSoup

soup = BeautifulSoup(html_doc, 'lxml')
```

### 过滤器

[文档参考](https://beautifulsoup.readthedocs.io/zh_CN/v4.4.0/#id28)
介绍 `find_all()` 方法前,大家可以参考一下过滤器的类型。过滤器只能作为搜索文档的参数,或者说应该叫参数类型更为贴切。这些过滤器贯穿整个搜索的API。过滤器可以被用在 tag 的`name`中,节点的属性中,字符串中或他们的混合中。

`find_all()` 方法搜索当前 tag 的所有 tag 子节点,并判断是否符合过滤器的条件。这里有几个例子:

```Python
soup.find_all("title")
# [<title>The Dormouse's story</title>]

soup.find_all("p", "title")
# [<p class="title"><b>The Dormouse's story</b></p>]

soup.find_all("a")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.find_all(id="link2")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```

有几个方法很相似,还有几个方法是新的,参数中的 `string` 和`id`是什么含义? 为什么 `find_all("p", "title")` 返回的是CSS Class为”title”的标签? 我们来仔细看一下`find_all()`的参数:

#### name参数

name 参数可以查找所有名字为 name 的 tag,字符串对象会被自动忽略掉。

```Python
soup.find_all("title")
# [The Dormouse's story]
```

搜索 name 参数的值可以使任一类型的过滤器,字符窜,正则表达式,列表,方法或是`True` 。
我们常用的 name 参数是搜索文档的标签名。

#### keyword参数

如果我们的 HTML代码中有几个`div`标签，但是我们只想获取到`class`属性为`top`的`div`标签，我们怎么出来呢。

```Python
soup.find_all('div', class_='top')
# 这里注意下，class是Python的内部关键词，我们需要在css属性class后面加一个下划线'_'，不然会报错。
```

仍然以上面的代码实例：

```Python
soup.find_all('a', id='link2')
# [<a id="link2" href="http://example.com/lacie">Lacie</a>]
```

这样我们就只获取到`id`为`link2`的`a`标签。

#### limit参数

`find_all()` 方法返回全部的搜索结构,如果文档树很大那么搜索会很慢。如果我们不需要全部结果,可以使用 `limit` 参数限制返回结果的数量。效果与 SQL 中的`limit`关键字类似,当搜索到的结果数量达到`limit`的限制时,就停止搜索返回结果。

比如我们要搜索出`a`标签，但是满足的有3个，我们只想要得到2个：

```Python
soup.find_all("a", limit=2)
# [<a id="link1" class="sister" href="http://example.com/elsie">Elsie</a>,
# <a id="link2" class="sister" href="http://example.com/lacie">Lacie</a>]
```

其他的参数，不是经常用到，大家如需了解可以参考官方文档。

### find()

`find_all()`返回的是所有元素列表，`find()`返回单个元素。

`find( name , attrs , recursive , string , **kwargs )`

`find_all()`方法将返回文档中符合条件的所有 tag,尽管有时候我们只想得到一个结果。比如文档中只有一个标签,那么使用`find_all()` 方法来查找标签就不太合适, 使用`find_all`方法并设置`limit=1`参数不如直接使用`find()`方法。下面两行代码是等价的:

```Python
soup.find_all('title', limit=1)
# [The Dormouse's story]

soup.find('title')
#The Dormouse's story
```

唯一的区别是`find_all()`方法的返回结果是值包含一个元素的列表,而`find()`方法直接返回结果。`find_all()`方法没有找到目标是返回空列表, `find()`方法找不到目标时,返回`None`。

### CSS选择器

`Beautiful Soup`支持大部分的 CSS选择器。在`Tag`或`BeautifulSoup`对象的`.select()`方法中传入字符串参数, 即可使用 CSS选择器的语法找到 tag。我们在写 css 时，标签 class类名加"`.`"，id属性加"`#`"。

```Python
soup.select("title")
# [The Dormouse's story]
```

通过 tag标签逐层查找:

```Python
soup.select("body a")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.select("html head title")
# [<title>The Dormouse's story</title>]
```

找到某个 tag标签下的直接子标签:

```Python
soup.select("head > title")
# [<title>The Dormouse's story</title>]

soup.select("p > a")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie"  id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

soup.select("p > #link1")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

soup.select("body > a")
# []
```

通过 CSS 的 class类名查找:

```Python
soup.select(".sister")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
#  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]
```

通过 tag 的 id 查找:

```Python
soup.select("#link1")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>]

soup.select("a#link2")
# [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```

同时用多种 CSS选择器查询元素，使用逗号隔开:

```Python
soup.select("#link1,#link2")
# [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
#  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
```

## 提取标签内容

如果我们得到了几个标签：

```Python
list = [<a href="http://www.baidu.com/">百度</a>,

<a href="http://www.163.com/">网易</a>,

<a href="http://www.sina.com/"新浪</a>]
```

我们要怎样提取他里面的内容呢。我们开始的时候有提及。

```Python
for i in list:
    print(i.get_text()) # 我们使用get_text()方法获得标签内容
    print(i.get['href'] # get['attrs']方法获得标签属性
    print(i['href']) # 简写结果一样
```

结果：

```Python
百度
网易
新浪
http://www.baidu.com/
http://www.163.com/
http://www.sina.com/
http://www.baidu.com/
http://www.163.com/
http://www.sina.com/
```

## 总结

- `BeautifulSoup`的解析库，推荐使用`lxml`，如果出现乱码的情况下，可以使用`html.parser`；
- `BeautifulSoup`的标签选择筛选方法，虽然弱但是速度快；
- 推荐使用`find_all()`,`find()`方法搜索标签，当然如果对css选择器熟悉，推荐使用`.select()`方法；
- `get_text()`方法获取标签文本内容，`get[attrs]`方法获取标签属性值。

本篇我们就基本上整理了`BeautifulSoup`的常用方法。如果大家希望了解更高级的用法，可以查看`BeautifulSoup`的官方文档。

最后，大家可以结合`Requests`库写出自己的爬虫吧。

谢谢阅读
