<!--#!/usr/bin/env python
# -*- coding: utf-8 -*-
# @Date    : 2017-10-19 16:35:58
# @Author  : kevin ma (mahaibin97@gmail.com)
# @Link    : http://www.aduxingzhe.com
# @Version : $Id$-->

Python爬虫的requests库---request方法
====

如果你想使用Python语言的写一个爬虫的程序，用来获取网络资源的话，最常用到的Python第三方库有两个，Requests和Scrapy。

其中，如果是需求简单的初级爬虫程序的话，比如说定向爬取网页，requests库就足够了；而如果你的需求是爬取网站或者系列网站，数据量大的话，那就要编写一个中级的爬虫程序，这时候最常用的就是scrapy库；当然，你要想爬取整个互联网的话，你就得自己定制一个第三方库了。

我们今天要说的是最简单的---Requests库。Requests库可以说是Python爬虫中最优秀的库之一，它简单好用，能够解决我们大多数的需求。

### 从一个实例开始

首先，我用一个实例来说明。

```
import requests

url= "http://www.baidu.com"

r=requests.get(url)
r.text[:100]
```

这段代码要实现的功能是：爬取百度首页。我首先用import关键字引入了Requests第三方库，之后将Requests中get方法“计算”的结果返回给r变量。这里的get(url)就是request的对象，而r变量就是response的对象。最后，以text的格式输入前100个字符。

从这个实例中，你已经看到了Requests库的可用之处，接下来我们细说---Requests库。

### 认识Requests库

严格来说，Requests库是Python中的一个HTTP请求模块。这个请求与回应的过程，原理和我们使用浏览器访问某个网站服务器是一样的，唯一不同的是，发出访问请求的主体是Python-requests/x.xx。有关这一点，之后还会详细说明。

既然requests库是HTTP的请求模块，当然，我们就能想到二者之间必然有更深的联系。

HTTP中，定义了8中操作网页资源的方法：GET、HEAD、POST、PATCH、PUT、DELETE、TRACE、CONNECT；而requests库中的7个方法除了request，其余都于HTTP中的前六种资源操作方法一致。

下面就是requests库的7个主要方法

方法

说明

requests.request()

构造一个请求，支撑以下各方法的基础方法

requests.get()

获取HTML网页的主要方法，对应于HTTP的GET

requests.head（）

获取HTML网页头信息的方法，对应于HTTP的HEAD

requests.post()

向HTML网页提交POST请求的方法，对应于HTTP的POST

requests.patch()

向HTML网页提交局部修改请求，对应于HTTP的PATCH

requests.delete()

向HTML页面提交删除请求，对应于HTTP的DELETE

requests.put()

向HTML网页提交PUT请求的方法，对应于HTTP的PUT

### 基础方法---request（）

如果说requests库中只有一个方法的话，那就是request（）方法。因为request（）方法是其余6个方法的基础，它就是那个比较特殊的孩子。

request（）方法的基本格式是：requests.request(method,url,**kwargs)

其中，

- method : 请求方式，对应get/put/post等6种
- url : 需求中想要获取页面的url链接
- **kwargs: 控制访问的参数，共有13个

举一例子来说明，```r=requests.request("GET","http://www.baidu.com",timeout=30)```。调用get（）方法，抓取百度首页，超时计时器为30s。

从实例可以看出来，method有6中选择： GET、HEAD、POST、PATCH、PUT、DELETE；最常用的当然是get了，关于git我们将在下一篇文章中详细介绍。
而request方法中的13个可控访问参数，分别是：params/data/JSON/headers/cookies/files/timeout/auth/proxies/allow_redirects/stream/verify/cert。其中，最常用的几个，也是怎今后的学习中我们会谈到的，如下：

params

字典或字节序列，作为参数增加到url中

data

字典、字节序列或文件对象，作为Request的内容

JSON

JSON格式的数据，作为Request的内容

cookies

字典或CookieJar，Request中的cookie

timeout

设定超时时间，秒为单位

headers

字典，HTTP定制头

一个实例

### 爬取Amazon中某个商品的页面

```
import requests
def getAmazon(url):
try:
kv={'user-agent':'Mozillia/5.0'}
r=requests.get(url,timeout=30,headers=kv)
r.raise_for_status()
r.encoding=r.apparent_encoding
return r.text[:500]
except:
     return "Error!\n"
if __name__=="__main__":
     url="https://www.amazon.cn/%E6%9C%AA%E6%9D%A5%E7%AE%80%E5%8F%B2-%E5%B0%A4%E7%93%A6%E5%B0%94-    %E8%B5%AB%E6%8B%89%E5%88%A9/dp/B01MZ4Z5DQ/ref=tmm_pap_swatch_0?_encoding=UTF8&qid=&sr="
     print(getAmazon(url))
```

这个实例中，用到的是get（）方法 ，而增加了的可变参数有timeout和headers两个。这两个参数在这里的作用是，

- timeout用来做计时器，控制时间；
- headers是用来修改ＨＴＴＰ请求的头部。这是因为，Ａｍａｚｏｎ对 有专门针对这类爬虫的反爬机制，它能识别出访问来源于Ｐｙｔｈｏｎ爬虫。

```
r.request.headers

>>> {'User-Agent': 'python-requests/2.13.0', 'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive'}
```

就是上面显示的这样，所以我们要修改 request对象的头部信息，＂逃避＂网站反爬追踪。这就用到了ｈｅａｄｅｒｓ参数。

```
>>> kv={"user-agent":"Chrome/3.0"}
>>> r=requests.get("https://www.amazon.cn/%E6%9C%AA%E6%9D%A5%E7%AE%80%E5%8F%B2-%E5%B0%A4%E7%93%A6%E5%B0%94-%E8%B5%AB%E6%8B%89%E5%88%A9/dp/B01MZ4Z5DQ/ref=tmm_pap_swatch_0?_encoding=UTF8&qid=&sr=",headers=kv)
>>>r.request.headers
{'user-agent': 'Chrome/3.0', 'Accept-Encoding': 'gzip, deflate', 'Accept': '*/*', 'Connection': 'keep-alive'}
```

这样，就完美的隐藏了访问源，可以达到我们爬取的目的。

结尾

今天，我们从一个简单的例子到一个较复杂的实例，来讲 requests 库中的 request（）方法。主要说了 request的结构，可选用的方法，以及可控参数，特别是那几个常用参数，一定要用熟。当然，最后的这个实例也是非常重要的，以后爬取相似的网页，完全可以根据这个来改。

预告一下，明天，我们说的是 requests库中最重要的方法－－－ｇｅｔ（）方法。
