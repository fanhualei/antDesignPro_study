# WebStorm 的使用



> 目录

* [安装与破解](安装与破解)
* [启动WebStrom](启动webstrom)
* [其他设置](其他设置)
  * [不能输入中文](不能输入中文)
  * [取消立即保存的设置](取消立即保存的设置)
  * [配置debug](配置debug)
* [版本控制](版本控制)



##安装与破解



> 参考网址：
>
 * [webstorm 2018 激活破解方法大全](https://blog.csdn.net/voke_/article/details/76418116)
* [Ubuntu18.04 安装Webstorm的方法](https://blog.csdn.net/zhang___gang/article/details/81489740)



### 下载与安装

> 直接去官网下载一个linux版本进行解压

```
官网地址：https://www.jetbrains.com/webstorm/

```



### 配置JDK

>  解压完后，[启动webStorm](#启动webStorm)，如果提示JDK没有安装，就修改webstorm.sh文件：

```
# ---------------------------------------------------------------------
# Locate a JDK installation directory which will be used to run the IDE.
# Try (in order): WEBIDE_JDK, webstorm.jdk, ./jre64, JDK_HOME, JAVA_HOME, "java" in PATH.
# ---------------------------------------------------------------------

JAVA_HOME=/opt/jdk1.8.0_161 
```

指定JAVA_HOME的路径







### 破解

```
使用网上介绍的方法３
１：下载补丁
２：复制到WebStorm目录下
３：编辑bin\webstorm64.vmoptions
4：添加-javaagent:../JetbrainsCrack.jar
＊　实际过程中，我就把补丁复制到webstorm根目录下就可以，没有第３与４步．
５：到这个网站获取验证码就可以了　http://idea.lanyus.com/
```

[参考](https://blog.csdn.net/voke_/article/details/76418116)







## 启动WebStrom

```
使用命令行启动
cd /opt/WebStorm-2018.2/bin/
sudo sh webstorm.sh
```



> 除了上述方法，也可以在桌面建立一个快捷菜单



## 其他设置

> 参考网址

* [Webstorm 超实用教程](https://www.jianshu.com/p/4ce97b360c13)





### 不能输入中文

> 重要的事情说三遍，一定要在文件的最前边

[webStorm Linux Ubuntu 中文搜狗输入问题](https://www.cnblogs.com/ediszhao/p/5864856.html)

```
1 打开安装路径下bin/webstorm.sh

　　vim ~/WebStorm-145.597.6/bin/webstorm.sh

2.在打开文件的最前面加入如下代码：

export XMODIFIERS="@im=fcitx" 
export GTK_IM_MODULE="fcitx" 
export QT_IM_MODULE="fcitx"
```









### 取消立即保存的设置

```
打开webstorm的setting,然后搜索safe write，设置１８０秒自动保存
```



### 配置debug

这样就可以直接运行了

![alt](imgs\debug-setting.png)





## 版本控制