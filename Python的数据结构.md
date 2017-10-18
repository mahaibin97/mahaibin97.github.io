
Python基础知识- - -数据结构
====

Python开发语言有5种数据类型：数字类型（number）、字符串类型（string）、元组（ Tuple）、列表（list）、字典（dictionary）。

接下来，我分别以实例说明6中数据类型。

###number型

**number**型数据在Python中，称为数字类型，就是1、-2、1.3这样的数据。

```
a=10
b=1.9
print(a+b)
>>>10.9
```

具体的，Python支持四种不同的数字类型：
- int（有符号整型）
- long（长整型[也可以代表八进制和十六进制]）
- float（浮点型）
- complex（复数）

###string型

**string**型数据就是我们常说的字符串类型：'I am mahaibin'、'NanJing'、'j123'...

```
str='Hello world'
print(str[1])
>>>e
```

###list类型

**list**类型是Python中最常用的数据类型之一，它的中文名就叫“列表”。
```
list1=[1,2,3,4,5]
list2=['a','b','c']
```

###Tuple类型

**tuple**,元组类型，它类似于列表，但以一对圆括号为标识，且操作功能没有列表齐全。
```
tuple = ( 'runoob', 786 , 2.23, 'john', 70.2 )
```

###dictionary类型

**dictionary**，字典类型。以一对大括号为标识符，由一对键值对构成（key：value）。
```
dic1={'name':'kevin','age':30}
print(dict1[1].value)
>>>30
```

###file文件

**file**，文件类型。文件，很大程度上算不上是一种数据类型，而是程序语言中独立的存在。尽管如此，我还是要在Python的数据结构中提到**文件**。


详细请参考[Python Documentation](https://docs.python.org/3/)
