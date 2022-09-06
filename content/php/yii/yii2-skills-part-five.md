+++
title = "Yii2使用技巧（五）- 使用Sentry作为错误日志监控平台"
categories = ["PHP","Yii"]
tags = ["PHP","Yii"]
date = "2022-07-19T15:29:28+08:00"
draft = false

+++

> *Sentry* 是一个实时的事件日志和聚合平台，基于 *Django* 构建。使用它可以帮助我们记录下错误信息，官方提供的*phpSDK*,只需几行代码，就可以把他整合到*Yii*中，本篇文章是技巧三的延续。

## 一、注册*Sentry*账号
*Sentry*官方提供了线上版和*docker*部署两种方式，这里使用的是线上版，有兴趣独立部署的点击前往[github项目主页](https://github.com/getsentry/self-hosted)查看；
打开*Sentry*[注册](https://sentry.io/signup/)页面，填写对应公司信息和个人信息、邮箱、密码，该邮箱将会作为管理员邮箱。

![注册界面](/images/Snipaste_2022-07-19_16-28-22.jpg)

## 二、创建项目
注册完成后进入到控制台，点击右上角进入创建项目界面；

![控制台](/images/Snipaste_2022-07-19_16-45-29.jpg)

因为我们要在*Yii*中使用，所以选择*php*，填写项目名称，点击创建。

![创建项目](/images/Snipaste_2022-07-19_16-45-35.jpg)

创建完成后，在引导配置界面有我们等下需要用到的*dsn*，将其复制下来保存好。

![获取dsn](/images/Snipaste_2022-07-19_16-52-10.jpg)

## 三、整合到*Yii*中
1. 使用*composer*安装*sentry/sdk*，指令如下``composer require sentry/sdk --with-all-dependencies``；由于该包要求php版本不能低于8.0，所以要先对php环境配置好。

2. 将*dsn*设置到*Yii*的*params*中，在*config/params.php*的数组中加入以下代码块：
```php
<?php

return [
    'adminEmail' => 'admin@example.com',
    'senderEmail' => 'noreply@example.com',
    'senderName' => 'Example.com mailer',
    'dsn' => 'https://764e44dc5a984331bbe928d1f201fba8@o1261582.ingest.sentry.io/6439472'
];
```

3. 在模块的入口文件*init*方法加入*sentry*初始化相关，代码块如下：
```php
<?php
use function Sentry\init as sentry_init;

class Module extends \yii\base\Module{
    public function init(){
        parent::init();
        sentry_init(['dsn' => Yii::$app->params['dsn'] ]);
    }
}
```

4. 在错误拦截类中加入提交到*Sentry*相关
```php
<?php
namespace app\modules\api\exception;

use function \Sentry\captureException;

class ErrorHandler extends BaseErrorHandle
{

    /**
     * 错误处理
     * @param \Error|\Exception $exception
     * @return array
     */
    public function convertExceptionToArray($exception)
    {

        $value = parent::convertExceptionToArray($exception);
        // 加入此行
        captureException($exception);
        return [
            'code' => $value['code'] ,
            'message' => $value['message']
        ];


    }

}
```
## 四、测试并在控制台查看
在模块的控制器，加入`` throw new BaseException(401); ``，然后请求接口；
登录到控制台，点击对应的项目就可以查看到报错列表了。

![项目详情](/images/Snipaste_2022-07-20_09-35-13.jpg)

点击报错项，就可以查看到报错的具体信息了。

![报错详情](/images/Snipaste_2022-07-20_09-35-47.jpg)


