---
title: Python爬虫(2):Requests的基本用法
date: 2017-05-29 23:08:56
tags:
  - Python
  - 爬虫
categories: Python
keywords:
  - Python
  - 爬虫
  - Requests
urltitle: python-spider-Requests-basic
---
虽然Python有内置的`urllib`库，可以实现网络的请求，但是我并不推荐。因为`urllib`在很多时候使用起来不方便，比如加一个代理，处理`Cookie`时API都很繁琐，再比如发送一个`POST`请求也很麻烦。

而`Requests`就相当于`urllib`的升级版本，简化了`urllib`的使用方法。有了`Requests`，我们可以用几句代码实现代理的设置，`Cookie`的设置，非常方便。下面我就给大家整理了`Requests`库的使用方法和细节。详细可以参考`Requests`[官方文档](http://docs.python-requests.org/zh_CN/latest/)。
<!-- more -->

## 什么是Requests？

`Requests`是`Python`语言编写，基于`urllib3`，采用`Apache2 Licensed`开源协议的HTTP库。

它比`urllib`更加方便，可以节约我们大量的工作，完全满足`HTTP`测试需求。是`Python`实现的简单易用的`HTTP`库。

安装也很简单：`pip install requests`

## Requests的语法操作

### 1.实例引入

```Python
import requests

response = requests.get('http://www.baidu.com/')
print(response.status_code)
print(type(response.text))
print(response.text)
print(response.cookies)
```

运行结果：
```
200
<class 'str'>
 
# ...HTML网页源码..
<RequestsCookieJar[]>
```

可以看到，我们非常方便的就获取到了`Cookies`.

### 2.各种请求方式

```Python
import requests

requests.get('http://httpbin.org/get') # 发送get请求
requests.post('http://httpbin.org/post') # 发送post请求，只要调用post方法，传入一个url参数
requests.put('http://httpbin.org/put')
requests.delete('http://httpbin.org/delete')
```

官方文档里提供的这个网址足够我们测试这些请求方式了。

## 请求

### 1.基本GET请求

```Python
import requests

resp = requests.get('http://httpbin.org/get')
print(resp.text)
```

这个我们前面有使用过，也是最常用的方法。运行成功就可以看到网页的源码了。

### 2.带参数的GET请求

```Python
import requests

data = {
    'name' : 'jack',
    'age' : 20
}
resp = requests.get('http://httpbin.org/get', params=data)
print(resp.text)
```

传入参数只需要我们把数据生成一个字典，然后调用`params`参数，赋值给他就可以，是不是很方便。

### 3.解析json

```Python
import requests
import json

resp = requests.get('http://httpbin.org/get')
print(resp.text)
print(resp.json())
print(json.loads(resp.text))
print(type(resp.json()))
```

运行结果：

![image](http://imgout.ph.126.net/55901018/QQCDBCC6AC20170529120909.jpg)

可以看出`Requests`的`jaon`解析和`json`的`loads`方法解析出来的结果是完全一样的。所以`Requests`可以很方便的解析`json`数据。

### 4.获取二进制数据

```Python
import requests

resp = requests.get('http://www.baidu.com/img/baidu_jgylogo3.gif')
print(resp.content)
print(resp.text)
```

运行成功我们可以看到`content`方法获取的图片页面源码是二进制数据，而`text`获取的则是字符串代码。显然获取图片这种二进制数据需要使用`content`方法。

```Python
with open('logo.gif','wb') as f:
    f.write(resp.content)
```

这样我们就保存了图片，我们可以在文件夹下看到这张图片。

### 5.添加headers

```Python
import requests

headers = {'User-Agent':'Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/55.0.2883.87 Safari/537.36'}
resp = requests.get('http://www.baidu.com', headers=headers)
print(resp.text)
```

有些网页如果我们直接去请求的话，他会查看请求的对象是不是浏览器，如果没有浏览器信息就会禁止我们爬虫的访问，这个时候我们就要给爬虫加一个`headers`，加一个浏览器的`user-agent`信息。这样我们就可以正常访问了。如果有的伙伴不知道怎么得到`User-Agent`，可以打开浏览器的审查元素，找到`network`，随便点击一个链接就可以看到`User-Agent`的信息了。

![image](http://imgout.ph.126.net/55898004/QQCDBCC6AC20170529131930.jpg)

### 6.基本POST请求

```Python
import requests

data = {
    'name' : 'jack',
    'age' : 20
}
resp = requests.post('http://httpbin.org/post', data=data)
print(resp.text)
```

一个`POST`必然是要有一个`Form Data`的表单提交的，我们只要把信息传给`data`参数就可以了。一个`POST`请求只需要调用`post`方法，是不是特别方便呢。如果不觉得方便的话，可以去参考`urllib`的使用方法。

## 响应

### 1.response属性

```Python
import requests

response = requests.get('http://www.baidu.com/')
print(type(response.status_code)) # 状态码
print(type(response.text)) # 网页源码
print(type(response.headers)) # 头部信息
print(type(response.cookies)) # Cookie
print(type(response.url)) # 请求的url
print(type(response.history)) # 访问的历史记录
```

获取这些信息只需要简单的调用就可以实现了。

### 2.状态码判断

```Python
>>>import requests
 
>>>response = requests.get('http://www.baidu.com/')
>>>exit() if not resp.status_code == 200 else print('Sucessful')
Sucessful
```

如果发送了一个错误请求(一个4XX客户端错误，或者5XX服务器错误响应)，我们可以通过 `Response.raise_for_status()` 来抛出异常：

```Python
>>>bad_r = requests.get('http://httpbin.org/status/404')
>>>bad_r.status_code
404
>>>bad_r.raise_for_status()
Traceback (most recent call last):
  File "requests/models.py", line 832, in raise_for_status
    raise http_error
requests.exceptions.HTTPError: 404 Client Error
```

好了，这篇文章我们了解了`Requests`库的基本语法操作，相信大家对`Requests`库的请求和响应已经很清楚了，大家完全可以抓取一些网页了。

纸上得来终觉浅，绝知此事要躬行，大家加油！
