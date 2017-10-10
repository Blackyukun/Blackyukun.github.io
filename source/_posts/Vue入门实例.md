---
title: Vue入门实例
date: 2017-10-08 17:31:44
tags: 
  - JavaScript
  - Vue.js
categories: JavaScript
keywords: 
  - JavaScript
  - Vue
  - 清单应用
urltitle: Vue-instance
---
最近`React`框架的一些问题，基本上大多`React`使用者都停止使用。无疑`Vue`将会更加受人欢迎。

## 项目准备

对于不清楚`Vue`是什么或者不知道如何使用的伙伴可以自行参考`Vue` [文档](http://vue.sike.io/v2/guide/)。

安装`Vue`最简单的方法就是找一个国内`CDN`下载：[here](https://cdn.bootcss.com/vue/2.4.4/vue.js)
<!-- more -->

我们的项目结构为：

```
app\ 项目文件夹
    css\ 存放css文件
        main.css
    js\ 存放js文件
        main.js
    lib\ 存放外部引入文件
        vue.js
    index.html
```

## 计划清单应用

对于学习一项技能，最好的方法就是用于实际项目。边动手边巩固基础，能很好的加深自己的理解。这是一个清单应用，使用纯前端技能实现，没有使用数据库等操作。

![todo](http://imgout.ph.126.net/58058056/todo.jpg)

大致的功能就是我们可以将未来要做的事情，一条一条理清列出来，写到要做的列表中。可以写详情也可以添加提醒时间，提醒事件。如果完成了任务就点击完成，删除就点删除，应用的本身不是很复杂，理清逻辑就可以做出来。但是作为一个练手项目，用于加深`Vue`重要概念的理解，也让我们知道`Vue`在一个项目中应该如何使用。

[DEMO](https://blackyukun.github.io/todoPlan/)

## 页面基本结构

既然是一个网页应用，那就得有一个页面的结果和样式。这一点大家可以自己写自己的样式，我们先简单的确定他的基本结构，样式我们可以在后面自己添加。

```JavaScript
<!DOCTYPE html>
<html lang="zh_CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>计划</title>
    <style type="text/css">
        body{margin:0;}
        .float-left{
            float: left;
        }
        .float-right{
            float: right;
        }
        input{
            width: 70%;
            padding: 10px 10px;
            background: inherit;
        }
        #header{
            background: #27ae60;
            padding: 10px 0;
            margin-bottom: 20px;
        }
        .navbar{
            color: #fff;
            font-weight: 600;
            text-align: center;
        }
        #main{
            width: 50%;
            margin: 0 auto;
        }
        .wrap{
            text-align: center;
        }
        .todo,.done{
            border: 1px solid #ccc;
            border-radius: 3px;
            -webkit-box-shadow: 0 1px 3px rgba(0,0,0,.1);
            box-shadow: 0 1px 3px rgba(0,0,0,.1);
            -webkit-box-sizing: border-box;
            box-sizing: border-box;
        }
        .item{
            background: #fff;
            border-bottom: 1px solid #ccc;
            padding: 10px 10px;
            text-align: left;
        }
    </style>
</head>
<body>
    <div id="app">
        <div id="header">
            <div class="navbar">
                搞事情
            </div>
        </div>
        <div id="main">
            <div class="wrap">
                <div class="">
                    <h2>计划列表</h2>
                </div>
            </div>
            <div class="content">
                <p class="title">未完成</p>
                <div class="todo">
                    <form action="">
                        <input id="todoInput" type="text" placeholder="你想搞事吗。。。" autocomplete="off">
                        <button type="submit" class="inputBtn" title="添加">添加</button>
                    </form>

                    <div class="wrap">
                        <div class="plan-list">
                            <div class="item">
                                <button class="float-left" title="完成">完成</button>
                                啊啊啊啊
                                <button class="float-right" title="删除">删除</button>
                                <button class="float-right" title="修改">修改</button>
                            </div>

                        </div>
                    </div>
                </div>
                        
                <p class="title">已完成</p>
                <div class="done">
                    <div class="wrap">
                        <div class="plan-list">
                            <div class="item">
                                <button class="float-left" title="未完成">
                                    未完成
                                </button>
                                啦啦啦
                                <button class="float-right" title="删除">
                                    删除
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                
            </div>
        </div>
    </div>
    <script src="lib/vue.js"></script>
    <script>
        
    </script>

</body>
</html>
```
我们先确定他的页面结构，至于样式，我们后面在改。下面就是应用的功能了。

## 增加任务

基于页面的框架，我们来写自己的功能。首先我们先实例一个`Vue`用来存放我们的应用数据。由于项目是一个单例应用，实例一个就可以。

```JavaScript
<script>
    new Vue({
		el: '#app',
		data: {
		    todoList: [],
		    current: {},
		},
		methods: {
		    // 添加
		    add: function() {
			// ...
		    },

		    // 删除
		    remove: function() {
			// ...
		    },

		    // 更新
		    update: function() {
			// ...
		    },
		},
    });
</script>
```

上面的代码我们定义了`todoList`来存放我们的清单列表，而`current`是我们表单内输入的内容，是一个可能包含标题，详情内容，提醒时间的对象，存放在`todoList`中。在`vue`实例的`methods`中，我们定义了增，删，改的应用功能。我们首先来看一下添加任务的功能：

```JavaScript
<form action="" @submit.prevent="add">
    <input id="todoInput" autocomplete="off" type="text" placeholder="你想搞事吗。。。" v-model="current.title" />
    <button title="添加" type="submit">添加</button>
</form>
```

我们将`form`的提交指向`vue`实例中的`add`方法，`@`是`v-on`指令的缩写，`.prevent`是修饰符，相当于告诉指令对于提交事件调用`event.preventDefault()`，也就是取消掉提交后的自动刷新。`input`中的`v-model`指令指向`current`数据，由于`current`是一个对象，我们指向他的标题，因为他可能还有详情`current.detail`，提醒时间`current.alerted`，完成状态...

我们接着要将上面未完成下的“啊啊啊啊啊”改为接收vue数据的代码，但是由于`todoList`是一个集合，我们要使用`vue`的循环：

```JavaScript
<div class="plan-list" v-for="todo in todoList">
    <div class="item">
        <button class="float-left" title="完成">完成</button>
        {{ todo.title }}
        <button class="float-right" title="删除">删除</button>
        <button class="float-right" title="修改">修改</button>
    </div>
</div>
```

这样我们就应该写`js`的功能了。

```JavaScript
add: function() {
    this.todoList.push(this.current);
},
```

这样我们可以在页面表单中输入字段提交，就可以看到下方的列表更新出了提交的内容。但是呢，当我们多次提交就可以看到意料之外的结果。每次提交后列表中的结果就会被全部替换为新的内容。

这实际上是我们add函数中，`this.current`一直是一个引用的存在，并没有将这个数据拷贝出来，所以我们每次提交，列表中都是提交的`current`。这样我们需要做的就是在每次表单数据提交的时候，对`current`的数据拷贝一次，然后在传到list中。

```JavaScript
// 由于后面会多次使用拷贝，我们封装一下
function copy(obj){
    var copyVal = Object.assign({}, obj);
    return copyVal;
},

// 加入拷贝
add: function() {
    var todo = copy(this.current);
    this.todoList.push(todo);
},
```

但是这个函数似乎还有问题，当我们添加一个空的数据进去提交，他依然能添加成功。如果我们打开浏览器控制台，`console.log`打印一下结果，他依然会有输出。这肯定不是我们希望的，因为这样没有意义。所以我们需要判断一下，提交的数据是否为空，如果为空我们直接返回，不继续处理下去。

```JavaScript
add: function() {
    var title = this.current.title;
    if (!title && title !== 0) return; // 不要过滤掉0，return中止函数

    var todo = copy(this.current);
    this.todoList.push(todo);
    console.log(this.todoList);
},
```

我们再次测试，输入空的数据提交，在看控制台，就看不到打印输出了。

## 更新任务

比如你拟了一个计划“写作业”，但是你突然向更改它为“LOL”，那么我们就需要有更新功能。当我们点击页面中更新按钮的时候，表单中会自动显示本条计划“写作业”。然后我们将表单内容改为“LOL”，提交，列表中本条计划可以成功更改。

那这个实现起来很简单啊，我们给更新按钮加一个`v-on`点击指令，将`current`的值改为`todo`不就可以了吗。

```JavaScript
<button class="float-right" title="修改" @click="current = todo">修改</button>
```

我们在页面中实际操作一下，发现事情并没有这么简单，我们在更改表单中的内容过程中，列表中的`todo`也随着更改，并且在我们提交后，实际上又多添加了一条`todo`。这实际上是因为提交过程中我们的应用不知道他是更新还是添加任务。这就需要做一个判断，所以我们的添加任务和更新任务实际上是同一个任务，只要做一个更新还是不是更新的判断就可以了。如果是更新操作，我们直接更改`todoList`中的内容，如果不是就依然执行添加操作。那么我们写一个`add`和`update`函数的结合函数，删掉`add`和`update`函数。

那么在写`merge`函数之前我们需要明确如何判断是否是更新操作。肯定有伙伴想到了每一个提交的`todo`任务不是都有一个索引吗，我们直接修改`todoList`中的对应索引项不就可以了吗。但是随着列表中的增删改查，排序等操作，每一个`todo`的索引是不固定的。那这显然不行。我们都知道在数据库中每一条数据都有一个固定的`id`项，用来确定每一项。那我们也可以给每一个`todo`添加一个`id`。

```JavaScript
// 给每个计划添加id键
nextId: function() {
    return this.todoList.length + 1;
},

// 根据id查找index
find_index: function(id) {
    return this.todoList.findIndex(function(item) {
    return item.id == id;
    });
},
```

我建议大家将与对应功能之外的方法封装出来，保证一个函数内部的整洁，逻辑清晰。接下来写`merge`函数：

```JavaScript
merge: function() {
    var isUpdate, id;
    isUpdate = id = this.current.id;
    if (isUpdate) {
    // 先得到索引，因为在vue中我们不能直接写js语法更改数组，需要使用vue的set方法
    var index = this.find_index(id);
    Vue.set(this.todoList, index, copy(this.current));
    } else{
        var title = this.current.title;
        if (!title && title !== 0) return;

        var todo = copy(this.current);
        todo.id = this.nextId();
        this.todoList.push(todo);
},
```

`merge`函数的逻辑有了，但是我们点击更新后，修改表单内容下方列表中内容也跟着改动。这样就很让人误解，到底需不需要回车提交呢？这样显然不好，我们其实只要拷贝一下todo副本传给`current`就好了：

```JavaScript
// 更新todo副本，不使用current=todo
setCurrent: function(todo) {
    this.current = copy(todo);
},

更新html内容：
<button class="float-right" title="修改" @click="setCurrent(todo)">修改</button>
```

有了这个方法我们还可以做很多事，比如每次提交后，由于取消了默认刷新，但是我们还是想要表单中的内容自动清空，怎么办？直接给setCurrent函数传入空对象就好了：

```JavaScript
// 回车后清空表单
resetCurrent: function() {
    this.setCurrent({});
},
```

接下来就是删除功能了。

## 删除任务

删除任务实际上就是将`todo`从`list`中删除。那么最简单的操作就是获得`todo`任务的索引`index`，然后数组删除该索引对应的项。

```JavaScript
remove: function(id) {
    var index = this.find_index(id)
    this.todoList.splice(index, 1);
},
```

调用`splice`方法完成删除操作，很简单。

## 完成与未完成

增删改的功能都实现了，查功能本身就有，后面就是控制每个todo的完成与未完成的状态了。这个功能实际就是当我们点击完成按钮，该todo就会在未完成列表中消失，出现在下方的已完成列表中。

这样就需要我们给每一个todo对象添加`complete`键，点击完成按钮，`complete`为`true`，点击未完成就为`false`。这样逻辑很清楚，实现起来也很简单，我们先写他的vue方法。

```JavaScript
// 完成与未完成
toggleComplete: function(id) {
    var index = this.find_index(id);
    Vue.set(this.todoList[index], 'completed', !this.todoList[index].completed)
},

// html：
<div class="item">
...
<button class="float-left" title="完成" @click="toffleComplete(todo.id)">完成</button>
...
</div>
```

对于未完成列表的修改参照上方的内容，由于我很懒坐多余解释，大家自己写哦。

## 任务详情

给任务添加详细描述，这个其实不想写的，主要是懒，也是由于这里没什么好写的。大家可以自行按照自己的逻辑去写，有不明白的伙伴可以直接看源码，后面会留。

任务详情主要就是添加一个`textarea`表单，可以隐藏它，因为不是每一个任务都需要详情。当点击详情按钮时，弹出来。还可以添加一个提醒时间表单，分别使用`v-model`指令传给`current`对象的`.detail`和`alertedTime`。然后在`vue`的`merge`函数中将详情添加，保存`list`。

## 接入localStorage

我们的应用该有的功能都有了，但是当我们每次刷新后，写的todo全都没了，之前的操作全没了。看到这里，大家肯定会想，这个博主不是坑我了吗。这有个毛用啊？？？

由于我们说了使用纯前端的技能，我们没有后端，数据不能使用数据库，需要怎么做呢？这就需要使用前端存储数据的方式，[localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage)。

![localstorage](http://imgout.ph.126.net/58044102/todo2.jpg)

看他暴露的`api`还是很简单的，我们照着封装一下就是我们的了。

我们新建一个`myStorage.js`文件，封装代码如下：

```JavaScript
// 封装localStorage
(function() {
    window.ms = {
    set: set,
    get: get,
    };

    function set(key, val) {
        // 如果val是一个对象，toString()方法会出现问题，大家可以自己试试
        // 需要使用JSON方法将val变为json
        localStorage.setItem(key, JSON.stringify(val));
    },

    function get(key) {
        var json = localStorage.getItem(key);
        // 解析json
        if (json) {
            return JSON.parse(json);
        }
    }
})();
```

上面的封装其实将底层的`localStorage`暴露给我们的api封装了一下。大家可以自己测试他能否存储数据。接着就是对接我们的应用了。对接的逻辑很简单，大家想一下，是不是只要在每次添加或者删除后，调用`ms.set('todolist', todoList)`就可以了。没错，就是这么简单。

![image](http://imgout.ph.126.net/58051084/todo3.jpg)

但是首先我们需要调用`vue`的挂载钩子`mounted`来实现，相信看过文档的伙伴都知道`mounted`。在每次应用挂载的时候，调用它。我们需要将`localStorage`里的值取出来。

```JavaScript
mounted: function() {
    this.todoList = ms.get('todoList') || this.todoList;
},
```

如果觉得在每次的添加和删除操作后调用`ms.set()`来保存数据不好，我们还可以使用`vue`中更巧妙的方法。

vue的`watch`函数，给他属性`todoList`,`deep`设为`true`。就是指不管`todoList`里面的嵌套多复杂，只要里面有变化，就调用`handler`函数。`handler`函数传入两个参数，一个新值，一个旧值。

```JavaScript
// 将每次改动传给localStorage
// 调用watch方法,每次todolist有改动就调用handler方法
watch: {
    todoList: {
        deep: true,
        handler: function(newVal, oldVal) {
            if (newVal) {
                ms.set('todoList', newVal);
            } else{
                ms.set('todoList', []);
            }
        },
    }
},
```

## 提醒与页面美化

我们前面添加的todo的详情和提醒时间，那么有了提醒时间我们就可以根据时间是否到达来设置提醒事件。比如`alert('时间到')`。代码如下，注意看注释。

```JavaScript
// 提醒
showAlerted: function() {
    // 一个函数中有回调函数，this指向问题要警惕
    var me = this;

    // 循环数组每一项的提醒时间，ele是每一项，i是索引
    this.todoList.forEach(function(ele, i) {
        var alertedTime = ele.datetime;
        // 如果没有提醒时间，或者已经提醒过了，直接返回
        if (!alertedTime || ele.alerted_confirmed) return;

        // 转化时间格式为多少秒
        var alertedTime = (new Date(alertedTime)).getTime();
        var now = (new Date()).getTime();
        // 判断是否到达事件
        if (now &amp;gt;= alertedTime) {
            var confirmed = confirm('时间到：' + ele.title + '\n' + '详情：' + ele.detail);
            Vue.set(me.todoList[i], 'alerted_confirmed', confirmed);
        }
    });
},
```

写好提醒函数，还有将他添加到`mounted`挂载中去，每次进入应用先看看是否有任务到达时间了，但是我们要让他一直去保持一个判断时间到没到的状态，需要设置一个`setInterval`。

```JavaScript
mounted: function() {
    var me = this;
    this.todoList = ms.get('todoList') || this.todoList;

    // 打开应用提醒,1秒一次执行
    setInterval(function() {
        me.showAlerted();
    },1000);
},
```

好了我们应用的功能这样基本实现了，至于页面的美化就交给大家自己完成了，懒得小伙伴可以用我的实现代码。

## 组件化

这是一个很简单的应用，让它实现组件化的吧，或许比他本身更复杂。但是为了加深对于`Vue`的理解，大家可以自行实现。不清楚的可以参考文档。

## 项目地址

[github](https://github.com/Blackyukun/todoPlan)


