---
layout: post
title: "Linux（二）树莓派（linux）安装scipy"
catalog: true
header-style: text
tag:
    - Scipy
    - Linux
---
在树莓派上装scipy，老是报错，使用miniconda3装也是报错，尝试了好几种方法，最后还是conda命令好啊，**以下操作都是在conda环境下**：
## 成功案例
```linux
conda install -c anaconda scipy
```
conda会自动下载相关依赖
## 失败案例1（使用pip直接安装）
```linux
pip install scipy
```
告诉我缺少lapack、blas依赖
## 失败案例2（使用pip安装依赖）
### 命令
直接安装缺少依赖
```linux
sudo apt-get install libblas-dev
sudo apt-get install liblapack-dev
sudo apt-get install gfortran
sudo apt-get install python-scipy

 yum install lapack lapack-devel blas blas-devel
 pip install numpy
 pip install scipy
```
报错:yum命令没找到
### 安装yum
[安装yum](https://blog.csdn.net/u011322112/article/details/115365096)
```linux
rpm -ivh yum-4.2.23-3.oe1.noarch.rpm
```
运行命令显示以下错误：
![报错](/img/树莓派安装scipy/报错.png)
再下alien，搁这套娃
### 使用alien
```linux
sudo apt-get install alien
sudo alien xxxx.rpm #将rpm转换位deb，完成后会生成一个同名的xxxx.deb
sudo dpkg -i xxxx.deb #安装生成后的文件名
```
下一步还报错找不到yum命令，我不装了

## 失败案例3（whl+setup.py安装）
### 查看系统型号

```linux
uname -a ## 查看内核/操作系统/CPU信息 
getconf LONG_BIT #查看位数
```
![系统型号](/img/树莓派安装scipy/系统型号.png)
armv7l是32位的
### 找源
去清华源找对应的whl文件：[https://pypi.tuna.tsinghua.edu.cn/simple/scipy/](https://pypi.tuna.tsinghua.edu.cn/simple/scipy/)

这里我没找到whl文件，下载的是tar.gz文件，然后执行
```linux
tar -zxvf scipy-1.5.4.tar.gz
cd scipy-1.5.4
sudo python setup.py install
```
直接程序执行起来没完了，也不报错，也不终止，我强行终止了，放弃

## 其他尝试
安装numpy+mkl、添加conda源等等