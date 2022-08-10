+++
title = "Yii2使用技巧（三）- 优雅处理业务错误"
categories = ["PHP","Yii"]
tags = ["PHP","Yii"]
date = "2022-07-18T09:50:28+08:00"
draft = false

+++

> 错误提示是程序运行重要的一环，可以让开发者及时知晓出错的位置和原因，但是在编码的时候，因拦截错误所编写的大量*try catch*会让代码后期难以维护，所以这里将会针对*Yii*，对程序的错误提示进行统一处理。

## 一、准备错误码类
在对应的模块目录下先创建一个*exception*的目录，用于放置我们处理错误的逻辑内容；在该目录下创建一个名为*ErrorCodes.php*的文件，该文件主要是放置错误码列表，一些通用的错误将通过该列表获取返回；代码如下：
```php
<?php
namespace app\modules\example\exception;

class ErrorCodes
{
    
        /**
         * 错误码列表
         * @return array
         */
        public static function errorCodeList()
        {
    
            return [
    
                401 => "Access Token 无效",
                403 => "该 App ID 已被封禁，请联系管理员",
                429 => "请求次数过多，请稍后再试",
                500 => "服务器内容错误",
    
                // TODO 其他的错误信息可以根据错误码内容写入
                101001 => "生成 Access Token 失败",
    
            ];
    
        }
    
}
```
上面的代码里放置了几个比较通用的错误码和提示语，这里可以根据业务需求进行自定义。

## 二、重写*Yii*错误控制类
```xml
<response>
<name>OK</name>
<message>Access Token 无效</message>
<code>401</code>
<status>200</status>
<type>app\modules\api\exception\BaseException</type>
</response>
```
上面的代码块原先*ErrorHandle*返回的内容，而该模块主要是提供给APP做接口调用，所以在返回内容需要较为简洁，因此需要重写*Yii*的*ErrorHandler*；   
```php
<?php
namespace app\modules\api\exception;

use yii\web\ErrorHandler as BaseErrorHandle;

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
        
        return [
            'code' => $value['code'],
            'message' => $value['message']
        ];

    }
}
```
上面的代码块重写了错误渲染结果，只返回了错误码和信息；处理之后的结果如下：
```xml
<response>
<code>401</code>
<message>Access Token 无效</message>
</response>
```
## 三、编写业务代码使用的错误基类
编写这一块主要是为了给业务使用，方便根据错误码来返回对应信息。
*Exception.php*为错误基类，这个类继承了*Yii*的*HttpException*，该类可以对网络状态码进行设置，不用再进行其他响应操作。
```php
<?php

namespace app\modules\api\exception;

use yii\web\HttpException;

class Exception extends HttpException
{

    /**
     * @var array $error_code_list 错误码列表
     */
    protected $error_code_list;

    /**
     * 错误状态返回
     * BaseException constructor.
     * @param int $code 状态码
     * @param null $message 错误信息
     */
    public function __construct( $code = 500 , $message = null )
    {

        if( array_key_exists($code, $this->error_code_list) ){
            $message = $this->error_code_list[$code];
        }

        parent::__construct(200, $message, $code);

    }

    /**
     * 根据错误码获取错误信息
     * @param $code integer
     * @return mixed
     */
    public function getErrorMessage( $code )
    {
        return $this->error_code_list[$code];
    }

}
```
*BaseException.php*这个衍生的子类可以给控制器或者模型使用，在业务代码内调用。
```php
<?php

namespace app\modules\api\exception;


class BaseException extends Exception
{

    public function __construct($code = 500 , $message = null)
    {
        $this->error_code_list = ErrorCodes::errorCodeList();
        parent::__construct( $code, $message );
    }

}
```
## 四、在业务逻辑内调用
这里编写了一个简单的demo，模拟控制器检查到token出错时抛出。
```php
<?php

namespace app\modules\api\controllers;

use yii\rest\Controller;
use app\modules\api\exception\BaseException;

class IndexController extends Controller{

    public function actionIndex(){
        throw new BaseException(401);
    }

}
```
## 五、在模块内注册错误控制类
在模块目录下的*Module.php*文件的*init*方法中，加入我们自定义的错误控制类
```php
<?php
namespace app\modules\api;

use app\modules\api\exception\ErrorHandler;


class Module extends \yii\base\Module
{
    /**
     * @inheritdoc
     */
    public $controllerNamespace = 'app\modules\api\controllers';


    /**
     * @inheritdoc
     */
    public function init()
    {

        parent::init();

        // custom initialization code goes here

        // 自定义拦截错误时间拦截注册
        $error_handler = new ErrorHandler();
        $error_handler->register();


    }
}

```