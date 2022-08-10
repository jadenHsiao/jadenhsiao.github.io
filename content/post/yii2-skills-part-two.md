+++
title = "Yii2使用技巧（二）- 在Yii上使用MongoDB"
categories = ["PHP","Yii"]
tags = ["PHP","Yii"]
date = "2022-03-21T00:26:28+08:00"
draft = false

+++

> 公司项目的主线业务使用的是 MySQL ，但是在日志系统使用的是非关系型数据库 MongoDB ，而 Yii2 本身的 Active Record 是支持的，所以使用技巧二将针对 Yii2 上 MongoDB 的使用方法做一系列讲解。

## 一、下载安装 ```Yii2-mongodb``` 拓展包
根据拓展的简介，这个拓展包的功能就是作为 Yii2 和 mongoDB 的粘合剂，因此这个拓展包是必要的；在 Yii2 项目目录下，执行 ```composer require yiisoft/yii2-mongodb``` ；或者在项目的“ composer.json ”文件中“ require ”项内加入 ```"yiisoft/yii2-mongodb": "~2.1.0"``` ，再执行 ```composer update``` ，将拓展包安装包项目内。

## 二、将组件添加到 ```Yii``` 配置

因为本人使用的是 basic 版，所以就直接在项目的 config/web.php 文件的 components 关联数组内添加一下代码：

```php
'mongodb' => [
            'class' => 'yii\mongodb\Connection',
            'dsn' => 'mongodb://localhost:27017/'.env("MONGODB_DATABASE"),
            'options' => [
                "username" => env("MONGODB_USERNAME"),
                "password" => env("MONGODB_PASSWORD")
            ]
        ]
```

上面的代码是添加一个名为```mongodb```的配置项，其中包括调用的类，链接地址，还有登录数据库的配置，其中相关的配置都是使用```.env``` 文件进行动态配置；完成上诉配置之后，在项目里就可以使用```Yii::$app->mongodb```来调用拓展提供的方法了。

## 三、加入到代码生成器（Gii）

加入到```Gii```后就可以使用其创建基于```Active Record```的模型类，更加方便于我们对集合进行增删查改的操作，接着第二步的同一个配置文件，在```return $config```前加入如下配置：

```php
$config['bootstrap'][] = 'gii';
$config['modules']['gii'] = [
    'class' => 'yii\gii\Module',
    // uncomment the following to add your IP if you are not connecting from localhost.
    'allowedIPs' => ['127.0.0.1', '::1'],
    'generators'=>[
        'mongoDbModel' => [
            'class' => '\yii\mongodb\gii\model\Generator'
        ],
    ]
];
```

在配置的 bootstrap 内新增一个 gii，同时在 modules 内的 gii 指定为 gii 的模块，具体的类为```yii\gii\Module```；

还有 allowedIPs ，是为了安全起见对访问的 IP 地址进行限制，只有被添加到该项的 IP 才能访问；

最后在生成器 generators 内，指定一个名为 mongoDbModel 的类为 ```\yii\mongodb\gii\model\Generator``` ；

完成以上操作后，在本地打开 gii，就可以发现已经多了一项可以生成 MongoDB 模型的生成器了。

![Gii MongoDB模型生成器](/images/Snipaste_2022-03-22_01-15-47.png)

## 四、使用生成器生成集合模型

在完成第三步的情况下，点击 start ，进入到生成器界面，如图所示。

![模型生成器选项](/images/Snipaste_2022-03-22_01-51-24.png)

* ```Collection Name``` 这里填写的是集合的名称

* ```Database Name``` 这里填写的是数据库的名称

* ```Attribute List``` 这里填写文档的字段，多个字段使用逗号隔开（记得是半角下状态的），然后```_id``` 是不用手动添加的，生成器会自动补充的

* ```Model Class``` 这里填写的是模型的类名

* ```Namespace``` 为类的命名空间，这里根据你项目安排

  完成上述内容填写后，点击```Preview```，再点击```Generate```，就可以在对应的目录下生成模型文件了。


## 五、组件部分使用方法

在集合没有创建``` Active Record```模型的情况下，可以使用第二步注入的组件来对集合进行一系列的增删改查操作，下列的方法都是基于```Yii::$app->mongodb```的，并且每次都需要指定到目标集合。

==以下的操作都是需要先指定数据库，再指定集合！==

