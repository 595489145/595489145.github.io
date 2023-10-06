---
layout: post
title: "Matlab（一）Linux无图形界面安装matlab（正版）"
catalog: true
header-style: text
tag:
    - Matlab
    - Linux
---

## 1、matlab镜像下载及上传
我是从学校正版软件上下载的Matlab R2022a_Linux.iso镜像文件，网上有好多破解版安装，随意
上传至服务器可以使用Xshell7+Xftp7,MobaXterm,随意
最好上传到你的家目录（/home/你的用户名）
## 2.挂载镜像
```Linux
cd ~ #切换到家目录
mkdir matlab #存放挂载文件
sudo mount -r R2022a_Linux.iso matlab #挂载，可以看到文件夹里多了一堆东西
```
MobaXterm左侧文件导航栏：
![安装路径](/img/Linux安装Matlab/安装路径.png)



Linux mount命令用于挂载Linux系统外的文件

**root用户或sudo**才拥有mount权限
若普通用户运行sudo报错：**“xxxis not in the sudoers file.  This incident will be reported. ”**，需要使用管理员用户给普通用户设置sudo权限
执行：
```Linux
su rootname #切换到root用户
sudo usermod -a -G sudo username #给你的用户sudo权限
su username(或者ctrl+D) 切换回原用户
```

## 3.编辑配置文件及非交互安装
### 说明书步骤：
![安装说明](/img/Linux安装Matlab/安装说明.png)
### 我们按着说明步骤做
#### 进入matlab官网获取许可证文件
进入[https://ww2.mathworks.cn/licensecenter/licenses/](https://ww2.mathworks.cn/licensecenter/licenses/)
点击你的许可证
![在这里插入图片描述](/img/Linux安装Matlab/获取许可证1.png)
在”安装与激活“中选择激活并获取许可证文件
![在这里插入图片描述](/img/Linux安装Matlab/获取许可证2.png)
选择”激活计算机“
选择版本，操作系统、主机ID（ifconfig ether内容）、计算机登录名（用户名）
复制安装密钥，并上传许可证license.lic
#### 非交互安装
```Linux
mkdir ~/Matlab_R2022a #创建matlab安装目录
touch ~/installer_input.txt  #创建安装配置文件
vim ~/installer_input.txt #编辑配置文件
## 输入i进入编辑模式
## 输入以下文字---------
	sudo tee ~/installer_input.txt &>/dev/null <<EOF
	destinationFolder=~/Matlab_R2022a  #安装目标文件夹
	fileInstallationKey=50874-33247-14209-37962-45495-25133-28159-33348-18070-60881-29843-35694-31780-18077-36759-35464-51270-19436-54668-35284-27811-01134-26918-26782-54088 #文件安装密钥
	agreeToLicense=yes  #同意许可
	outputFile=~/matlab_install.log #安装日志输入文件
	licensePath=~/license.lic #许可证目录
	EOF
## 输入Esc，退出编辑模式
## 输入wq，保存并退出

sudo chmod 444 ~/installer_input.txt  #修改安装配置文件的权限
sudo matlab/install -inputFile ~/installer_input.txt #从配置文件安装，这里会很慢，也没有输入，等一会就好了
sudo umount ~/matlab #取消挂载
```
## 4.运行Matlab
```Linux
~/Matlab_R2022a/bin/matlab #执行bin下的matlab脚本
```
若执行失败，让你激活的话，你在运行同目录下的activate_matlab文件激活
```Linux
~/Matlab_R2022a/bin/activate_matlab.sh ## 激活
```
激活成功就可以运行matlab了

## 5.添加变量
设置环境变量，输入matlab就可以运行
```Linux
vim ~/.bashrc ## 编辑配置
## 最后添加以下文字---------
export PATH="/home/username/Matlab_R2022a/bin:$PATH"
## alias matlab='/home/username/Matlab_R2022a/bin/matlab -nodesktop -nodisplay' #这里是设置matlab快捷方式，输入matlab相当于输入等号后面的指令，随意
## 输入Esc，退出编辑模式
## 输入wq，保存并退出
source ~/.bashrc ## 更新配置 
```
## 6.启动matlab
```Linux
matlab -nodesktop -nodisplay #无界面显示
```
![运行效果](/img/Linux安装Matlab/运行效果.png)
Linux后台运行脚本(可关闭远程终端)：
```linux
cd  程序目录 #一定要转到程序目录
nohup matlab -nodesktop -nosplash -r '程序名（不加.m）;exit' > /dev/null 2>&1 &
```

这个时候控制台会返回PID，若要杀死进程，只需要记下或者找到PID
```linux
ps -ef | grep 脚本名 #查找进程信息
kill PID号 #PID为第二列的数字
```

参考：
1.[linux 安装matlab](https://www.cnblogs.com/lvchaoshun/p/9746155.html#:~:text=%E4%BA%8C%E3%80%81%E5%AE%89%E8%A3%85MATLAB%201%E3%80%81%E6%8C%82%E8%BD%BD%E9%95%9C%E5%83%8F%E5%B9%B6%E5%BC%80%E5%A7%8B%E8%BF%90%E8%A1%8C%20install%20%E6%96%87%E4%BB%B6%20cd%20~%20#%20%E5%88%87%E6%8D%A2%E5%88%B0home%E7%9B%AE%E5%BD%95,Linux/R2016b_glnxa64_dvd1.iso%20matlab/%20#%20%E6%8C%82%E8%BD%BDdvd1%20sudo%20matlab/install%20#%20%E5%BC%80%E5%A7%8B%E5%AE%89%E8%A3%85)
2.[无图形界面Linux安装Matlab2020](https://zhuanlan.zhihu.com/p/394298249)
3.[从 Linux 系统提示符启动 MATLAB 程序](https://ww2.mathworks.cn/help/matlab/ref/matlablinux.html)
4.[Linux后台运行matlab脚本](https://www.cnblogs.com/jeshy/p/11756032.html)
