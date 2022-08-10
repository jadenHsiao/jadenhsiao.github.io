+++
title = "Yii2使用技巧（一）- 数组辅助工具"
categories = ["PHP","Yii"]
tags = ["PHP","Yii"]
date = "2022-03-16T23:36:28+08:00"
draft = false

+++

> 因为公司主线业务使用的是 Yii2，所以该系列文章主要是平时工作中使用 Yii2 框架的技巧总结，希望可以让开发者更多地使用 Yii2 框架提供的特性，编写出更加规范化和可读性更高的业务代码；Yii2 官方提供的数组工具非常实用，这里根据使用经验展开说明。

## 一、数组筛选（\yii\helper\ArrayHelper::filter）

该函数在 Yii2.0 的权威指南中数组助手类并没有看到，而是在 API 文档中查阅到的，主要功能是根据指定的规则筛选数组，但是这个函数有使用条件，就是只能在两维或以下的数组中使用。

![API 文档说明](/images/Snipaste_2022-03-16_23-51-11.png)

从上图可以看到，该函数有两个参数，第一个参数是待处理的数组，第二个参数是筛选的规则（格式也是数组）；其中规则支持，筛选（排除）出一维或二维指定字段；下面就由一个简单的示例来说明该函数的作用。

```php
public function actionExample()
{
	$example = [
            'name' => 'jack',
            'sex' => 'man',
            'age' => 17,
            'course' => [
                'one' => 'english',
                'two' => 'chinese',
                'three' => 'math'
            ]
        ];
        $result = \yii\helpers\ArrayHelper::filter(
            $example,
            ['!name','sex','course.one','!course.three']
        );
        var_dump($result);

 }
```

上面这个 demo 里，使用该筛选函数，排除了一维的 name ，显示了一维的 sex ，显示二维的 course 中的 one ，排除了二维的 course 中的 three，最后的结果为下图。

![运行结果](/images/Snipaste_2022-03-17_00-02-32.png)

## 二、数组检索列（\yii\helper\ArrayHelper::getColumn）

其实这个函数的话在官方文档内也说明的挺清楚的，[点击前往](https://www.yiiframework.com/doc/guide/2.0/zh-cn/helper-array#retrieving-columns)，但是在这里的话，我是对文档内指定一个匿名函数的补充，因为官方文档内示例是只检索出单独一个值，而这里的话是可以返回出一个新的数组结构。

```php
public function actionExample()
{

    $example = [
        ['name' => 'jack', 'sex' => 'man', 'age' => 17],
        ['name' => 'james', 'sex' => 'man', 'age' => 19],
        ['name' => 'jones', 'sex' => 'man', 'age' => 16]
    ];
    $result = \yii\helpers\ArrayHelper::getColumn(
        $example,
        function($element){
        	return ['student'=>$element['name'],'gender'=>$element['sex']];
        }
    );
    var_dump($result);

}
```

上面这个 demo 里，使用了检索列函数，同时用匿名函数来为数组的每一项返回新的结构，使用 student 来显示原先的 name，gender 显示原先的 sex，最后结果为下图。

![运行结果](/images/Snipaste_2022-03-17_00-33-40.png)

## 二、对象转化为数组（\yii\helper\ArrayHelper::toArray）
有时候我们通过*Active Record*查询出的数据并不满足前端需求，或者某些字段需要其他字段进行聚合运算；可能好多开发者的第一反应是对结果集转化为数组后进行遍历操作，这样一方面代码不够整洁，另一方面进行遍历操作如果存在其他表查询就会带来N+1的问题，所以使用*toArray*这个方法来处理我们的需求。

```php
<?php
use yii\helpers\ArrayHelper;
use app\models\Example;

//其他伪代码....
$data = ArrayHelper::toArray(
            $list, 
            [
                Example::className() => [
                    'id',
                    'name',
                    'article_top',
                    'article',
                    'question_list' => function ($item){
                        return $item->getQuestion()->all();
                    }
                ]
            ]
    );

```
上面的伪代码中，``$list``为模型查询出来的数据（对象）；转化为数组时需要先指定目标的模型，指定模型后，如果需要返回原先模型的字段，则直接对应名称即可；如果需要定义返回内容的话，则需要编写对应的返回名称和执行计算的闭包函数，闭包函数的参数是查询的*Active Record*对象，基于它可以进行模型关联等其他操作。