###   1. 插入一条数据
使用insert方法，传入一个关联数组，数组的键为集合的字段，返回的是一个BSON对象，是_id；
代码块：
```php
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('test')  
  ->insert([  
  'name' => 'mike',  
  'sex' => 'man',  
  'hobby' => json_encode(['sport'=>'basketball','music'=>'jazz'])  
 ]);  
var_dump($example);
```
结果：
```php
MongoDB\BSON\ObjectId#1
(
    [oid] => '6291d18c4c1b00008d0033b1'
)
```
###   2. 插入多条数据
使用batchInsert方法，传入一个二维数组，第二维的每一项和插入一条数据的结构是一样的，返回的是一个带
_id的数组；
代码块
```php
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('test')  
  ->batchInsert([  
	  [ 
		  'name' => 'smith',  
		  'sex' => 'man',  
		  'hobby' => json_encode(['sport'=>'football','music'=>'pop'])  
	  ],  
	  [  
		  'name' => 'james',  
		  'sex' => 'women',  
		  'hobby' => json_encode(['sport'=>'basketball','music'=>'jazz'])  
	 ] 
 ]);
 var_dump($result);
```
结果：
```php
[
    0 => [
        'name' => 'smith'
        'sex' => 'man'
        'hobby' => '{\"sport\":\"football\",\"music\":\"pop\"}'
        '_id' => MongoDB\BSON\ObjectId#1
        (
            [oid] => '62a2cc104e2f0000e70020a1'
        )
    ]
    1 => [
        'name' => 'james'
        'sex' => 'women'
        'hobby' => '{\"sport\":\"basketball\",\"music\":\"jazz\"}'
        '_id' => MongoDB\BSON\ObjectId#2
        (
            [oid] => '62a2cc104e2f0000e70020a2'
        )
    ]
]
```
###   3. 删除一条数据
使用remove方法，传入一个关联数组（键值对的形式），数组的内容是删除的条件，返回被删除的记录条数
代码块：
```php
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('test')  
  ->remove([  
  'name' => 'smith',  
  'sex' => 'man',  
  ]);  
var_dump($example);
```
### 4.获取当前操作的数据库和集合
使用getFullName方法，获取到的是“数据库.集合”这种格式的字符串。
代码块：
```php
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('test')  
  ->getFullName();  
var_dump($example); // 'test.test'
```
### 5.删除集合的所有索引
使用dropAllIndexes方法，返回删除的索引总数。
代码块：
```php
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('test')  
  ->dropAllIndexes();
var_dump($example); // 1  
```
### 6.检查某个条件的文档是否存在
使用documentExists方法，传入条件类型为关联数组，返回布尔值。
代码块：
```php
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('test')  
  ->documentExists(['name'=>'mike']);  
var_dump($example); // true or false
```
### 7.获取某个条件下文档的总数
使用count方法，传入条件类型为关联数组，返回整型。
代码块：
```php
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('test')  
  ->count(['name'=>'mike']);  
var_dump($example); // 1
```
### 8.获取指定字段不重复的值
使用distinct，第一个参数为指定的字段（类型为字符串），第二个参数为筛选的条件（类型为数组），如果存在返回数据（类型为数组），没有的话为false。
```php
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('test')  
  ->distinct("name",["sex"=>"M","age" => 21]);
dd($example);  
```
![Navicat查询结果](/images/Snipaste_2022-07-15_15-09-26.jpg)
![函数查询结果](/images/Snipaste_2022-07-15_15-09-36.jpg)
### 9.使用聚合函数（aggregate）进行复杂查询
>MongoDB 中聚合(aggregate)主要用于处理数据(诸如统计平均值，求和等)，并返回计算后的数据结果。

为了后面筛选数据结果比对明显，所以先添加测试数据，指令如下：
```shell
db.user.drop();
for(var i=10; i< 100; i++) {
  db.user.insert({
    name:"user" + i, 
    age : Math.floor(Math.random()*10)+ 20, 
    sex : Math.floor(Math.random()*3)%2 ==0 ? 'M' : 'F',
    chinese : Math.floor(Math.random()*50)+50,
    math : Math.floor(Math.random()*50)+50,
    english : Math.floor(Math.random()*50)+50,
    class : "C" + i%5
  })
}
```
该指令添加了100条数据，数据内容包括用户名，年龄，性别，语文、数学、英语成绩还有所在班级；
![数据内容](/images/Snipaste_2022-07-15_15-09-26.jpg)

