---
title:  "PHP数组和对象相互转换"
date: 2017-06-19  12:00:00
updated: 2017-06-19  12:00:00
comments: true
categories: PHP
tags:
    - PHP
---
> 最近面试了很多PHP程序员，我出了这道题目，没几个做出来的。我很好奇，这也是项目中也经常能遇到的一个问题。所以顺便就记录一下。
 <!-- more -->
## 题目
`array('foo' => 'bar','one' => 'two','three' => 'four')`
把这个数组转成对象，然后在把对象转成数组。

## 答案
```php
/**
 * 对象转数组
 * @param unknown $obj 对象
 */
function objectToArray($obj){
    $arr = is_object($obj) ? get_object_vars($obj) : $obj;
    if(is_array($arr)){
        return array_map(__FUNCTION__, $arr);
    }else{
        return $arr;
    }
}

/**
 * 数组转对象
 * @param unknown $arr
 */
function arrayToObject($arr){
    if(is_array($arr)){
        return (object) array_map(__FUNCTION__, $arr);
    }else{
        return $arr;
    }
}
```
## 扩展
`get_object_var($object)` 返回一个数组。获取`$object`对象中的属性，组成一个数组。
```php
class person{
 public $name="王小明";
 public $age = 18;
 public $birth = 19910506;
}
$p = new person();
print_r(get_object_vars($p));
# 输出：
Array ( [name] => 王美人 [age] => 25 [birth] => 19910506 ) 

```

