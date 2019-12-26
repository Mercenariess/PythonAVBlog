# Python爬虫-正则

# 前言

想说正则的知识点并不多，需要思考地方却不少。python里提取数据的方式很多，但私以为掌握那些中的某一个的同时，一定要兼顾正则。主观推崇原因有二，一：re是python的基本库，不需要额外安装且解析速度快；二：正则能做到xpath、BeautifulSoup、PyQuery可以做到的；正则还是能做到它们做不到的。

# 一、使用

1. `import re`
2. match，正则中最基本的函数，用法：
   `result = re.match(pattern, 需要区配的字符串)`
   `result.group()`取出被区配到的部分
3. 演示语法的时候，基本match区配的过程是：注意⚠️**从左到右区配，直到出现不满足规则的时候停止**

# 二 、语法

## 表示字符

| 字符 |                    功能                     |
| :--: | :-----------------------------------------: |
|  .   |          区配任意一个字符（除\n）           |
|  []  |             区配[]中列举的字符              |
|  \d  |                区配数字，0-9                |
|  \D  |                 区配非数字                  |
|  \s  | 区配空白，（包含空格和Tab键）（\t \n 空格） |
|  \S  |                 区配非空白                  |
|  \w  |       区配单词字符，a-z，A-Z，0-9，_        |
|  \W  |               区配非单词字符                |

##### 1.点表示任意字符，所以返回H

示例:

```python
import re
ret = re.match(r".","Hello,wored")
print(ret.group())
```

##### 2.中括号是列举的意思，表示只要满足其中任意一个就行

示例:

```python
import re
ret = re.match(r"[0-9]","123457")
print(ret.group())
```

返回结果 

```python
1
```

##### 3.`/d`表示数字等价于`[0-9]`

##### 4.`\s`表示制表符，空格，换行；这里用`"""`包裹的字符串来演示，因为`"""`里，字符串可以保存样式，即可以包含换行的内容，所以最后返回换行符

```python
import re
string = """

chunsheng

"""
ret = re.match(r"\s",string)
print(ret.group())
```

##### 5.`\w`的使用基本上可以举一反三了，包括其大写字符的实现效果。规则很好记忆：d是digit(数字)，s是space(空间)，w是word(词)

## 表示个数

|   `*`   |  限定前一个字符出现0次或n次  |
| :-----: | :--------------------------: |
|   `+`   | 限定前一个字符出现1次或者n次 |
|   `?`   |  限定前一个字符出现0次或1次  |
|  `{m}`  |    限定前一个字符出现m次     |
| `{m,}`  |  限定前一个字符至少出现m次   |
| `{m,n}` |   限定前一个字符出现m到n次   |

注意⚠️：

**前面表示字符，这里是表示个数，在没有明确说明个数的情况下默认一个；其次，表示个数的符号总是修饰它`左边`的字符**

##### 1.`*`表示被修饰字符出现0次或n次，所以一直到逗号出现，不满足规则了停止，返回区配到的内容，即为hello

示例:

```python
ret = re.match(r"\w*","hello,word")
print(ret.group())
```

返回结果：

```python
hello
```

## 表示边界

| 字符 |      功能      |
| :--: | :------------: |
| `^`  | 表示字符串开头 |
| `$`  | 表示字符串结尾 |
| `\b` | 表示单词的边界 |
| `\B` | 表示非单词边界 |

注意⚠️：需要说名的是`^`是放在被修饰符的左边，而`$`放在被修饰符的右边；**且如果`^`在中括号中出现，表示取反的意思**

##### 1.这个表达式分5个部分去理解：

`^\d*`表示以数字开头，并且之后可以有0或n个数字

`@`就表示区配@

中间的`\w*`表示允许0个或n个单词

`.`表示任意字符，这里区配邮箱号中的点

`\w*$`表示存在多个单词字符，并且以单词字符结尾

示例:

```python
import re
ret = re.match(r"^\d*@\w*.\w*$","1093725364@qq.com")
print(ret.group())
```

返回结果:

```python
1093725364@qq.com
```

