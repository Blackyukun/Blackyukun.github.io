---
title: Python爬虫(10):Selenium+PhantomJS基本操作
date: 2017-07-26 17:07:22
tags:
  - Python
  - 爬虫
categories: Python
keywords:
  - Python
  - 爬虫
  - Selenium
urltitle: python-spider-Selenium-PhantomJS-basic
---
大家好，这篇文章我们来看一下`Selenium`库结合`PhantomJs`，`Chrome`等一些浏览器的操作。那么我们在之前的文章中，有提到过`Selenium`库和`PhantomJ`，说他们结合使用是万能的利器。那么，他们真的那么厉害吗，我们一起来看看`Selenium`库的用法吧。
<!-- more -->

## 什么是Selenium

`Selenium`是一个自动化测试工具，支持包括`Chrome`，`Firefox`，`Safari`，`PhantomJs`等一些浏览器。如果用于爬虫中，我们主要用来解决一些`JavaScript`渲染的问题。

我们在使用`Requests`库去请求一些网页的时候，比如 163music，我们获得的响应数据呢，并不全是我们在浏览器中看到的信息。他可能是通过`js`渲染出来的。那么，我们如果使用`Selenium`库，就不会再去关心如何去解决这种问题了。

因为我们的浏览器，比如`PhantomJs`，他就是一个无界面的浏览器，他用来渲染解析`js`，而`Selenium`库就负责给浏览器发送一些命令，模拟一些比如下拉，拖拽，翻页，输入表单等动作。这样他们两个结合，对于那些 JS 的渲染问题是不是完美解决了。

