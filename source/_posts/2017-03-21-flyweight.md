---
title:  "PHP设计模式-享元"
date: 2017-03-21 12:00:00
updated: 2017-03-21 12:00:00
comments: true
categories: PHP
tags:
    - PHP
    - 设计模式
---

## 适用性

 * **php享元（轻量级）模式**
 * 就是缓存了创建型模式创建的对象，不知道为什么会归在结构型模式中，个人觉得创建型模式更合适，哈哈～
 * 其次，享元强调的缓存对象，外观模式强调的对外保持简单易用，是不是就大体构成了目前牛逼哄哄且满大的街【依赖注入容器】
 * 下面我们借助最简单的’工厂模式‘来实现享元模式，就是给工厂加了个缓存池

<!-- more -->

## UML

<img src="/images/DesignPatterns/flyweight.png">

## 代码示例

~~~ PHP

/**
 * 农场
 *
 * 生产动物
 */
class Farm
{
  /**
   * 对象缓存池
   * @var array
   */
  private $_farmMap = [];

  /**
   * 构造函数
   */
  public function __construct()
  {
    echo "-----------初始化了一个农场----------- \n\n";
  }

  /**
   * 生产方法
   *
   * 生产农物
   *
   * @param  string $type 农场类型
   * @return mixed
   */
  public function produce($type='')
  {
    // 对象缓存池判断
    if (key_exists($type, $this->_farmMap)) {
      echo "来自缓存池-> ";
      return $this->_farmMap[$type];// 返回缓存
    }

    switch ($type) {
      case 'chicken':
        return $this->_farmMap[$type] =  new Chicken();
        break;

      case 'pig':
        return $this->_farmMap[$type] =  new Pig();
        break;

      default:
        echo "该农场不支持生产该农物~ \n";
        break;
    }
  }
}

/**
 * 动物接口
 */
interface AnimalInterface
{
  /**
   * 类型获取
   *
   * @return string
   */
  public function getType();
}


/**
 * 实体鸡
 *
 */
class Chicken implements AnimalInterface
{
  /**
   * 类别
   * @var string
   */
  private $_type = '';

  /**
   * 构造函数
   */
  public function __construct()
  {

  }

  /**
   * 类型获取
   *
   * @return string
   */
  public function getType()
  {
    echo "这是只鸡～ \n";
  }
}


/**
 * 实体猪
 *
 */
class Pig implements AnimalInterface
{
  /**
   * 类别
   * @var string
   */
  private $_type = '';

  /**
   * 构造函数
   */
  public function __construct()
  {

  }

  /**
   * 类型获取
   *
   * @return string
   */
  public function getType()
  {
    echo "这是只猪～ \n";
  }
}


// 初始化一个工厂
$farm = new Farm();
//-----------初始化了一个农场----------- 

// 成产一只鸡
$farm->produce('chicken')->getType();
//这是只鸡～

// 再生产一只鸡
$farm->produce('chicken')->getType();
//来自缓存池-> 这是只鸡～

// 成产一只猪
$farm->produce('pig')->getType();
//这是只猪～

// 再生产一只猪
$farm->produce('pig')->getType();
//来自缓存池-> 这是只猪～


~~~
