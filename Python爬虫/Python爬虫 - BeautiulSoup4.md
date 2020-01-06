# Python爬虫 - BeautiulSoup4

## 简介

**BeautiulSoup4是一个 Python第三方库  需要pip 安装 又称 CSS选择器 和 lxml 一样，Beautiful Soup 也是一个 HTML/XML 的解析器，主要的功能也是如何解析和提取 HTML/XML 数据。**

> lxml 只会局部遍历，而Beautiful Soup 是基于 HTML DOM 的，会载入整 个文档，解析整个 DOM 树，因此时间和内存开销都会大很多，所以性 能 要低于 lxml。
>
> BeautifulSoup 用来解析 HTML 比较简单，API非常人性化，支持 CSS选择器、Python 标准库中的 HTML 解析器，也支持 lxml 的 XML 解析 器。

Beautiful Soup 3 目前已经停止开发，推荐现在的项目使用Beautiful Soup 4

**官方文档**:http://beautifulsoup.readthedocs.io/zh_CN/v4.4.0

| **解析器**   | **使用方法**                                                 | **优势**                                                     |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Python标准库 | BeautifulSoup(html, "html.parser")                           | 1、Python的内置标准库2、执行速度适中3、文档容错能力强        |
| lxml HTML    | BeautifulSoup(html, "lxml")                                  | 1、速度快2、文档容错能力强                                   |
| lxml XML     | BeautifulSoup(html, ["lxml", "xml"])BeautifulSoup(html, "xml") | 1、速度快2、唯一支持XML的解析器                              |
| html5lib     | BeautifulSoup(html, "html5lib")                              | 1、最好的容错性2、以浏览器的方式解析文档3、生成HTML5格式的文档 |

## 测试页面数据

```html
<html lang="en">
<head>
	<meta charset="UTF-8" />
	<title>测试bs4</title>
</head>
<body>
	<div>
		<p>百里守约</p>
	</div>
	<div class="song">
		<p>李清照</p>
		<p>王安石</p>
		<p>苏轼</p>
		<p>柳宗元</p>
		<a href="http://www.song.com/" title="赵匡胤" target="_self">
			<span>this is span</span>
		宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱</a>
		<a href="" class="du">总为浮云能蔽日,长安不见使人愁</a>
		<img src="http://www.baidu.com/meinv.jpg" alt="" />
	</div>
	<div class="tang">
		<ul>
			<li><a href="http://www.baidu.com" title="qing">清明时节雨纷纷,路上行人欲断魂,借问酒家何处有,牧童遥指杏花村</a></li>
			<li><a href="http://www.163.com" title="qin">秦时明月汉时关,万里长征人未还,但使龙城飞将在,不教胡马度阴山</a></li>
			<li><a href="http://www.126.com" alt="qi">岐王宅里寻常见,崔九堂前几度闻,正是江南好风景,落花时节又逢君</a></li>
			<li><a href="http://www.sina.com" class="du">杜甫</a></li>
			<li><a href="http://www.dudu.com" class="du">杜牧</a></li>
			<li><b>杜小月</b></li>
			<li><i>度蜜月</i></li>
			<li><a href="http://www.haha.com" id="feng">凤凰台上凤凰游,凤去台空江自流,吴宫花草埋幽径,晋代衣冠成古丘</a></li>
		</ul>
	</div>
</body>
</html>
```

## 一、安装

```python
pip3 install beautiulsoup4
pip3 install lxml
```

## 二、使用流程

使用方式：可以将一个html文档，转化为BeautifulSoup对象，然后通过对象的方法或者属性去查找指定的节点内容

### 1.转化本地文件

```python
soup = BeautifulSoup(open('本地文件'), 'lxml')
```

### 2.转化网络文件:

```python
soup = BeautifulSoup('字符串类型或者字节类型', 'lxml')
```

### 3.打印soup对象显示内容为html文件中的内容

```python
from bs4 import BeautifulSoup

# 创建bs4对象

soup = BeautifulSoup(html, "lxml")

print(soup) #直接输入内容 

print(soup.prettify()) #把 html格式化输出
```

## 三、使用语法

### 1.根据标签名查找

只能找到第一个符合要求的标签

```python
soup = BeautifulSoup(html, "lxml")
print(soup.a)
print(soip.div)
print(soip.title)
```

返回结果；

```html
<a href="http://www.song.com/" target="_self" title="赵匡胤">
<span>this is span</span>
		宋朝是最强大的王朝，不是军队的强大，而是经济很强大，国民都很有钱
</a>

<div>
<p>百里守约</p>
</div>

<title>测试bs4</title>
```

### 2.获取属性

- 获取a所有的属性和属性值，返回一个字典

```python
soup = BeautifulSoup(html, "lxml")
print(soup.a.attrs)
```

返回结果：

```python
{'href': 'http://www.song.com/', 'title': '赵匡胤', 'target': '_self'}
```

- soup.a.attrs['href']   获取href属性

```python
soup = BeautifulSoup(html, "lxml")
print(soup.a.attrs["href"])
```

返回结果:

```python
http://www.song.com/
```

- 也可简写为这种形式

```python
 soup.a['href'] 
```

### 3.获取内容

```python
soup = BeautifulSoup(html, "lxml")
print(soup.a.string)
print(soup.a.get_text())
print(soup.text)
```

注意⚠️如果标签还有标签，那么string获取到的结果为**None**，而其它两个，可以获取文本内容

### 4.ind：找到第一个符合要求的标签

```python
soup = BeautifulSoup(html, "lxml")

print(soup.find("a"))
print(soup.find("a", title="xxx")) # 找到title叫xxx的第一个标签 
print(soup.find("a", alt="xxx")) 
print(soup.find("a", class_="xxx")) # 找到class叫xx的第一个标签
print(soup.find("a", id="xxx"))
```

### 5.find_all：找到所有符合要求的标签 返回值是个list

```python
soup = BeautifulSoup(html, "lxml")

print(soup.find_all("a",)) #找多所有的a标签
print(soup.find_all("a",class_="du")) #找多所有的class叫 du a标签
print(soup.find_all(["a","b"])) #找多所有的a,b标签
print(soup.find_all("a",limit=2)) #找多所有的a,b标签

```

### 6.根据选择器选择指定的内容select 返回值是个list

```python
soup = BeautifulSoup(html, "lxml")
print(soup.select("#feng"))
print(soup.select(".du"))
```

- 层级选择器

```python
div .dudu #lala .meme .xixi  下面好多级
div > p > a > .lala          只能是下面一级
```

【注意】⚠️select选择器返回永远是列表，需要通过下标提取指定的对象