> 文档地址：[here](http://selenium-python.readthedocs.io/index.html)

### 注意

虽然`Selenium`库加上`PhantomJs`很好用，但是他毕竟是驱动一个浏览器，然后获取数据。所以在我们使用中，会发现他并没有我们使用一些解析库速度快。这其实就是他的弊端，所以我还是建议大家，不到实在找不到解决办法的时候，不去使用他们。

## 安装准备

`pip`直接安装`Selenium`库：`pip install selenium`

浏览器驱动的安装：

- `Chrome`浏览器驱动：[地址](https://sites.google.com/a/chromium.org/chromedriver/downloads)
- `PhantomJs`浏览器驱动：[地址](http://phantomjs.org/download.html)

**我们需要把安装好的浏览器驱动配置到我们的环境变量。对于Windows用户，配置环境变量比较麻烦。我们需要找到下载好的驱动位置，然后复制他的文件位置，见他粘贴到环境变量即可。**

## 使用样例

```Python
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.support.wait import WebDriverWait


browser = webdriver.Chrome()

try:
    browser.get('http://www.yukunweb.com')
    input = browser.find_element_by_id('s')
    input.send_keys('Python')
    input.send_keys(Keys.ENTER)
    wait = WebDriverWait(browser, 10)
    wait.until(EC.presence_of_element_located((By.ID, 'main')))
    print(browser.current_url)
    print(browser.page_source)
finally:
    browser.close()
```

如果我们运行上面的代码，会看到本地打开了一个`Chrome`浏览器，然后在浏览器地址栏输入了我的博客网址，然后他会自动的在搜索栏输入‘Python’，并且点击了回车搜索。并且将结果页的`url`和源代码打印出来。

我们的例子都是使用`Chrome`浏览器来操作，因为`PhantomJs`是无界面的，不方便查看到效果。如果大家运行错误的话，一般情况是浏览器并没有打开，那么应该是大家没有安装好`Chrome`浏览器，或者没有将驱动配置环境变量。

_那么这几行代码究竟是什么意思呢，我们究竟赋予了什么指令呢？_

### 声明浏览器对象

```Python
from selenium import webdriver

browser = webdriver.Chrome()
# 声明其他浏览器
browser = webdriver.PhantomJs()
browser = webdriver.Firefox()</pre>
这就相当于我们调用了Selenium库的webdriver方法，实例化一个Chrome浏览器给我们调用。
<h2>访问页面</h2>
<pre class="lang:python decode:true ">from selenium import webdriver
 
browser = webdriver.Chrome()
browser.get('http://www.yukunweb.com')
```

我们将要访问的`url`传给`get`方法。调用浏览器访问`url`。

### 查找元素

```Python
input = browser.find_element_by_id('s')
```

这句代码调用`find_element_by_id`方法，顾名思义，就是查找`id`为‘s’的标签，那么如果是操作`class`为‘s’的话，就是`find_element_by_class('s')`。

当然，我们还可以使用 CSS选择器和`xpath`选择器查找元素：

```Python
input = browser.find_element_by_css_selector("#s")
print(input)
input = browser.find_element_by_xpath('//*[@id="s"]')
print(input)
```

通过打印结果，可以看到不管使用什么选择器，查找结果都是一样的。下面是一些查找`api`：

- find_element_by_name
- find_element_by_xpath
- find_element_by_link_text
- find_element_by_partial_link_text
- find_element_by_tag_name
- find_element_by_class_name
- find_element_by_css_selector

### 查找多个元素
如果我们查找的元素是网页中的`li`标签，是很多的元素。那么我们的查找方式和单个元素是相同的，只是对于查找的`api`我们需要在`element`后面加个复数形式 s。即是：

- find_elements_by_name
- find_elements_by_xpath
- find_elements_by_link_text
- find_elements_by_partial_link_text
- find_elements_by_tag_name
- find_elements_by_class_name
- find_elements_by_css_selector

### 元素交互操作

即是对于我们获取的元素下达指令，调用交互的方法。

```Python
browser.get('http://www.yukunweb.com')
input = browser.find_element_by_id('s')
input.send_keys('Python')
input.send_keys(Keys.ENTER)
```

这段代码中，我们首先查找到了`id`为‘s’的元素，然后传给他‘Python’值，然后调用交互方法，敲了回车。

当然，在大多是情况下，我们不能直接使用敲击回车的方法，因为我们不确定是不是敲了回车，表单就提交了。我们需要使用查找器查找到提交按钮元素，然后模拟点击:

```Python
button = browser.find_element_by_class_name('xxxx')
button.click()
# 清除表单信息
button.clear()
```

那么，我们可以看到在模拟登陆时候，直接让我们手动的输入账号，密码，如果有验证码的话直接给一个`input`方法，我们手动输入验证码传给表单，是不是很简单的就模拟登录了了。

### 交互动作

元素交互动作与上面的操作是不同的。上面的操作需要获得一个特定的元素。然后对这个特定的元素调用一些指令，才可以完成交互。而这个交互是将这些动作附加到动作链中串行执行。

我们以拖拽元素为例(我们需要导入`ACtionChains`方法)：

```Python
rom selenium import webdriver
from selenium.webdriver import ActionChains

browser = webdriver.Chrome()

browser.get(url)
source = browser.find_element_by_name("source")
target = browser.find_element_by_name("target")
actions = ActionChains(browser)
actions.drag_and_drop(source, target).perform()
```

这里的`sourcs`是我们要拖拽的元素，我们使用查找器找到他，`target`就是我们要拖拽到的位置元素。然后调用`ActionChains`方法，实现拖拽操作。

更多的操作可以查看文档：[here](http://selenium-python.readthedocs.io/api.html#module-selenium.webdriver.common.action_chains)

### 执行JavaScript

有些动作呢，`Selenium`库并没有为我们提供特定的`api`，比如说将浏览器进度条下拉，这个实现起来是很难的。那么我们就可以通过让`Selenium`执行`JS`来实现进度条的下拉，这个得需要一些`js`的知识，不过还是很简单的。

```Python
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('http://www.yukunweb.com')
browser.execute_script('window.scrollTo(0, document.body.scrollHeight)')
browser.execute_script('alert("到达底部")')
```

这就相当于我们将一些JS命令传给`Selenium`的`execute_script`这个`api`，我们运行就可以看到浏览器下拉到底部，然后弹出会话框。

### 获取元素文本值

如果我们查找得到一个元素，我们要怎样获得元素的一些属性和文本信息呢？

```Python
from selenium import webdriver

browser = webdriver.Chrome()

browser.get('http://www.yukunweb.com')
name = browser.find_element_by_css_selector('#kratos-logo &gt; a')
print(name.text)
print(name.get_attribute('href'))
```

运行结果可以看到，他打印出了‘意外’和他的url。

### Frame框架

有些网页在我们直接使用`Selenium`驱动浏览器打印源码的时候，并没有如期获得想要的数据，那在我们查看网页源码的时候，可以看到网页的`iframe`标签包裹的一个一个的框架。那么这就需要我们请求对应框架，拿到源码了。

我们以网易云音乐的歌手栏为例。

```Python
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://music.163.com/#/discover/artist/signed/')

print(page_source)
```

可以查看结果，并没有我们想要的信息。

```Python
from selenium import webdriver

browser = webdriver.Chrome()
browser.get('https://music.163.com/#/discover/artist/signed/')
browser.switch_to.frame('contentFrame')

print(page_source)
```

这次打印，我们就可以看到我们需要的信息了，是不是很简单。

### 显示等待

在文章开始的时候，我们运行的那段代码中有一段代码是不是还没有说。那就是我们命令浏览器等待的操作。

等待有两种方式，一种是隐士等待，一种是显示等待。当使用了隐士等待执行时，如果浏览器没有找到指定元素，将继续等待，如果超出设定时间就会抛出找不到元素的异常。而大多数情况我们建议使用显示等待。

显示等待是你指定一个等待的条件，还指定一个最长等待时间。那么程序会在最长等待时间内，判断条件是否成立，如果成立，立即返回。如果不成立，他会一直等待，直到最长等待时间结束，如果条件仍然不满足，就返回异常。

```Python
wait = WebDriverWait(browser, 10)
wait.until(EC.presence_of_element_located((By.ID, 'main')))
```

这里的`By.ID`方法实际上就是一个查找的万能方法，而我们直接查找或者使用`CSS`、`xpath`查找足够满足，我也不过多介绍，想要了解可以查看官方文档。

这里是知道查找到`id`为‘main’就返回。

显示等待的一些条件还有：

- title_is 标题是某内容
- title_contains 标题包含某内容
- presence_of_element_located 元素加载出，传入定位元组，如(By.ID, 'p')
- visibility_of_element_located 元素可见，传入定位元组
- visibility_of_element_located 元素可见，传入定位元组
- visibility_of_element_located 元素可见，传入定位元组
- visibility_of 可见，传入元素对象
- presence_of_all_elements_located 所有元素加载出
- text_to_be_present_in_element 某个元素文本包含某文字
- text_to_be_present_in_element_value 某个元素值包含某文字
- frame_to_be_available_and_switch_to_it frame加载并切换
- invisibility_of_element_located 元素不可见
- element_to_be_clickable 元素可点击
- staleness_of 判断一个元素是否仍在DOM，可判断页面是否已经刷新
- element_to_be_selected 元素可选择，传元素对象
- element_located_to_be_selected 元素可选择，传入定位元组
- element_selection_state_to_be 传入元素对象以及状态，相等返回True，否则返回False
- element_located_selection_state_to_be 传入定位元组以及状态，相等返回True，否则返回False
- alert_is_present 是否出现Alert

### 窗口选择
如果我们在表单输入关键词，提交表单后浏览器新打开了一个窗口，那么我们要怎么去操作新的窗口呢？索性`Selenium`为我们提供了对应的`api`.

```Python
import time
from selenium import webdriver
from selenium.webdriver.common.keys import Keys

browser = webdriver.Chrome()
browser.get('http://www.23us.cc/')
input = browser.find_element_by_id('bdcs-search-form-input')
input.send_keys('斗破苍穹')
input.send_keys(Keys.ENTER)
browser.switch_to_window(browser.window_handles[1])
print(browser.current_url)
time.sleep(1)
browser.switch_to_window(browser.window_handles[0])
print(browser.current_url)
```

通过打印结果，不难看出先打印了搜索结果窗口`url`，然后打印了索引页`url`。要注意窗口的索引是从 0 开始的哦，这个大家都明白。

### 异常处理

异常处理和普通的异常处理一样，没有什么要说的，大家自己查看官方异常 api.[地址](http://selenium-python.readthedocs.io/api.html#module-selenium.common.exceptions)

## 最后

好了，通过本篇文章希望大家可以基本上了解`Selenium`库结合浏览器驱动的一些使用方法。我们例子里使用的是`Chrome`，但是大家在实际的代码里最好是使用`PhantomJs`，因为他是无界面的，运行起来相对好一点。

文章开始说过一般情况下不建议大家使用`Selenium`，因为他很慢。但是即使是慢，也很爽啊，是不是。

谢谢阅读
