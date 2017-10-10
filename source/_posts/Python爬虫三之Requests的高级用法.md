---
title: 	Python爬虫(3):Requests的高级用法
date: 2017-05-29 23:28:36
tags:
  - Python
  - 爬虫
categories: Python
keywords:
  - Python
  - 爬虫
  - Requests
urltitle: python-spider-Requests-advanced-usage
---
上一篇文章我们整理了`Requests`库的基本用法，相信大家已经经过爬取一些简单网页的练习，已经很熟练了。

这一篇文章我们来 看一下`Requests`库的高级操作。
<!-- more -->

## 高级操作

### 1.文件上传

```Python
import requests

files = {'file' : open('logo.gif','rb')}
resp = requests.post('http://httpbin.org/post', files=files)
print(resp.text)
```

文件上传的操作只要我们从文件夹中把文件读取出来，并且赋值给 files 参数，就可以了，打印出源代码我们就可以看待上传文件的字节流了。

### 2.获取Cookie

```Python
>>>import requests

>>>resp = requests.get('http://www.baidu.com')
>>>print(resp.cookies)
<RequestsCookieJar[]>
>>>for key, value in resp.cookies.items():
...    print(key + '=' + value)
BDORZ=27315
```

我们可以通过获取字典的键值对来查看`cookie`.

### 3.会话维持
我们获得到了`cookie`就可以做一个会话维持，可以维持一个登录的状态，也就是做模拟登录。我们来看实现方式：

```Python
import requests

s = requests.Session()
s.get('http://httpbin.org/cookies/set/number/123456789') # 设置了一个cookie
resp = s.get('http://httpbin.org/cookies')
print(resp.text)
```

这就相当于模拟了一个会话，比如做登陆验证，可以用`session`，POST 一下，登陆一下，然后保持会话信息，在访问登录过页面的话，就可以正常获取登录后的页面了。如果你要模拟登录，可以通过申明`Session`对象，再用`Session`对象发起两次get请求，那么这两次请求相当于在一个浏览器里面，先访问`set cookie`页面，在访问`get cookie`页面。当然，`cookie`是自动处理的，不需要担心写一些处理`cookies`的方法。

建议模拟登录用`requests`的`Session`对象。

### 4.SSL证书验证

`Requests`可以为 HTTPS 请求验证 SSL 证书，就像 web浏览器一样。要想检查某个主机的 SSL证书，你可以使用 verify参数:

```Python
>>>import requests

>>>requests.get('https://kennethreitz.com', verify=True) # verify参数默认值为True
requests.exceptions.SSLError: hostname 'kennethreitz.com' doesn't match either of '*.herokuapp.com', 'herokuapp.com'
```

如果不想他报这个错误，我们可以把参数`verify`的值设为`False`.运行后发现程序没有报错，但是会出现警告信息，警告我们要验证 SSL证书。如果要消除这个警告，我们需要调用原生包：

```Python
>>>import requests
>>>from requests.packages import urllib3

urllib3.disable_warnings()
>>>requests.get('https://kennethreitz.com', verify=False)
```

我们还可以自己指定一个证书：

```Python
>>>import requests

>>>resp = requests.get('https://kennethreitz.com', cert=('/path/server.crt', '/path/key'))
>>>print(resp.status_code)
200
```

### 5.代理设置

有些网站会限制 IP 访问频率，超过频率就断开连接。这个时候我们就需要使用到代理，我们可以通过为任意请求方式提供`proxies`参数来配置单个请求。

```Python
import requests

proxies = {
    "http": "http://10.10.1.10:3128",
    "https": "http://10.10.1.10:1080",
}
resp = requests.get('http://www.baidu.com', proxies=proxies)
print(resp.status_code)
```

也可以通过环境变量 `HTTP_PROXY` 和 `HTTPS_PROXY` 来配置代理。
有些代理需要加上用户名和密码的，代理可以使用`http://user:password@host/`语法，比如：

```Python
proxies = {
    "http": "http://user:pass@10.10.1.10:3128/",
}
```

除了基本的 HTTP代理，`Requests`还支持`SOCKS`协议的代理，如果需要用的，可以安装带三方库：

```
$ pip install requests[socks]
```

安装好依赖以后，使用 SOCKS 代理和使用 HTTP 代理一样简单：

```Python
proxies = {
    "http": 'socks5://user:pass@host:port',
    "https": 'socks5://user:pass@host:port'
}
```

### 6.超时设置

超时设置就是设置请求的时间，如果在规定的时间内没有返回应答，就抛出异常.


```Python
import requests

resp = requests.get('http://www.baidu.com', timeout=0.5)
print(resp.status_code)
```

如果在0.5秒内没有返回，就会报出`ReadTimeout`的异常。
如果远端服务器很慢，你可以让`Request`永远等待，传入一个`None`作为`timeout`值，然后就冲咖啡去吧。

### 7.认证设置

有一些网站在访问的时候需要我们输入用户名和密码，那么这种网站我们要怎样处理呢。

```Python
import requests
from requests.auth import HTTPBasicAuth

resp = requests.get(url, auth=HTTPBasicAuth('username','password'))
print(resp.status_code)
```

调用`HTTPBasicAuth`类，直接传入用户名和密码就可以了。

### 8.异常处理

如果你遇到无法访问的网站，或者是你的网速不够快，你的访问超时，就会导致程序的中断。显然我们在实际的抓取中不愿意看到爬取到一半的程序突然中断的情况，那么我们能够避免这种程序中断的情况吗，答案是肯定的：

```Python
import requests
from requests.exceptions import ReadTimeout, ConnectionError, RequestException

try:
    resp = requests.get('http://httpbin.org/get', timeout=0.5)
    print(resp.status_code)
except ReadTimeout： # 访问超时的错误
    print('Timeout')
except ConnectionError: # 网络中断连接错误
    print('Connect error')
except RequestException: # 父类错误
    print('Error')
```

这样我们就可以把`requests`抓取过程中常见的异常都处理捕获了，捕获错误应该先捕获子类异常在捕获父类异常，这样做能够更加直观清楚的应对程序中出现的错误了。

如果我们能够自己捕获了这些异常，就可以保证我们的爬虫一直运行了。

好了，`Requests`的大部分用法已经全部说完了，大家是否已经学会了这门屠龙之术了呢。快找个网页练练手吧。
