---
title:  "PHP设计模式-单例"
date: 2016-12-10 12:00:00
updated: 2016-12-10 12:00:00
comments: true
categories: PHP
tags:
    - PHP
    - 设计模式
---

> "two day~ ”

## 适用性

* **当类只能有一个实例而且客户可以从一个众所周知的访问点访问它时。**
* **当这个唯一实例应该是通过子类化可扩展的，并且客户应该无需更改代码就能使用一个扩展的实例时。**

<!-- more -->

## UML

<img src="/images/DesignPatterns/Singleton.png">

## 代码示例

CD wed站点出售CD。在访问者购买后要及时更新库存。为了实现这个功能。需要链接MySQL 数据库以及更新CD数量。使用变相对象的方式。可能要创造多个不必要的数据库链接。如下所示，完全可以选择单例模式的数据库链接：
```php
    class InventoryConnection
    {
        protected static $_instance = null;
        
        protected $_handle = null;

        public static function getInstance()
        {
            if(!self::$_instance instanceof self)
            {
                self::$_instance = new self;
            }
            return self::$_instance;
        }

        protected function __construct()
        {
            $this->_handle = mysql_connect('localhose','user','pass');
            mysql_select_db('cd',$this->_handle);
        }

        public function updateQuantity($band,$title,$number)
        {
            $query = "update CDS set amount=amount+".intval($number);
            $query .= " where band =' ".mysql_real_escape_string($band)." ' ";
            $query .= " and title =' ".mysql_real_escape_string($title)." ' " ;

            mysql_query($query,$this->_handle);
        }
    }
```
InventoryConnection 类的第一个公共方法 getInstance()的静态方法。这个方法检查$_instance 是否有实例。接下来就是购买CD.
```php
    class CD
    {
        protected $_title = '';
        protected $_band = '';

        public function __construct($title,$band)
        {
            $this->_title = $title;
            $this->_band = $band;
        }

        public function buy()
        {
            $inventory = InventoryConnection::getInstance();
            $inventory->updateQuantity($this->_band, $this->_title,-1);
        }        
    }
```
通过调用InventoryConnection 的getInstance() 方法来获得这个类的实例。一旦接收实例，就会调用通过调用InventoryConnection 对象updateQuantity()方法指定CD数量减1。
```php
    $boughtCDs = array();
    $boughtCDs[] = array('band'=>'Never Again','Waste of a Rib');
    $boughtCDs[] = array('banc'=>'Therappee','Long Road');

    foreach ($boughtCDs as $boughtCD)
    {
        $cd = new CD($boughtCD['title'],$boughtCD['banc']);
        $cd->buy();
    }
```