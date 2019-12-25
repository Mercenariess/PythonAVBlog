# Requests

# 简介

Requests库

![img](https://upload-images.jianshu.io/upload_images/17476284-aaaae0326f700dc7.png?imageMogr2/auto-orient/strip|imageView2/2/w/1020/format/webp)

`requests`是一个很实用的Python HTTP客户端库，编写爬虫和测试服务器响应数据时经常会用到。可以说，**Requests 完全满足如今网络的需求



开源地址：[https://github.com/kennethreitz/requests](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fkennethreitz%2Frequests)
中文文档：[http://docs.python-requests.org/zh_CN/latest/index.html](https://links.jianshu.com/go?to=http%3A%2F%2Fdocs.python-requests.org%2Fzh_CN%2Flatest%2Findex.html)

# 一、Requests基础

## 1.安装requests库

```python
pip3 install requests
```

## 2.使用Requests库

```python
import requests
```

# 二 、发送请求与接收响应(基于GET请求)

## 1.传送pramas参数

- 参数包含在url中

```python
response = requests.get("http://httpbin.org/get?name=春生&age=22")
print(response.text)
```

- http://httpbin.org 

```python
httpbin.org 这个网站能测试 HTTP 请求和响应的各种信息，比如 cookie、ip、headers 和登录验证等，且支持 GET、POST 等多种方法，对 web 开发和测试很有帮助。

它用 Python + Flask 编写，是一个开源项目。

官方网站：http://httpbin.org/
开源地址：https://github.com/Runscope/httpbin
```

- 通过get方法传送参数

```python
data = {
        "name": "zhangsan",
        "age": 30
    }
response = requests.get("http://httpbin.org/get", params=data)
print(response.text)
```

## 2.模拟发送请求头(传送headers参数)

```python
import requests

headers = {

    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.88 Safari/537.36'
}

url = "http://httpbin.org/get"

response = requests.get(url=url,headers=headers)

print(response.text)
```

# 三、发送请求与接收响应(基于post请求)

```python
import requests

headers = {

    'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.88 Safari/537.36'
}

url = "http://httpbin.org/post"

data ={
    "cnbool": "PythonAV",
    "name": "chunsheng",
    "age": "18"
}

response = requests.post(url=url,data=data,headers=headers)

print(response.text)
```

# 四、Request属性

|         属性         |               描述               |
| :------------------: | :------------------------------: |
|    response.text     | 获取str类型（Unicode编码）的响应 |
|   response.content   |       获取bytes类型的响应        |
| response.status_code |          获取响应状态码          |
|   response.headers   |            获取响应头            |
|   response.request   |        获取响应对应的请求        |

# 五、代理

- proxies 代理

```python
proxies = {
    "http": "https://175.44.148.176:9000",
    "https": "https://183.129.207.86:14002"
}
response = requests.get("https://www.baidu.com/", proxies=proxies)
```

# 六、cookie和session

## 1.cookie

(1)获取cookie信息

```python
response.cookies
```

## 2.session

(1)构造session回话对象

示例:

```python
 # 自动处理 cookie信息
    headers = {
        'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.36'
    }
    url = 'https://xueqiu.com/v4/statuses/public_timeline_by_category.json?since_id=-1&max_id=20359506&count=15&category=-1'
    from requests import Session

    requests_session = Session()

    # 构建cookie 信息 存储在requests_session
    requests_session.get(url="https://xueqiu.com/", headers=headers)

    # 利用 存储在requests_session 这个已经有 cookie 信息的 对象 在去发送请求
    response = requests_session.get(url=url, headers=headers).json()
    print(response)
```

# 七、案例

![img](https://upload-images.jianshu.io/upload_images/17476284-07b7217279f91414.png?imageMogr2/auto-orient/strip|imageView2/2/w/602/format/webp)

## 案例1：爬取搜狗主页

```python
import requests
url = "https://www.sogou.com/web"
query = input(">>>>>>>>")
# 数据动态化
params = {
    "query":query
}
# 
headers = {
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.3"
}
# requests 参数模拟
restonse = requests.get(url=url,params=params,headers= headers)

# 解决乱码 
restonse.encoding = "utf-8"

filename = query+".html" 

with open(filename,"w",encoding="utf-8") as f:
    f.write(restonse.text)
```

## 案例2: 爬取豆瓣电影详情数据

```python
import requests
headers = {
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.108 Safari/537.3"
}
url = "https://movie.douban.com/j/chart/top_list"
start = input("在第几个电影开始")
limit = input("在第几个电影结束")
dic = {
    'type':'13',
    'interval_id':'100:90',
    'action': "2",
    'start': start,
    'limit': limit
}
response=requests.get(url=url,params=dic,headers=headers)
page_text = response.json()
page_text

```

## 案例3:肯德基查询

```python
url = "http://www.kfc.com.cn/kfccda/ashx/GetStoreList.ashx?op=keyword"
for page in range(1,5):
    data = {
        'cname':'',
        'pid':'',
        'keyword': '北京',
        'pageIndex': str(page),
        'pageSize': '10'
    }
    response = requests.post(url=url,headers=headers,data=data)
    print(response.json())
```



