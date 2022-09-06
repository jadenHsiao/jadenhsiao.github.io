+++
title = "Cygwin安装apt-cyg"
categories = ["Cygwin"]
tags = ["Cygwin"]
date = "2021-08-01T11:33:28+08:00"
draft = false
header_images="static/images/cygwin-install-apt-cyg.png"
+++
>在*Linux*系统下，如果我们需要一些软件包的话，可以使用软件包管理进行下载、安装；例如在**Ubuntu**下的*apt-get*，又或者在**CentOS**下的*yum*，都能帮助我们快速的下载安装软件包，同时又能自动安装软件包对应的依赖包，从而提高我们的开发效率。

既然*Cygwin*是模拟*Linux*的运行环境，所以，毫无例外，它也提供了软件包管理工具*apt-cyg*，下面，我就简单分享下安装*apt-cyg*的方法。

## 一、执行下载*apt-cyg*可执行文件 ##
创建一个下载目录，在该目录下执行指令，`wget https://raw.githubusercontent.com/transcode-open/apt-cyg/master/apt-cyg`。


## 二、执行安装  ##
1. 下载完成后，将*apt-cyg*执行安装到可执行目录，*Cygwin*的可执行目录是*bin*，指令为`install apt-cyg /bin`。
2. 安装完成后就可以执行`apt-cyg`来测试是否安装完成。
   

## 三、设置镜像源和更新源 ##
因为网络环境的问题，使用国内的镜像源可以提高我们在安装、更新软件包的速度，这里推荐使用网易的镜像源，执行指令`apt-cyg mirror http://mirrors.163.com/cygwin`，然后再更新源`apt-cyg update`。
![安装流程](/images/1624804899614.png)
完成以上步骤就可以使用*apt-cyg*安装需要的软件包了。




