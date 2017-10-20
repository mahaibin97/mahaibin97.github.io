<!--#!/usr/bin/env Markdown
# -*- coding: utf-8 -*-
# @Date    : 2017-10-19 15:41:10
# @Author  : kevin ma (mahaibin97@gmail.com)
# @Link    : http://mahaibin97.github.com
# @Version : $Id$-->


# Python技术文章 

## 一、基本
#### 1.1 标题
使用“#”，标题总共有6级。
># 一级
>## 二级
>### 三级
>#### 四级
>##### 五级
>###### 六级


#### 1.2 有序列表
-1
-2
-3

#### 1.3 链接

使用"[]()"。

[Python](http://www.python.org)

![photo](http://upload-images.jianshu.io/upload_images/259-0ad0d0bfc1c608b6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 1.4 引用

使用 '>'。

>技术、数据，才是未来！
>
>Data Time

**technology**

*data*
 *一盏灯*， 一片昏黄；**一简书**， 一杯淡茶。 守着那一份淡定， 品读属于自己的寂寞。
 保持淡定， 才能欣赏到最美丽的风景！ 保持淡定， 人生从此不再寂寞。
 
```
import requests
r=requests.request("GET","http://www.zhihu.com",timeout=30)
r.text[:100]
```

# 二、表格

### 2.1
|  table1                      |table2                      |table3    |
|------------------------------|----------------------------|----------|
|  table4                      |table5                      |table6    |
|  table7                      |table8                      |table9    |

### 2.2
dog | bird | cat
----|------|----
foo | foo  | foo
bar | bar  | bar
baz | baz  | baz


## source from： 

###### 1.[献给作者的Markdown说明](http://www.jianshu.com/p/q81RER)
###### 2.[Markdown语法说明](http://wowubuntu.com/markdown/)
