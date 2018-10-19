Git同步本地项目到远端仓库的 shell
======


###### step 1：在本地建立 Git 仓库，并添加本地项目进入仓库

```
Cd Users/mahaibin/python3/BestOne/
Git init
Git add .   // 添加该目录下所有项目进入仓库
Git commit -m “add BestOne polls APP"
```

###### Step 2：连接本地仓库和远端仓库 github.com/mahaibin97/Django-learning

```
git remote add origin git@github.com:mahaibin97/Django-learning
git push -uf origin master
```