aggregate，该方法需要传递一个二维的索引数组，第二维为关联数组，数组的键为mongoDB表达式，值为条件内容。
#### 9.1 用聚合来筛选性别为“M”的文档
这里需要使用到聚合表达式的`$match`，其功能如同MySQL的where，代码如下：
```php
$aggregate = [
	['$match' => ['sex'=>['$regex'=>"M"]]]
];
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('user')  
  ->aggregate($aggregate);
var_dump($example); 
```
得到的结果如图所示：
![数据内容](/images/Snipaste_2022-07-15_17-48-52.jpg)
#### 9.2 根据班级、性别进行分组，同时获取该分组下的人数
分组需要使用到聚合表达式的`$group`，其功能如同MySQL的group，作为分组依据的同时也作为筛选字段；统计人数则是表达式`$sum`，而`'$sum'=>1`是如果满足条件的话该值就加一，代码如下：
```php
$aggregate = [
	[  
	  '$group' => [  
		  '_id'=>["class"=>'$class',"sex" => '$sex'],
		  "count"=>['$sum'=>1]
	 ]
 ],
];
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('user')  
  ->aggregate($aggregate);
var_dump($example); 
```
得到的结果如图所示：
![数据内容](/images/Snipaste_2022-07-15_17-55-30.jpg)
#### 9.3 在9.2的条件基础上，获取到该分组下语文分数的最高分、最低分和平均分
这里需要使用到三个聚合表达式，分别为：最大值（`$max`），最小值（`$min`），平均值（`$avg`），代码如下：
```php
$aggregate = [
	[  
	  '$group' => [  
		  '_id'=>["class"=>'$class',"sex" => '$sex'],
		  "count"=>['$sum'=>1],
		  "chinese_max" => ['$min' => '$chinese'],  
		  "chinese_min" => ['$max' => '$chinese'],  
		  "chinese_avg" => ['$avg' => '$chinese']
	 ]
 ],
];
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('user')  
  ->aggregate($aggregate);
var_dump($example); 
```
得到的结果如图所示：
![数据内容](/images/Snipaste_2022-07-15_18-07-32.jpg)
#### 9.4 在9.3的基础上根据语文分数最大值由高到低排序
这里需要使用到的表达式为`$sort`，当值为1时为升序，-1为降序，代码如下：
```php
$aggregate = [
	[  
	  '$group' => [  
		  '_id'=>["class"=>'$class',"sex" => '$sex'],
		  "count"=>['$sum'=>1],
		  "chinese_max" => ['$min' => '$chinese'],  
		  "chinese_min" => ['$max' => '$chinese'],  
		  "chinese_avg" => ['$avg' => '$chinese']
	 ],
	 [  
		  '$sort' => ['chinese_max'=>-1]  
	 ],
 ],
];
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('user')  
  ->aggregate($aggregate);
var_dump($example); 
```
得到的结果如图所示：
![数据内容](/images/Snipaste_2022-07-15_18-13-30.jpg)
#### 9.5 在9.4的基础上分页
这里需要使用的聚合表达式为`$limit`和`$skip`，`$skip`为跳过指定条数后显示之后的内容，而`$limit`则是显示多少条 ，因为进行链式操作，所以`$skip`和`$limit`的顺序不能出错，代码如下：
```php
$page = \Yii::$app->request->get("page",1);  
$limit = \Yii::$app->request->get("limit",3);
$aggregate = [
	[  
	  '$group' => [  
		  '_id'=>["class"=>'$class',"sex" => '$sex'],
		  "count"=>['$sum'=>1],
		  "chinese_max" => ['$min' => '$chinese'],  
		  "chinese_min" => ['$max' => '$chinese'],  
		  "chinese_avg" => ['$avg' => '$chinese']
	 ],
	 [  
		  '$sort' => ['chinese_max'=>-1]  
	 ],
	 [  
		  '$skip' => --$page * $limit
	 ],
	 [  
		  '$limit' => $limit
	 ],
 ],
];
$example = \Yii::$app->mongodb  
  ->getDatabase('test')  
  ->getCollection('user')  
  ->aggregate($aggregate);
var_dump($example); 
```
得到的结果如图所示：
![数据内容](/images/Snipaste_2022-07-15_18-38-05.jpg)
