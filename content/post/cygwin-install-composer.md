+++
title = "Cygwin安装composer"
categories = ["Cygwin"]
tags = ["Cygwin"]
date = "2021-08-01T11:33:28+08:00"
draft = false
header_images="static/images/cygwin-install-composer.png"
+++
>A Dependency Manager for PHP

上面是引用composer官网的说明，作为一个依赖管理工具，我们可以很轻松把其他开发者开发的优秀拓展引用到我们的项目上，提高我们的开发效率，下面就简单分享下Cygwin下安装composer的方法。

## 一、下载composer并且移动到可执行目录 ##

1. 在下载目录使用*wget*下载*composer.phar*

` wget https://install.phpcomposer.com/composer.phar `

2. 移动到*Cygwin*的可执行目录*bin*

` mv composer.phar /bin `

## 二、设置别名和测试 ##
这一步主要为了可以在*Cygwin*的终端全局可以使用*composer*指令。
`alias composer="php /bin/composer.phar"`
![设置别名和测试](/images/1624694369706.png)
在终端输入`composer`回车，却发现报了一个致命错误，未捕获的错误，类*phar*未找到，这个问题还是比较好解决的，只需要打开*Cygwin*的安装包，搜索关键字*phar*，在搜索结果里选择*php-phar*，然后在*Current*选择对应的php版本，*src*勾选安装源码，执行下一步安装即可。
![Cygwin安装phar拓展](/images/1624694642931.png)

## 三、设置国内镜像 ##
因为网络环境的原因，所以设置为国内的镜像可以提高我们下载更新拓展包的速度，目前国内有多家镜像提供商，这里列举几个比较常用的。
|提供方|镜像地址|
|:-|:-:|
|腾讯云|https://mirrors.cloud.tencent.com/composer/|
|阿里云|https://mirrors.aliyun.com/composer/|
|Packagist / Composer（中国全量镜像）|https://packagist.phpcomposer.com|
|华为云|https://mirrors.huaweicloud.com/repository/php/|

设置方法：使用以下指令，`composer config -g repos.packagist composer 镜像地址`
![设置完成后打开](/images/1624695464175.png)
😄enjoy!!

