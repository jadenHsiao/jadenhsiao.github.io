+++
title = "Windows10 安装 Windows Subsystem for Linux"
categories = ["WSL"]
tags = ["WSL"]
date = "2021-08-28T00:33:28+08:00"
draft = false

+++

> 适用于 Linux 的 Windows 子系统可让开发人员按原样运行 GNU/Linux 环境 - 包括大多数命令行工具、实用工具和应用程序 - 且不会产生传统虚拟机或双启动设置开销。

上面这段是微软官方对于 Windows 子系统的简介，通过 WSL ，可以在 Windows 10 上安装诸如 Ubuntu、Kali、Debian等Linux的发行版，接下来，我将简单分享下安装 WSL 的流程和如何开启远程登录访问。

## 一、开启 WSL 支持  ##

WSL 作为拓展功能存在于 Windows 10 ，因此它不是默认开启的，需要我们手动启用该功能。

![开启 WSL 支持步骤一](/images/Snipaste_2021-08-28_00-36-42.png)

1. 使用 *Win* + *R*  键，启动“运行”工具，输入 control ，回车，然后再在弹出的“控制面板”界面，点击“程序和功能”；

![开启 WSL 支持步骤二](/images/Snipaste_2021-08-28_00-40-06.png)

 2. 点击“启用或关闭 Windows 功能”，在弹出的 “Windows 功能”选择“适用于 Linux 的 Windows 子系统”，点击“确定”，等待安装，重启。

    

## 二、下载安装 Linux 发行版和初始化系统 ##

![下载安装 Ubuntu](/images/Snipaste_2021-08-27_00-38-27.png)

1. 打开 Microsoft Store ，搜索 *Ubuntu* ，在结果内容中选择你要安装的版本，点击“获取”，这里作者选择的版本是 18.04，各位可以根据自己的开发需求，选择不同的版本。 

![初始化系统](/images/Snipaste_2021-08-27_00-48-08.png)

2. 下载安装完成后，在开始菜单就有 Ubuntu18.04 的项目，第一次点击它后，会提示需要等待几分钟来安装，安装到最后一步，提示需要创建一个 Unix 账户，按照指引填写账号，密码，再次确认密码，按下回车，看到 *Installation successful* ，就是完成安装和初始化了。



## 三、为系统创建一个 Root 用户和修改镜像源 ##

![创建 Root 用户](/images/Snipaste_2021-08-27_23-10-36.png)

1. 输入 ``` sudo passed root```，先验证当前账号的密码，然后填写 root 账户的密码和确认即将设置的密码，按下回车后，如果看到 *password updated successfully*  就是已经创建成功了。

![修改镜像源](/images/Snipaste_2021-08-28_01-05-45.png)

 2. 这里所使用的是中科大的镜像，使用```cd /etc/apt``` 进入 apt 的目录，用 Vim 编辑 sources.list，在文件的开头添加以下内容；

    ```shell
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
    ```

3.再执行 ```sudo apt update && sudo apt upgrade -y```，用来检查更新和更新软件包。



## 四、安装新版 ssh 和配置远程访问

1. 先执行 ```sudo apt automove --purge openssh-server ```卸载旧版本的 ssh，再执行 ```sudo apt install openssh-server -y```，安装完成后，进入到系统根目录，执行 ```sudo service ssh --full-restart```，启动ssh。

   ![编辑 ssh 配置文件](/images/Snipaste_2021-08-28_01-17-20.png)

2. 编辑 ```/etc/ssh/sshd_config```，解开  *Port* 注释，并且设置端口为 2222（因为宿主机的 22 端口另有他用，故设置为 2222 端口），加入 *PermitRootLogin*，同时设置值为 yes（这个主要是开启允许 root 账户远程登录），最后再加入 *PasswordAuthentication*，值为 yes（这个主要是开启密码验证登录），最后保存文件。

   

   

   ![远程登录](/images/Snipaste_2021-08-28_01-22-47.png)

完成以上操作后，打开 *SecureCRT*，选择“快速链接”，主机名为当前宿主机的 *ipv4* 地址，端口为 2222，用户名为 *root*， 密码为上面第三步之一所设置的，登录后就可以看到 Ubuntu 的操作区域了。

![远程登录](/images/Snipaste_2021-08-28_01-24-28.png)

## 五、启动和停止 WSL 服务

  ![启动和停止 WSL 服务](/images/Snipaste_2021-08-28_23-36-47.png)

如果是需要启动的话，可以在开始菜单直接点击安装的 *Ubuntu*，或者使用 Powershell 执行 ```net start LxxsManager```，停止的话，依旧是在 Powershell 执行 ```net stop LxxsManager```即可。

就可以在 WSL 开始进行愉快的开发体验了，enjoy。

