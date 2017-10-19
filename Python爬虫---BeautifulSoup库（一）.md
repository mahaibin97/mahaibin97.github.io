#!/usr/bin/env Markdown
# -*- coding: utf-8 -*-
# @Date    : 2017-10-19 16:47:03
# @Author  : kevin ma (mahaibin97@gmail.com)
# @Link    : http://www.aduxingzhe.com
# @Version : $Id$


Python爬虫---BeautifulSoup库（一）
====

## 一、第一个应用实例

我们先来看一个实例，初探BS库的神奇之处。

```
import requests
from bs4 import BeautifulSoup

r=requests.get("http://www.zuihaodaxue.cn/Sport-Science-Schools-and-Departments-2016.html")
demo=r.content[:2000]
soup=BeautifulSoup(demo,"html.parser")
print(soup.prettify())

>>><!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
 <head>
  <meta charset="utf-8">
   <meta content="width=device-width, initial-scale=1" name="viewport">
    <meta content="上海软科于2016年6月15日发布软科世界一流学科排名”2016。此次学科排名发布的学科集中在工程领域，包括化学工程、土木工程、电力电子工程、能源科学与工程、环境科学与工程、材料科学与工程、机械工程共7个学科。" name="description">
     <meta content="最好大学网" name="author">
      <link href="images/favicon.png" rel="shortcut icon"/>
      <title>
       软科全球体育类院系学术排名 2016 | 最好大学网
      </title>
      <link href="./houtai/templates/css/bootstrap.min.css" rel="stylesheet">
       <link href="./houtai/templates/css/template.css" rel="stylesheet">
       </link>
      </link>
     </meta>
    </meta>
   </meta>
  </meta>
 </head>
</html>
```

- 通过输出结果，能看到HTML页面的标签形成了整齐的标签结构树，展示出来。
- 该实例中用到了BS库的基本用法和prettify()方法。

## 二、BS库的基本元素

#### 2.1 认识BS库

**```<BS 对象名> = BeautifulSoup(<Html>,"解析器")```**
- 上例中的soup就是一个BS对象，这个对象就是调用BS库之后，HTML页面被解析得到的结果；
- Html,指的是requests库返回的结果；
- 解析器，这里的“html.parser”是Python标准库的表示，其他的解析器还有lmxl HTML、lxml XML、html5lib。

#### 2.2 BS库中的基本元素

###### 1）Tag
###### 2）Name
###### 3）Attributes
###### 4）NavigableString
###### 5）Commit

![](http://upload-images.jianshu.io/upload_images/4505098-6a0b34c35fde1796.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###### 基本元素的测试

```
import requests
from bs4 import BeautifulSoup

r=requests.get("http://www.zuihaodaxue.cn/Sport-Science-Schools-and-Departments-2016.html")
demo=r.content[:1000]
soup=BeautifulSoup(demo,"html.parser")
#print(soup.prettify())
print(soup.meta.title)
print(soup.meta.name)
print(soup.meta.parent.name)
print(soup.meta.attrs)
print(soup.meta.string)
>>><title>软科全球体育类院系学术排名 2016 | 最好大学网</title>
meta
head
{'charset': 'utf-8'}
None
```

#### 2.3 BS库与标签树

![](http://upload-images.jianshu.io/upload_images/4505098-2e38dbc244c49e36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![](http://upload-images.jianshu.io/upload_images/4505098-880f4aaf8bc4a1e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![](http://upload-images.jianshu.io/upload_images/4505098-458123f1ff949a5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 三、遍历标签树

#### 3.1上行遍历

###### 概述

根据第一个实例，<head>标签和<meta>标签是父子关系，所以称<meta>到<head>的遍历过程是上行遍历。

###### 方法
![](http://img.blog.csdn.net/20170318220032314)

###### example
```
for parent in soup.link.parents:
  if parent in None:
    print(parent)
  else:
    print(parent.name)
>>>meta
meta
meta
meta
head
html
[document]

***Repl Closed***
```

#### 3.2下行遍历

###### 概述

同理，反过来的话，<head>向<meta>遍历就是下行遍历。

###### 方法
![](http://img.blog.csdn.net/20170318215800921)

###### example
```
import requests
from bs4 import BeautifulSoup

r=requests.get("http://www.zuihaodaxue.cn/Sport-Science-Schools-and-Departments-2016.html")
demo=r.content[:1000]
soup=BeautifulSoup(demo,"html.parser")

for child in soup.html.contents:
  print(child)
>>><head>
<meta charset="utf-8">
<meta content="width=device-width, initial-scale=1" name="viewport">
<meta content="上海软科于2016年6月15日发布软科世界一流学科排名”2016。此次学科排名发布的学科集中在工程领域，包括化学工程、土木工程、电力电子工程、能源科学与工程、环境科学与工程、材料科学与工程、机械工程共7个学科。" name="description">
<meta content="最好大学网" name="author">
<link href="images/favicon.png" rel="shortcut icon"/>
<title>软科全球体育类院系学术排名 2016 | 最好大学网</title>
<link href="./houtai/templates/css/bootstrap.min.css" rel="stylesheet">
<link href="./houtai/templates/css/template.css" rel="stylesheet">
</link></link></meta></meta></meta></meta></head>
```

#### 3.3平行遍历 

###### 概述 

例如,<head>和<body>之间就是平行关系,在他们之间遍历就是平行遍历。

###### 方法

![](http://img.blog.csdn.net/20170318220147174)

###### example
```
import requests
from bs4 import BeautifulSoup

r=requests.get("http://www.zuihaodaxue.cn/Sport-Science-Schools-and-Departments-2016.html")
demo=r.content[:1000]
soup=BeautifulSoup(demo,"html.parser")

for sibling in soup.a.next_sibling:
  print(sibling)
>>>None
```

## 四、信息标记

信息标记的方式有三种：XML/JSON/YAML

## 五、BS库的prettify()方法
```
soup.prettify()
```
prettify算是一种初级的格式化输出，本文第一例就是用到了此方法。


***以后有更多补充***