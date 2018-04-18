<!--#!/usr/bin/env Markdown
# -*- coding: utf-8 -*-
# @Date    : 2017-10-19 16:40:50
# @Author  : kevin ma (mahaibin97@gmail.com)
# @Link    : http://www.aduxingzhe.com
# @Version : $Id$-->

Python爬虫的requests库---get方法
====

**昨天预告中已经提到，今天我们细说的是---requests库中的get（）方法。**

##一、get方法的概述
get（）方法，是requests库中最常用的一个方法，也是我们实现定向网页爬虫最常用的方法。
```
<responce 对象> =requests.get(url,params=None,****kwargs)
```
####1.1.responce对象
responce对象，是Python爬虫向网站服务器请求后，收到的回应。response对象也有自己的属性参数，具体见下。

#### 1.2.requests.get(X,X,X)
request的对象，其中有及具体描述给请求的参数、变量等等。
```
url  : http://host [:port] [path]
```
## 二、response对象的属性
#### 2.1状态码
```
r.status_code
>>>200
```
HTTP请求的返回状态，200表示连接成功，404表示失败

#### 2.2 HTTP回应的内容文本格式

```
r.text
```
#### 2.3 HTTP回应的二进制格式

```
r.content
```
#### 2.4 回应内容的编码方式

```
r.encoding
```
这样得到的是，从HTTP header中猜测的响应内容编码方式

#### 2.5 备选编码方式  

```
r.apparent_encoding
```
从内容中分析出的响应内容编码方式（备选编码方式）

#### 2.6 headers属性

```
r.headers
>>>{'Transfer-Encoding': 'chunked', 'Cache-Control': 'private, no-cache, no-store, proxy-revalidate, no-transform', 'Content-Encoding': 'gzip', 'Content-Type': 'text/html', 'Date': 'Sun, 30 Jul 2017 16:17:45 GMT', 'Keep-Alive': 'timeout=38', 'Last-Modified': 'Mon, 23 Jan 2017 13:28:12 GMT', 'Pragma': 'no-cache', 'Server': 'bfe/1.0.8.18', 'Set-Cookie': 'BDORZ=27315; max-age=86400; domain=.baidu.com; path=/'}
```

## 三、get方法详解

>从二中我们已经知道了get方法的格式规范，现在我们就开始详解get方法。

#### 3.1 get方法中的params参数

字典或字节序列，可作为参数添加到URL中。

```
kv={'key1':'value1'}
r=requests.get("http://www.baidu.com/ws",params=kv)
```

#### 3.2 get中的13个可变参数

###### 3.2.1 data  

字典、字节序列或文件对象，作为Request的内容

###### 3.2.2 headers

字典，HTTP定制头

```
import requests
kv="Chrome/3.0"
headers={"user-agent":"kv"}
r=requests.get("htttp://www.baidu.com",headers=headers)
>>>r.headers
```

###### 3.2.3 timeout

超时处理

```
r=requests.get("htttp://www.baidu.com",timeout=30)
```

###### 3.2.4 其余参数

>JSON、data、cookies、auth、files、proxies、allow_redirects、stream、verify、cert

## 四、简单爬虫的通用代码框架

```
import requests

def getHtml(url):
  try:
    r=requeests.get(url,timeout=30)
    r.raise_for_status()
    r.encoding=r.apparent_encoding
    return r.text[:300]
  except:
    return "Error!\n"

if _ _name_ _=="_＿ｍａｉｎ＿＿＂：
   url＝＂http://www.baidu.com＂
   print(getHtml(url))
```

## 五、说一个实例实例

**使用百度关键词搜索**

```
import requests

def askBaidu(url,keyword):
	try:
		kv={'wd':'keyword'}	#baidu的关键词搜索链接为：http://www.baidu.com/s?wd=<keyword>
		r=requests.get(url,timeout=30,params=kv)
		r.raise_for_status()
		r.apparent_encoding
		return r.text[:500]
	except:
		return "Error !\n"

def main():
	print("Please Enter Your keyword:\n")
	keyword=str(input())
	url="http://www.baidu.com/s"
	print(askBaidu(url,keyword))

main()
```
