+++
title = "Cygwin安装phpredis拓展"
categories = ["Cygwin"]
tags = ["Cygwin"]
date = "2021-08-01T11:33:28+08:00"
draft = false
header_images="static/images/cygwin-install-phpredis.webp"
+++
>phpredis是一个用C所编写的php拓展模块，用来链接并且操作Redis数据库上的数据

这个是phpredis在github项目主页上的简介，就是一款对redis操作的拓展，在swoft的composer.json文件里，phpredis是必须要安装的拓展，下面就简单分享下Cygwin下安装phpredis的方法。


## 一、下载phpredis源码包 ##
在Cygwin终端下创建一个下载目录，用 wget来下载源码包，因为我使用的环境是Cygwin提供的php7.3.7版本，所以使用的phpredis是3.1版本，如果没有wget，可以使用安装Cygwin的安装包选择安装wget拓展。

1. 下载phpredis3.1源码包

` wget http://pecl.php.net/get/redis-3.1.0.tgz `

2. 解压源码包

`  tar xzf redis-3.1.0.tgz `

3. 进入源码目录并且执行生成编译文件
   
` cd redis-3.1.0 `
` phpize  `

![下载源码包解压并且执行生成编译文件](/images/1624677894616.png)


## 二、执行配置  ##
这里需要注意的是，执行配置时需要指定到Cygwin下php-config文件，指令如下。
` ./configure --with-php-config=/bin/php-config`
![执行配置](/images/1624678635684.png)

## 三、执行编译和安装  ##
跑完执行配置后，就是最关键的编译和安装了，指令如下。
` make && make install `
![执行编译和安装](/images/1624678803373.png)

## 四、执行测试和添加配置到php.ini  ##
执行`make test` 查看上一步是否存在纰漏；
![执行测试](/images/1624679066762.png)
打开Cygwin的*php.ini*，目录在 `/etc ` 下，加入`extension=redis.so`；最后再执行`php -m`查看列表是否存在redis，如果存在就安装完成了。