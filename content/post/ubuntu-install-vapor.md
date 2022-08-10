+++
title = "Ubuntu 安装 Vapor"
categories = ["Ubuntu","Swift","Vapor"]
tags = ["Ubuntu","Swift","Vapor"]
date = "2021-08-31T22:38:28+08:00"
draft = false
header_images="/images/Snipaste_2021-08-31_23-35-06.png"
+++

> Apple 公司的 Swift 不仅能够编写 MacOS 、IOS 端的应用，还能基于 SwiftNIO 编写服务端，在 Swift 服务端的阵营里，有 Kitura、perfect、Vapor 。

而今天，我将给大家介绍一款服务端框架 [Vapor](https://vapor.codes)，他在 [github](https://github.com/vapor/vapor) 收获高达 20.6k star 的服务端框架，同时支持 JWT、MySQL、Redis、MongoDB种种我们服务端开发所需要的组件，并且设计思路来源于 Laravel/Lumen（php开发者狂喜😆😆），同时拥有活跃的社区，并且随着 Swift 版本在不断地更新，所以，我毅然决然选择 Vapor 作为我 Swift 开发服务端的框架。

因为我的 MacOS 系统版本是 Majove，同时平常更加倾向于使用 Windows，所以就选择了 WSL 作为 Vapor 的开发环境，使用的是 Ubuntu18.04，接下来，将先介绍下在 Ubuntu 下如何安装 Vapor。


![下载 Vapor 源码](/images/Snipaste_2021-08-31_11-56-18.jpg)

1. 到 Vapor 的 github 项目主页上下载源码；

![编译源码](/images/Snipaste_2021-08-31_11-59-59.jpg)

2. 进入到 toolbox，执行 ```swift build -c release --disable-sandbox --enable-test-discovery```进行源码编译，在执行 ```mv .build/release/vapor /usr/local/bin``` 移动到执行目录；

![校验是否安装完成](/images/Snipaste_2021-08-31_12-41-01.jpg)

3. 执行 ```vapor --help```，如和上图所示，即为安装完成；

![创建项目](/images/Snipaste_2021-08-31_12-50-02.jpg)

4. 执行 ```vapor new hello -n```，创建第一个 Vapor 项目，看到如此大的水蒸气图案💧，就证明创建完成了；

![编译项目](/images/Snipaste_2021-08-31_12-55-35.jpg)

5. 执行 ```swift build```，编译 Vapor 项目文件；

![启动项目](/images/Snipaste_2021-08-31_12-55-56.jpg)

6. 启动项目，这时候看到命令行提示使用 127.0.0.1:8080 进行访问。

打开浏览器，访问 127.0.0.1:8080，就可以看到第一个路由的返回结果，“It work”了😀。

