+++
title = "微信开发技巧之文件上传"
categories = ["PHP","WeChat"]
tags = ["PHP","WeChat"]
date = "2022-04-16T00:26:28+08:00"
draft = false

+++

>微信小程序直播提供了由服务端发起创建直播间的功能，然而其中包括直播间的封面图和分享海报，官方文档的[指引](https://developers.weixin.qq.com/doc/offiaccount/Asset_Management/New_temporary_materials.html)是通过临时素材的 media_id 来访问文件的，该接口请求方式为 http 的 POST/FORM ，即用 FORM 表单来上传一个多媒体文件，这里使用的是 php 的 Curl  请求工具类发起网络请求，用 CURLFile 封装表单的多媒体文件。

下面是我在项目开发时使用的代码片段，第一部分是发送网络请求的工具函数：

```php
public function httpUploadFile( $url,$data )
{
	$ch = curl_init();

    curl_setopt($ch, CURLOPT_URL, $url);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_POST, 1);
    curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
    curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 5);
    curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
    curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, false);
    curl_setopt(
        $ch,
        CURLOPT_POSTFIELDS,
        $data
    );
    $headers = array();
    $headers[] = 'Content-Type: multipart/form-data';
    curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);

    $output = curl_exec($ch);
    curl_close($ch);
    return $output;
}
```

上面的代码片段其实是和普通的 Curl 发起 POST 是无差别的；需要注意的是，发送前，需要设置请求头为 `'Content-Type: multipart/form-data'` 。

第二部分就是发送请求的 FORM 表单内容了：

```php
$data = [
	'media'=>new CURLFile(realpath($file)),
	'type' => 'image'
]
```

这里的 media 就是接口需要的字段，因为本人的开发环境是 php7.4 所以必须要使用 CURLFile 封装发送的文件，这里调用类传递的第一个参数是文件的真实路径，同时他还能指定文件的 MineType，还有文件上传的名称；

```php
array(4) {
  'type' =>
  string(5) "image"
  'media_id' =>
  string(64) "fOfJIy2-XlOS0HWDJcvEy1CAt4nRpphJUS2sIagHCcOnpGo8hN4dqHZ9ZlDzdlOg"
  'created_at' =>
  int(1649911169)
  'item' =>
  array(0) {
  }
}
```

上面就是请求成功后返回的结果集了，然后本地就可以使用 media_id 来获取到资源文件了。

