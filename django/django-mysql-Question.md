Django连接 MySQL8.0时的配置问题和解决方案
=========

#### 一、环境

我安装的 Python、Django 和 MySQL 的版本是：

-  OS ：Mac OS 10.13.6
-  Python 3.6.5
-  Django 2.0.1
-  MySQL 8.0.11
-  MySQL 图形化工具：Navicat for MySQL
-  IDE ：PyCharm CE

以上是笔者的开发环境，配置情况和遇到的问题都在此基础上分析和解决。

#### 二、问题

当我在 IDE project 中的myBlog/settings.py文件内配置好之后：

```
DATABASES = {
    'default': {
        "ENGINE": "django.db.backends.mysql",
        "NAME": "news",
        "USER": "root",
        'PASSWORD': "xxxxx",
        'HOST': "localhost",
        "PORT": "3306",
        "CHARSET": "utf8",
    }
}
```

 运行服务器，连接 MySQL 时，出现了报错：

![Question](./images/questionDjangoMySQL.png)

报错的代码显示是：

这个主机名为 localhost，用户名为 root 的连接被拒绝。并且说，正在使用的密码为空！

#### 三、解决过程

1. 首先查看 settings.py 数据库密码配置是否跟 MySQL 的 root 用户的密码一致。经过检查，相同。而 CSDN 里面大多数说法是密码错误，笔者认为这不全面。

2. 接下来去查看 Django 和 MySQL文档，发现 MySQL8.0以后，在密码的传输时，加密方式做了调整，经过编译的密码字符和 Django 配置文件中进行匹配验证时，自然不匹配。

3. 此时，登录 MySQL，重新修改密码，并刷新 MySQL 环境。

4. 具体步骤如下：

###### Step1： 打开 terminal ，登录 MySQL。

```            
MacBook:~ mahaibin$mysql -h localhost -u root -p
Password:XXXXX
```

###### Step2：进入数据库

```
Use yourDatabase;
```

###### Step3：修改密码，刷新

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'new-password'; 
FLUSH PRIVILEGES;
```

###### Step4：进入 Django project 的myBlog/settings.py，修改为新密码

```
DATABASES = {
    'default': {
        "ENGINE": "django.db.backends.mysql",
        "NAME": "news",
        "USER": "root",
        'PASSWORD': "new-password",
        'HOST': "localhost",
        "PORT": "3306",
        "CHARSET": "utf8",
    }
}
```

###### step5：验证连接，做数据迁移

```
python3 manage.py makemigrations
python3 manage.py migrate  
```

结果如下:

![result](./images/mysql数据迁移.png)

###### step6： 打开 terminal，连接 server，在浏览器中查看

```
cd XXXX/myBlog/  #manage.py 所在目录
python3 manage.py runserver
```

如下：

![localhost](./images/localhostPolls.jpg)

#### 四、参考文档
https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-DATABASES