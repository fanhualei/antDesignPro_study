# Linux 下命令行Git的使用





* [Git常用命令使用大全](https://www.cnblogs.com/Gxiaopan/p/6714539.html)

*  [git的一些基础命令](http://www.cnblogs.com/libin-1/p/5918468.html)



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



## 基本操作





