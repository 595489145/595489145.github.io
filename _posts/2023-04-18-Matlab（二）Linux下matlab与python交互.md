---
layout: post
title: "Matlab（二）Linux下matlab与python交互"
catalog: true
header-style: text
tag:
    - Matlab
    - Linux
---
## Anaconda 安装
[linux64安装Anaconda](https://blog.csdn.net/arno_an/article/details/105229780) 
[树莓派安装Anaconda](https://blog.csdn.net/qq_45266796/article/details/115128171)
注意linux系统，正常服务器应该是64位的，后缀是x86_64
树莓派是32位的，要别的包
建议用anaconda，不然装包版本不统一太难受了
## matlab安装
[安装matlab](https://blog.csdn.net/weixin_44987694/article/details/129341420?spm=1001.2014.3001.5502) 

## 设置环境变量
```linux
vim ~/.bashrc
export PATH="/home/jkx/Matlab_R2022a/bin:$PATH"    #matlab安装路径
export PATH="/home/jkx/.conda/envs/PhyID/bin/:$PATH" #python安装路径
source ~/.bashrc
```
## matlab检测python环境
在matlab终端中输入：
```matlab
pyenv
```
![版本](/img/Linux下matlab与python交互/版本.png)
若想修改环境变量，只需定位到python解释器路径，然后使用
```matlab
pyenv('Version','解释器路径')
```
注意matlab版本和python解释器版本匹配，可以从官网查到![请添加图片描述](https://img-blog.csdnimg.cn/a77512d681b04fa6af5e3689b29e9005.png)
## matlab 调用python
将matlab文件和python文件放在一个目录下
```matlab
obj = py.importlib.import_module('point_detection'); %输入你的模块名
py.importlib.reload(obj);
index=py.point_detection.PELT(vvv); %调用模块中的函数
```