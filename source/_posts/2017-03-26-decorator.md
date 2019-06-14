---
title:  "PHP设计模式-装饰器"
date: 2017-03-26 12:00:00
updated: 2017-03-26 12:00:00
comments: true
categories: PHP
tags:
    - PHP
    - 设计模式
---
## 适用性

 * **对现有的对象增加功能**
 * 和适配器的区别：适配器是连接两个接口，装饰器是对现有的对象包装
<!-- more -->

## 代码示例

~~~ PHP

/**
 * 鞋接口
 */
interface ShoesInterface
{
    public function product();
}

/**
 * 装饰器抽象类.
 */
abstract class Decorator implements ShoesInterface
{
    /**
     * 产品生产线对象
     */
    protected $shoes;

    /**
     * 构造函数.
     */
    public function __construct(ShoesInterface $shoes)
    {
        $this->shoes = $shoes;
    }

    /**
     * 生产.
     */
    public function product()
    {
        $this->shoes->product();
    }

    /**
     * 装饰操作.
     */
    abstract public function decorate($value);
}

/**
 * 贴标装饰器
 */
class DecoratorBrand extends Decorator
{

    private $_brand;

    /**
     * 构造函数
     */
    public function __construct(ShoesInterface $phone)
    {
        $this->phone = $phone;
    }

    public function __set($name='', $value='')
    {
        $this->$name = $value;
    }

    /**
     * 生产
     */
    public function product()
    {
        $this->phone->product();
        $this->decorate($this->_brand);
    }

    /**
     * 贴标操作
     */
    public function decorate($value='')
    {
        echo "贴上{$value}标志 \n";
    }

}

/**
 * 滑板鞋实体
 */
class ShoesSkateboard implements ShoesInterface
{
    public function product()
    {
        echo "生产一滑板鞋";
    }
}

/**
 * 运动鞋实体
 */
class ShoesSport implements ShoesInterface
{
    public function product()
    {
        echo "生产一双球鞋";
    }
}


try {
    echo "未加装饰器之前：\n";
    // 生产运动鞋
    $shoesSport = new ShoesSport();
    $shoesSport->product();

    echo "\n--------------------\n";

    echo "加贴标装饰器：\n";
    // 初始化一个贴商标适配器
    $DecoratorBrand = new DecoratorBrand(new ShoesSport());
    $DecoratorBrand->_brand = 'nike';
    // 生产nike牌运动鞋
    $DecoratorBrand->product();
} catch (\Exception $e) {
    echo $e->getMessage();
}

~~~
