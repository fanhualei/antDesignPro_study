# Linux 下命令行Git的使用





* [Git常用命令使用大全](https://www.cnblogs.com/Gxiaopan/p/6714539.html)

* [git的一些基础命令](http://www.cnblogs.com/libin-1/p/5918468.html)

* [阮一峰-常用 Git 命令清单](http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html)



## 入门



> 在github上建立一个工程后，会有一个入门的提示

### …or create a new repository on the command line

```
echo "# antDesignPro_study" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/fanhualei/antDesignPro_study.git
git push -u origin master
```

### …or push an existing repository from the command line

```
git remote add origin https://github.com/fanhualei/antDesignPro_study.git
git push -u origin master
```

### …or import code from another repository

You can initialize this repository with code from a Subversion, Mercurial, or TFS project.



### 如何解决每次都要输入密码的问题

```
git remote rm origin 
git remote add origin https://fanhualei:madengxia123@github.com/fanhualei/antDesignPro_study.git

```







## 常用操作



```
git add ./doc

git commit -m "send commit"

git commit ./doc/*.* -m "send commit"

git push -u origin master



```



