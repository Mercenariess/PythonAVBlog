# Python爬虫 - XPath

# 简介

## XPath使用

XPath，全称 XML Path Language，即 XML 路径语言，它是一门在XML文档中查找信息的语言。XPath 最初设计是用来搜寻XML文档的，但是它同样适用于 HTML 文档的搜索。

## XPath概述

XPath 的选择功能十分强大，它提供了非常简洁明了的路径选择表达式，另外它还提供了超过 100 个内建函数用于字符串、数值、时间的匹配以及节点、序列的处理等等，几乎所有我们想要定位的节点都可以用XPath来选择。

> XPath 于 1999 年 11 月 16 日 成为 W3C 标准，它被设计为供 XSLT、XPointer 以及其他 XML 解析软件使用，更多的文档可以访问其官方网站：[https://www.w3.org/TR/xpath/](https://link.zhihu.com/?target=https%3A//www.w3.org/TR/xpath/)。

# 一、前言

通过前面的文章，我们已经知道了如何获取网页和下载文件，但是前面我们获取的网页都是未经处理的，冗余的信息太多，无法进行分析和利用

这一节我们就来学习怎么从网页中筛选自己需要的信息。



说到信息筛选我们立马就会想到正则表达式，不过今天我们不讲正则表达式。因为对于爬虫来讲，正则表达式太复杂对新手十分不友好，而且正则表达式的容错率差，网页有稍微的改动就得重新写匹配表达式，另外正则表达式可读性几乎没有。



当然，这并不是说正则不好，只是正则不适合爬虫和新手。其实正则是十分强大的，在后面的数据清洗里我们会用到正则。

# 二、XPath的安装和使用

## 1.安装lxml库

  在前面的文章中，我几乎从未提过某个库的安装，但是为什么在这里我要提出如何安装呢？原因很简单，Xpath只是 lxml 库中的一个模块，在 Python 很多库中都提供有 Xpath 的功能，但是最基本的还是 lxml 的这个库。效率最高。所以，你知道了，想要使用 Xpath 那么你就需要安装 lxml 库:

```python
pip install lxml
```

## 2.简单使用

```python
# 导入 etree类
from lxml import etree

# 作为示例的 html文本
html = '''<div class="container">
                <div class="row">
                    <div class="col">
                        <div class="card">
                            <div class="card-content">
                                <a href="#123333" class="box">
                                    点击我
                                </a>
                            </div>
                        </div>
                    </div>
                </div>
            </div>'''

# 对 html文本进行处理 获得一个_Element对象
dom = etree.HTML(html)

# 获取 a标签下的文本
a_text = dom.xpath('//div/div/div/div/div/a/text()')

print(a_text)

```

如果 `lxml`导入没有错误就说明安装成功了！

熟悉 html的朋友都知道在 html中所有的标签都是节点。一个 html文档是一个文档节点，一个文档节点包含一个节点树，也叫做 dom树。

> 节点树中的节点彼此拥有层级关系。
>
> 父（parent）、子（child）和同胞（sibling）等术语用于描述这些关系。父节点拥有子节点。同级的子节点被称为同胞（兄弟或姐妹）。
>
> - 在节点树中，顶端节点被称为根（root）
> - 每个节点都有父节点、除了根（它没有父节点）
> - 一个节点可拥有任意数量的子
> - 同胞是拥有相同父节点的节点

另外，我们把距离某个节点最近的子节点叫做它的直接子节点，如下图所示的 body和 head就是 html的直接子节点

![img](https://upload-images.jianshu.io/upload_images/8516750-352c94461c27aab5.gif?imageMogr2/auto-orient/strip|imageView2/2/w/486/format/webp)

了解了 html结构之后我们再来看 xpath的使用。

# 三、XPath语法使用

测试数据

```python
html_doc ="""
<html>
    <head></head>
    <body>
        <li>
            <a href="/book_16860.html" title="总裁的新鲜小妻">
                <img src="/16860s.jpg">
            </a>
            <img src="/kukuku/images/only.png" class="topss png_bg">
                <img src="abc.png" class="topss png_bg">
            <a href="/book_16860.html">总裁的新鲜小妻子</a>
        </li>
        <li>
            <a href="/book_16861.html" title="斗神天下">
                <img src="/16861s.jpg">
            </a>
            <img src="/kukuku/images/only.png" class="topss png_bg">
            <img src="def.png" class="topss png_bg">
            <a href="/book_16861.html">斗神天下</a>
        </li>

    </body>
</html>"""
```

首先 我们都知道 **从网页获取的都是字符串格式**。

那么如果我们想要通过 Xpath 来提取我们想要的内容，我们首先要生成 HTML 的 DOM 树：

```python
from lxml import etree

page = etree.HTML(html_doc)
```

## 1.xpath解析模式

- 解析网络资源etree.HTML()

```python
from lxml import etree
import requests
response = requests.get(url="http://httpbin.org/")
dom = etree.HTML(response.text, etree.HTMLPullParser())
ret = dom.xpath("/html")
```

- 解析本地文件资源

```python
from lxml import etree
dom = etree.HTML("./text.html", etree.HTMLPullParser())
ret = dom.xpath("/html")
print(ret)
```

## 2.路径查找

想要使用路径查找，那么我们就要知道Xpath语法 。

**XPath主要语法如下**：

|  表达式  |                            描述                            |
| :------: | :--------------------------------------------------------: |
| nodename |                  选取名为nodename的子节点                  |
|    /     |                        从根节点选取                        |
|    //    | 从匹配选择的当前节点选择文档中的节点，而不考虑它们的位置。 |
|    .     |                        选取当前节点                        |
|    …     |                    选取当前节点的父节点                    |
|    @     |                          选取属性                          |

### 2.1 查找当前的子节点

```python
dom = etree.HTML(html)
ret = dom.xpath("head")
print(ret)
```

返回结果：

```python
[<Element head at 0x10ae04e88>]
```

当前的节点位置是在 html，所以直接查询 `head` 节点可以查询出来**，`li` 是html的孙节点，如果查询 `li` 将返回空值**：

```python
dom = etree.HTML(html)
ret = dom.xpath("li")
print(ret)
```

返回结果：

```python
[]
```

### 2.2 从根节点进行查找 

`/`代表根节点

```python
dom = etree.HTML(html)
ret = dom.xpath("/html")
print(ret)
```

返回结果：

```python
[<Element html at 0x11a50dc08>]
```

从根节点进行查找，根节点下只有一个节点 `html` 节点，**所以从根节点查找只能查找到 `html`，如果查找其它内容将返回空列表：**

```python
dom = etree.HTML(html)
ret = dom.xpath("/li")
print(ret)
```

返回结果

```python
[]
```

### 2.3 从整个文档所有节点查找

```python
dom = etree.HTML(html)
ret = dom.xpath("//li")
print(ret)
```

返回结果：

```python
[<Element li at 0x1116dbdc8>, <Element li at 0x111bfab48>]
```

从整个文档进行查找可以查找整个文档中符合条件的节点，包括孙节点及以下。

### 2.4 获取但前节点的父节点

```python
dom = etree.HTML(html)
ret = dom.xpath("//li")[0].xpath("..")
print(ret)
```

返回结果:

```python
[<Element body at 0x115165bc8>]
```

另一种方式 获取父节点

```python
dom = etree.HTML(html)
ret = dom.xpath("//li/..")
print(ret)
```

返回结果：

```python
[<Element body at 0x10e8418c8>]
```

### 2.5 获取属性

```PYTHON
dom = etree.HTML(html)
ret = dom.xpath("//li/@href")
print(ret)
```

返回结果：

```python
['/book_16860.html', '/book_16860.html', '/book_16861.html', '/book_16861.html']
```

另一种方式获取：

```python
dom = etree.HTML(html)
ret = dom.xpath("//a")[0].xpath("@href")
print(ret)
```

## 3.节点查找

通过路径查找到节点以后，就需要从超找到的节点中选取我们需要的内容了。查找节点也有一些语法，如下：

|            表达式            |                      结果                       |
| :--------------------------: | :---------------------------------------------: |
|       nodename[index]        |          选取符合要求的第 index 个元素          |
|       nodename[last()]       |                选取最后一个元素                 |
|  nodename[position()< num]   |                选取前 num 个元素                |
|     nodename[@attribute]     |        选取带有属性名为 attribute 的元素        |
| nodename[@attribute=‘value’] | 选取带有属性名为 attribute 且 值为 value 的元素 |

### 3.1 选取第一个 img 节点的 src 属性

```python
dom = etree.HTML(html)
ret = dom.xpath("//li[1]/a[1]/img[1]/@src")
print(ret)
```

返回结果：

```python
['/16860s.jpg']
```

注意⚠️：**当你在选取一个节点的属性的时候，有一点需要注意**

>通过[index]选取出的节点是每一个符合条件的的第[index]个符合条件的元素。

看下面这个例子:

```python
dom = etree.HTML(html)
ret = dom.xpath("//li//img[1]")
print(ret)
```

返回结果:

```python
[<Element img at 0x11197ab88>, <Element img at 0x111eac648>, <Element img at 0x111ec9e88>, <Element img at 0x111ed9d08>]
```

你可以看到，**我们选取出了4个 img 节点**。原因很简单，看下面的代码和解释就能明白了：

```html
<html>
	<head></head>
	<body>
        <li>
            <a href="/book_16860.html" title="总裁的新鲜小妻">
                （一）<img src="/16860s.jpg">
            </a>
            （二）<img src="/kukuku/images/only.png" class="topss png_bg">
            （三）<img src="abc.png" class="topss png_bg">
            <a href="/book_16860.html">总裁的新鲜小妻子</a>
        </li>
        <li>
        	<a href="/book_16861.html" title="斗神天下">
                （四）<img src="/16861s.jpg">
            </a>
            （五）<img src="/kukuku/images/only.png" class="topss png_bg">
            （六）<img src="def.png" class="topss png_bg">
            <a href="/book_16861.html">斗神天下</a>
        </li>

    </body>
</html>
```

![image-20200106203755271](/Users/jiangchunsheng/Library/Application Support/typora-user-images/image-20200106203755271.png)

- xpath是一个树结构去选取的
- 每一个标签都是一个节点
- 代码中xpath表达式中一共有4个节点
- 在去选取每个节点标签的第一位子节点(img)

### 3.2 选取第二个元素开始的所有节点

```python
dom = etree.HTML(html)
ret = dom.xpath("//img[position()>1]/@src")
print(ret)
```

返回结果:

```python
['abc.png', 'def.png']
```

### 3.3 选取带有指定属性与指定值的节点

```python
dom = etree.HTML(html)
ret = dom.xpath("//a[@title='斗神天下']")
print(ret)
```

## 4.未知节点

当我们匹配时会出现路径不确定的情况，这个时候我们就要涉及到匹配未知节点。匹配未知节点也有对应的语法，如下：

| 通配符 |       描述       |
| :----: | :--------------: |
|   *    | 匹配任何元素节点 |
|   @*   | 匹配任何属性节点 |

### 4.1 匹配任何属性节点

```python
dom = etree.HTML(html)
ret = dom.xpath("//li/a/*")
print(ret)
```

返回结果:

```python
dom = etree.HTML(html)
ret = dom.xpath("//li/a/*")
print(ret)
```

/* 可以匹配任何节点。比如html 出现了一个。<ddv></ddv> 未知的标签我们不知道的就可用`*`去选取节点。

### 4.2 匹配任何元素节点

```python
dom = etree.HTML(html)
ret = dom.xpath("//li/a[@*]")
print(ret)
```

返回结果：

```python
[<Element a at 0x10f749c88>, <Element a at 0x10ed1d208>, <Element a at 0x10fc5fa08>, <Element a at 0x10fc97f48>]
```

## 5.获取节点中的文本

 通过 属性方法可以获取属性内的内容，但是位于节点之间的内容无法获取到，这个时候就可以通过 `text()` 与 `string()` 方法来获得其中的文本：

- 通过 `text()` 获取某个节点中的文本：

```python
dom = etree.HTML(html)
ret = dom.xpath("//li/a[2]/text()")
print(ret)
```

返回结果：

```python
['总裁的新鲜小妻子', '斗神天下']
```

- 通过 `string()` 获取某个节点中的文本：

```python
dom = etree.HTML(html)
ret = dom.xpath("string(//li/a[2])")
print(ret)
```

返回结果：

```python
总裁的新鲜小妻子
```

## 6.选取多个路径

 有的时候我们需要同时查找多个条件，这个时候你可以通过在路径表达式中使用管道符("|")，选取若干个路径：

```python
dom = etree.HTML(html)
ret = dom.xpath('//li[1]/img[1]/@src | //li[1]/a[2]/text()')
print(ret)
```

返回结果：

```python
['/kukuku/images/only.png', '总裁的新鲜小妻子']
```

注意⚠️：

同时选取多个路径并不会生成一个新的列表，只有一个列表，所以你要考虑好如何提取你的返回结果。一般情况下还是不建议使用多条件来进行查找，因为多种提取结果混在一个列表不利于提取，**即便可以提取，但也会增加计算量**，**降低程序性能**。所以，**尽量不要使用这种方法**。



以上就是所有的xpath有关内容，可能不是很细，做爬虫足够了使用了。如果你想更深入了解XPath的话 推荐。

W3C xpath 教程 ：https://www.w3school.com.cn/xpath/index.asp