##### 2.`$`应该慎用，因为你极可能因此拿不到返回。它指定的是被区配的字符串的末尾，这样说很难理解，看看下面示例

我原以为是区配邮箱前面的数字，然后区配@符，`\w*`表示任意个数的单词字符，并且以此结尾。

所以根据这个逻辑，料想应该会返回**1093725364@qq**，毕竟小圆点出现后就不再满足要求。然而事实是——**没有返回值**。

这是因为使用`$`应该以**被区配的字符串**的结尾作为标准，在这个标准满足之前，中间不能出现任何区配失败，不然返回空

示例：

```python
import re
ret = re.match(r"^\d*@\w*.\w$","1093725364@qq.com")
print(ret)
```

返回结果：

```python
None
```

有返回结果示例：

```python
import re
ret = re.match(r"^\d*@\w*.\w$","1093725364@qq.c")
print(ret.group())
```

只要结尾有个字符就可以了 因为 *符合 属于贪婪匹配 匹配多个

##### 3.`\b`是border边界的意思

放在最右侧时，饰其左边的单词字符，表示什么字母结尾的单词

```python
import re
ret = re.match(r"\w+o\b",r"hello word")
print(ret.group())
```

如果放在最左边，修饰右边的单词字符，表示什么字母开头的单词

```python
import re
ret = re.match(r"\bh\w+",r"hello word")
print(ret.group())
```

## 匹配分组

|     字符     |               功能               |
| :----------: | :------------------------------: |
|      \|      | “或”，表示符合左右任意一个都可以 |
|    `(ab)`    |     将括号中字符作为一个分组     |
|    `\num`    |    引用分组num区配到的字符串     |
| `(?P<name>)` |            分组起别名            |
| `(?P=name)`  | 引用别名为name分组区配到的字符串 |

##### 1.`|`即“或”运算

示例:

```python
import re
ret = re.match(r"\w*|\d* ",r"Abc123##")
print(ret.group())
```

返回结果:

```python
Abc123
```

##### 2.用括号表示分组，方便取出；这样一来，我们可以拿出我们想要的部分了

示例：

```python
import re
ret = re.match(r"^(\d*)@(\w*).(\w*)$",r"1093725364@qq.com")
print(ret.group(1),"--",ret.group(2),"--",ret.group(2))
```

返回结果：

```python
1093725364 -- qq -- com
```

##### 3.`\num`的使用如下，即从左到右，每个分组依次默认为123……，在后面需要引用的时候就用\num来代替

示例：

```python
import re

s = "<div><p>This is a test As long as the heart unwilling there is no time to give up</p></div>"

ret = re.match(r"<(div)><(p)>([\w|\s]+)</\2></\1>",s)
print(ret.group())
```

返回结果：

```html
<div><p>This is a test As long as the heart unwilling there is no time to give up</p></div>
```

##### 4.如果分组太多，一个一个的数会很麻烦，所以正则引进了分组别名的方法，并可以通过指定的语法实现引用

示例:

```python
import re

s = "<div><p>This is a test As long as the heart unwilling there is no time to give up</p></div>"
ret = re.match(r"<(?P<div>div)><(?P<p>p)>(.*)</(?P=p)></(?P=div)>",s)
print(ret.group())
```

也可以通过别名直接取出分组部分

示例:

```python
import re

s = "1093725364@qq.com"

ret = re.match("\d+@(?P<domain>\w+).\w+",s)
print(ret.group("domain"))

```

返回结果：

```python
qq
```

# 四、re模块高级用法

## search()

search：与match不同，match是从左到右，必须挨个符合；而search表示只要被区配的字符串中有符合的就返回，但是，**只返回第一次满足要求的内容**。如下示例，返回qq而不返回com

示例:

```python
import re

s = "1093725364@qq.com"

ret = re.search("[a-z]+",s)
print(ret.group())
```

返回结果：

```python
qq
```

## findall()

返回被区配字符串中所有的符合要求的内容，返回列表型

示例:

