<!--#!/usr/bin/env Markdown
# -*- coding: utf-8 -*-
# @Date    : 2017-10-19 16:40:51
# @Author  : kevin ma (mahaibin97@gmail.com)
# @Link    : http://www.aduxingzhe.com
# @Version : $Id$-->

Python爬虫的requests库---其余方法
====

## 一、head（）方法

```
>>> import requests
>>> r=requests.head("http://www.baidu.com")
>>> r.headers
{'Connection': 'close', 'Cache-Control': 'private, no-cache, no-store, proxy-revalidate, no-transform', 'Content-Encoding': 'gzip', 'Content-Type': 'text/html', 'Date': 'Mon, 31 Jul 2017 13:23:10 GMT', 'Keep-Alive': 'timeout=38', 'Last-Modified': 'Mon, 13 Jun 2016 02:50:23 GMT', 'Pragma': 'no-cache', 'Server': 'bfe/1.0.8.18'}
```

## 二、post（）方法

```
r=requests.post(url,data=None,Json=None,**kwgras)
```
- url : 拟更新页面的url链接
- data : 字典、字节序列或文件，Request的内容
- json : JSON格式的数据，Request的内容
-  **kwargs: 12个控制访问的参数

##### 2.1 传表单

通常，你想要发送一些编码为表单形式的数据——非常像一个 HTML 表单。要实现这个，只需简单地传递一个字典给 data 参数。你的数据字典在发出请求时会自动编码为表单形式：

```
>>> payload = {'key1': 'value1', 'key2': 'value2'}

>>> r = requests.post("http://httpbin.org/post", data=payload)
>>> print(r.text)
{
  ...
  "form": {
    "key2": "value2",
    "key1": "value1"
  },
  ...
}
```

#### 2.2 传文件参数

Requests 使得上传多部分编码文件变得很简单：

```
>>> url = 'http://httpbin.org/post'
>>> files = {'file': open('report.xls', 'rb')}

>>> r = requests.post(url, files=files)
>>> r.text
{
  ...
  "files": {
    "file": "<censored...binary...data>"
  },
  ...
}
```

## 三、put（）方法

**向HTML网页提交PUT请求的方法，对应于HTTP的PUT**
```
r=requests.put(url,data=None,**kwgras)
```
- url : 拟更新页面的url链接
-  data : 字典、字节序列或文件，Request的内容
- **kwargs: 12个控制访问的参数
```
import requests
r=requests.put("http://www.baidu.com")
r.text
>>>'<html>\r\n<head>\r\n<meta http-equiv="content-type" content="text/html;charset=utf-8">\r\n<style data-for='
```

## 四、patch（）方法

**向HTML网页提交局部修改请求，对应于HTTP的PATCH**

```
r=requests.patch(url,data=None,**kwgras)
```
- url : 拟更新页面的url链接
-  data : 字典、字节序列或文件，Request的内容
- **kwargs: 12个控制访问的参数
```
import requests
r=requests.patch("http://www.zhihu.com")
r.text
>>>'<html><body><h1>500 Server Error</h1>\nAn internal server error occured.\n</body></html>\n'
```

## 五、delete（）方法

**向HTML页面提交删除请求，对应于HTTP的DELETE**
```
r=requests.put(url,**kwgras)
```
- url : 拟更新页面的url链接
- **kwargs: 12个控制访问的参数
```
import requests
r=requests.delete("http://www.zhihu.com")
r.text
```


Source From：[Requests:HTTP for humans](http://docs.python-requests.org/en/master/)
