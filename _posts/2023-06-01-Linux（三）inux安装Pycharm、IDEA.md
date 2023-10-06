---
layout: post

[//]: # (title: "Java（二）Java并发编程")
catalog: true
header-style: text
tag: 
    - Java
    - 多线程
---

以pycharm社区版为例，IDEA安装过程同理，pycharm专业版可能更繁琐些，我用社区版就够了
# 安装pycharm

## 先看linux 架构
```linux
uname -a
```
![在这里插入图片描述](/img/Linux安装Pycharm/系统型号.png)
我是64位的服务器，选arm64
## 下载和解压
pycharm 官网：[https://www.jetbrains.com/pycharm/download/#section=linux](https://www.jetbrains.com/pycharm/download/#section=linux)
![在这里插入图片描述](/img/Linux安装Pycharm/官网.png)
下载后上传至服务器，在当前路径下解压
```linux
tar zxf 压缩包名
```
## 尝试启动
这个时候去解压包的bin文件下,启动pycharm
```linux
cd ~/Pycharm/pycharm-community-2023.1.2/bin/
./pycharm.sh
```

### 编译运行的java版本不匹配
他提示我有错：
```
com/intellij/util/lang/PathClassLoader has been compiled by a more recent version of the Java Runtime (class file version **61.0**), this version of the Java Runtime only recognizes class file versions up to 52.0
```
大概意思是运行时Java版本与编译时使用的Java版本不兼容，下载jdk，需要和编译时版本相同
```
JDK 19 = 63,
JDK 18 = 62,
JDK 17 = 61,
JDK 16 = 60,
JDK 15 = 59,
JDK 14 = 58,
JDK 13 = 57,
JDK 12 = 56,
JDK 11 = 55,
JDK 10 = 54,
JDK 9 = 53,
JDK 8 = 52,
JDK 7 = 51,
JDK 6.0 = 50,
JDK 5.0 = 49,
JDK 1.4 = 48,
JDK 1.3 = 47,
JDK 1.2 = 46,
JDK 1.1 = 45.0-45.6
```

我去下61也就是jdk17,下载的时候我选第三个,**注意aarch64和x64不同**
![在这里插入图片描述](/img/Linux安装Pycharm/安装包版本.png)
下完了解压
```linux
tar zxf 压缩包名
```
添加环境路径
```linux
cd ~
vim .profile

# 末尾添加下面这些
# java Environment
JAVA_HOME=/home/jkx/Pycharm/jdk-17.0.7/
JRE_HOME=$JAVA_HOME #jdk10以后解压后是没有jre目录，直接写$JAVA_HOME 10以前的需要加上jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH
# 加完了

source .profile
```

### IDE Already Running
这个时候能运行了，但是报错，没有pycharm界面回显
```
IDE Already Running
Cannot connect to already running IDE instance.
CannotActivateException: /home/jkx/.config/JetBrains
```
由于PyCharm已经在后台运行或者之前的一个会话没有正常关闭导致的
可以尝试：

1. 结束正在运行的PyCharm进程：
在终端中运行以下命令，以终止所有与PyCharm相关的进程：
```linux
ps -ef | grep pycharm
#第二列是pid
kill pycahrm-pid
```
2. 删除PyCharm的运行锁文件：
在终端中运行以下命令，删除可能导致此错误的锁文件：
```linux
sudo rm ~/.config/JetBrains/<PyCharm 版本号>/*.lock
```
3.清除PyCharm的缓存目录：
在终端中运行以下命令，清除PyCharm的缓存目录：
```linux
sudo rm -rf ~/.cache/JetBrains/<PyCharm 版本号>/
```

**注**：pycharm版本号自己查，找到对应的文件夹，文件夹可能没权限进，需要提权
```linux
sudo chmod -R 777 文件夹
```
-R 是指级联应用到目录里的所有子目录和文件
777 是所有用户都拥有最高权限
## 启动成功
图不放了。

参考：
版本号对应：https://blog.csdn.net/lzhfdxhxm/article/details/117018384
文件夹提权：https://blog.csdn.net/g313105910/article/details/117606612