```python
import re

s = "1093725364@qq.com"

ret = re.findall("[a-z]+",s)
print(ret)
```

返回结果：

```python
["qq","com"]
```

## sub()

将区配到的内容，按照指定的方式替换（批量处理）

示例:

```python
import re

s = "1093725364@qq.com"

ret = re.sub("[a-z]+","text",s)
print(ret)

```

返回结果：

```python
1093725364@text.text
```

# 五、re.S，re.M，re.I的作用

| 修饰符 | 描述                                                         |
| ------ | :----------------------------------------------------------- |
| re.I   | 使匹配对大小写不敏感                                         |
| re.L   | 做本地化识别（locale-aware）匹配                             |
| re.M   | 多行匹配，影响 ^ 和 $                                        |
| re.S   | 使 . 匹配包括换行在内的所有字符                              |
| re.U   | 根据Unicode字符集解析字符。这个标志影响 \w, \W, \b, \B.      |
| re.X   | 该标志通过给予你更灵活的格式以便你将正则表达式写得更易于理解。 |

##### 1.`re.S`。它表示 “.” 的作用扩展到整个字符串，包括“\n”。

示例：

```python
import re

a = '''abc hello pass
    world efi
    '''
b = re.findall('hello(.*?)world', a)
c = re.findall('hello(.*?)world', a, re.S)
print('b:', b)
print('c:', c)
```

返回结果：

```python
b: []
c: [' pass\n    ']
```

正则表达式中，“.”的作用是匹配除“\n”以外的任何字符，也就是说，它是在一行中进行匹配。这里的“行”是**以“\n”进行区分的。a字符串有每行的末尾有一个“\n”，不过它不可见。**

如果不使用re.S参数，则只在每一行内进行匹配，如果一行没有，就换下一行重新开始，不会跨行。

而使用re.S参数以后，正则表达式会将这个字符串作为一个整体，将“\n”当做一个普通的字符加入到这个字符串中，在整体中进行匹配。

##### 2.`re.l`  不区分大小写

示例：

```python
import re
res = re.findall(r"a", "Abc", re.I)
print(res)
```

返回结果：

```python
A
```

##### 3.`re.M`  将所有的行尾字母输出

多行匹配，影响 ^ 和 $

```python
>>> s= '12 34/n56 78/n90'
 
>>> re.findall( r'^/d+' , s , re.M )          # 匹配位于行首的数字
 
['12', '56', '90']
 
>>> re.findall( r’/A/d+’, s , re.M )        # 匹配位于字符串开头的数字
 
['12']
 
>>> re.findall( r'/d+$' , s , re.M )          # 匹配位于行尾的数字
 
['34', '78', '90']
 
>>> re.findall( r’/d+/Z’ , s , re.M )        # 匹配位于字符串尾的数字
 
['90']
```

# 六、贪婪非贪婪

## 贪婪匹配

示例:

```python
import re
patern = re.compile('www\..*')
match1 = patern.match("www.baidu.com")
if match1:
  print(match1.group())
else:
  print("match1 don't match")
```

返回结果：

```python
www.baidu.com
```

## 非贪婪匹配

示例：

```python
import re
patern = re.compile('www\..*?')
match1 = patern.match("www.baidu.com")
if match1:
  print(match1.group())
else:
  print("match1 don't match")
```

返回结果：

```python
www.
```

# 注意事项⚠️

使用python对网站进行爬取数据的时候，有些数据本身就是个字符 比如（& |   路径 ）需要使用到转义字符，

## 字符串转义

#### 反斜杠，在python中比较特殊，就是它可以用来构成一些特殊字符，比如"\n"表示换行，"\t"表示制表符。

```python
print("hello\word\npython") 
```

比如说我就想输出 原样字符串 "hello\word\npython" 但是结果：

```python
hello\word
python
```

结果给我换行了 无奈之举

### 解决办法

```python
print("hello\word\\npython")
```

## 正则转义

```python
import re
s = "我出了一道题：2\8 结果：4"
ret = re.search("\d\\\\\d+",s)
print(ret.group())
```



