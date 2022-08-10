+++
title = "Ubuntu 安装 Swift"
categories = ["WSL","Swift"]
tags = ["WSL","Swift"]
date = "2021-08-29T21:12:28+08:00"
draft = false

+++
>Apple 公司的 Swift 不仅在 MacOS 上运行，还支持在 Windows、Linux 编写程序；

笔者本人日常的开发环境为 Windows，但是 Windows 安装 Swift 需要安装 Visual Studio，这件庞然大物平时比较少用，所以就不打算直接在 Windows 上安装，本人的电脑上已经安装了 Ubuntu ，所以就选择在 WSL 环境安装 Swift，接下来，我将简单分享下在 Ubuntu 安装 Swift 的流程。

## 一、安装对应 Linux 所需的依赖

![不同的 Linux 发行版所需安装的依赖](/images/Snipaste_2021-08-29_22-04-09.png)

Swift 的官网说明了，在不同的 Linux 发行版下所需要的依赖，而我的 WSL 所安装的 Linux 发行版为 Ubuntu 18.04，所以对应选择安装依赖。

![执行安装依赖](/images/Snipaste_2021-08-29_13-55-58.png)

## 二、下载对应系统版本的 Swift 源码包和秘钥文件

![不同系统的源码包和秘钥](/images/Snipaste_2021-08-29_22-20-35.png)

依照不同的系统提供了不同的源码包和校验秘钥，这里我们将下载 Ubuntu 18.04 的源码包和秘钥文件

![下载源码包](/images/Snipaste_2021-08-29_13-59-49.png)

首先，使用 wget 下载 Swift 源码包；

![解压源码包](/images/Snipaste_2021-08-29_14-10-53.png)

下载完成后，执行 ```tar -xzf``` 解压源码包；

![下载秘钥文件](/images/Snipaste_2021-08-29_14-55-03.png)

下载秘钥文件。

## 三、配置到环境变量

![配置环境变量](/images/Snipaste_2021-08-29_14-32-11.png)

在 Root 身份下，执行 ```cd /etc ```，进入到 etc 目录，然后执行 ```vim profile```，编辑该文件；在该文件的添加 ```export PATH=/swift/usr/bin:"${PATH}"```，其中 swift 的运行目录就根据你解压的路径来决定。

## 四、导入 PGP 秘钥到系统和校验 PGP 秘钥

![导入 PGP 秘钥到系统](/images/Snipaste_2021-08-29_14-39-32.png)

按照官方指引，执行以下两个命令都可以进行导入秘钥：

```
gpg --keyserver hkp://pool.sks-keyservers.net \
      --recv-keys \
      '7463 A81A 4B2E EA1B 551F  FBCF D441 C977 412B 37AD' \
      '1BE1 E29A 084C B305 F397  D62A 9F59 7F4D 21A5 6D5F' \
      'A3BA FD35 56A5 9079 C068  94BD 63BC 1CFE 91D3 06C6' \
      '5E4D F843 FB06 5D7F 7E24  FBA2 EF54 30F0 71E1 B235' \
      '8513 444E 2DA3 6B7C 1659  AF4D 7638 F1FB 2B2B 08C4' \
      'A62A E125 BBBF BB96 A6E0  42EC 925C C1CC ED3D 1561' \
      '8A74 9566 2C3C D4AE 18D9  5637 FAF6 989E 1BC1 6FEA'
```

或者：

```
wget -q -O - https://swift.org/keys/all-keys.asc | \
  gpg --import -
```

![校验秘钥](/images/Snipaste_2021-08-29_14-55-53.png)

进入到第一步所下载秘钥包的目录，执行 ```gpg --verify swift-<VERSION>-<PLATFORM>.tar.gz.sig```，完成校验。

## 五、校验安装结果

![导入 PGP 秘钥到系统](/images/Snipaste_2021-08-29_15-00-15.png)

执行 ```swift --version```，如上图一样即为安装完成。


