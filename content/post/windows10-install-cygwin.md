+++
title = "Windows10安装Cygwin"
categories = ["Cygwin"]
tags = ["Cygwin"]
date = "2021-08-01T11:33:28+08:00"
draft = false
header_images="/images/windows10-install-cygwin.png"
+++

> What Is Cygwin?
Get that Linux feeling - on Windows

这个是[Cygwin](http://cygwin.org/ "Get that Linux feeling - on Windows")项目主页上的简介，安装它，可以得到一个类似Linux的Shell环境，在其中你可以使用绝大部分Linux软件和功能。如Gcc,Make,Vim,Emacs等等，作为一个php开发者，使用*Cygwin*的目的是为了运行*Swoole*，接下来，我将简单分享下安装*Cygwin*的流程和我们安装*swoole*需要的组件包。


## 一、从项目主页下载*Cygwin*安装包 ##
![Cygwin下载页面](/images/1624781189842.webp)
这个安装包不单单只是安装*Cygwin*，还能用来安装我们需要的组件包。

## 二、安装*Cygwin* ##
1. 选择组件包的下载方式
![选择组件包的下载方式](/images/1624781844331.webp)
    这里因为我使用了网络工具，网络环境相对来说比较好，所以我选择*Install from Internet*。

2. 选择*Cygwin*的安装路径
   ![选择Cygwin的安装路径](/images/1624781935840.webp)
    安装包提示我们最好选择在盘符的根目录下作为*Cygwin*的运行目录。

3. 选择组件包的下载路径
   ![选择组件包的下载路径](/images/1624782083039.webp)

4. 选择链接方式
   ![选择链接方式](/images/1624782150362.webp)
    这里就根据你自己的网络环境来选择对应的链接方式。

5. 选择下载组件包的镜像地址
   ![选择下载组件包的镜像地址](/images/1624782354070.png)
    选择下载组件包的镜像地址；
   ![根据镜像加载组件包列表](/images/1624782435987.png)
    这里也是根据网络环境，国内最好选择网易的镜像源，下载速度快且稳定。

6. 选择安装组件包
   ![选择安装组件包](/images/1624782591653.png)
    这里就根据你开发环境的需要来安装组件包。

7. 执行安装组件包和*Cygwin*
   ![执行安装组件包和Cygwin](/images/1624782777091.png)
    点击**下一页**后执行安装*Cygwin*和组件包，完成后就可以打开*Cygwin64 Terminal*来体验了。
    ![完成安装](/images/1624782943619.png)

## 三、安装组件包 ##

这里我列举出我们运行*php*运行环境和编译*Swoole*所需要的组件包列表。
![组件包列表](/images/1624783146587.png)
这一步，可以在执行安装组件包和*Cygwin*上进行；特别要提示的是，我们一旦选择好*php*的版本后，*php*相关组件包的安装都需要对应好版本。

以上步骤完成后，就可以在*Cygwin*上进行php开发了，enjoy🤗！！