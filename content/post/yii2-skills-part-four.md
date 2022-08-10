+++
title = "Yii2使用技巧（四）- 将Yii整合到GatewayWorker"
categories = ["PHP","Yii"]
tags = ["PHP","Yii"]
date = "2022-07-18T15:29:28+08:00"
draft = false

+++

> *Yii*作为FPM框架，主要针对业务开发方面，因为本身并不支持网络层面编程，所以需要引入其他网络框架，这里我选择的是基于*WorkerMan*的*GatewayWorker*，来处理业务里需要的*WebSocket*；但是我又想在*GatewayWorker*中使用*Yii*的例如*Active Record*、*ArrayHelper*等上手的特性，所以本篇文章将对于如何整个进行说明。

## 一、下载*GatewayWorker*
这里提供了[下载*GatewayWorker*](https://www.workerman.net/doc/gateway-worker/README.html)的链接地址，下载后，将压缩包的内容解压到*Yii2*的项目目录内。

## 二、修改*start_businessworker.php*
> BusinessWorker类其实也是基于基础的Worker开发的。BusinessWorker是运行业务逻辑的进程，BusinessWorker收到Gateway转发来的事件及请求时会默认调用Events.php中的onConnect onMessage onClose方法处理事件及数据，开发者正是通过实现这些回调控制业务及流程。

上述这段引用是*WorkerMan*对于该生命周期中，业务逻辑处理所在位置的说明；所以*Yii*的操作就在这个环节上；打开*Applications*目录，修改*start_businessworker.php*，加入以下代码块：
```php
<?php

//...

require_once __DIR__ . '/../../vendor/autoload.php';

defined('YII_DEBUG') or define('YII_DEBUG', true);
defined('YII_ENV') or define('YII_ENV', 'dev');

$config = require(__DIR__ . '/../../config/web-socket.php');
require(__DIR__ . '/../../vendor/yiisoft/yii2/Yii.php');

(new \yii\web\Application($config))->run();

if(!defined('GLOBAL_START'))
{
    Worker::runAll();
}
```
在``Worker::runAll()``，前面先引用*composer*的自加载，然后`$config`可以自己定义配置文件，再引入*Yii*的核心文件，最后一步将其实例化即可，其实这个流程就和*Yii*框架本身*public/index.php*是一致的。

## 三、用*Yii*的编码风格编写*GatewayWorker*的业务代码
在*Applications*目录下的*Events.php*中就可以愉快使用原先框架的模型文件进行增删改查，工具类函数进行数组操作了。

注意：引用类时需要使用完全限定名称，例如``use \app\models\Example;``

## 参考链接
* [在第三方系统内使用 Yii](https://www.yiiframework.com/doc/guide/2.0/zh-cn/tutorial-yii-integration#using-yii-in-others)
* [命名空间和动态语言特征](https://www.php.net/manual/zh/language.namespaces.dynamic.php)
* [GatewayWorker](https://www.workerman.net/doc/gateway-worker/README.html)

