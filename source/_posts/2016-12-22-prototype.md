---
title:  "PHP设计模式-原型"
date: 2016-12-22 12:00:00
updated: 2016-22-10 12:00:00
comments: true
categories: PHP
tags:
    - PHP
    - 设计模式
---

## 适用性

**原型设计模式创建对象的方式是复制和克隆初始对象或原型，这种方式比创建新实例更为有效。**

<!-- more -->

## UML

<img src="/img/in-post/post-design-patterns/Prototype.png">

* MyOject 类组合使用了原型设计模式。这个类具有名为 requestClone() 的公共方法，该方法用于生产 MyObject 实例的一个副本。
* CloneObject 对象表示 MyObject 的复制实例。需要注意的是，因为该对象实际是一个复制品，所有它具有 requestClone()方法。
* 通过调用MyObject 的 requestClone() 方法，可以创建 CloneObject 的许多实例。

## 代码示例

音乐销售 Wed 站点允许登录站点的艺术家创建许多乐队的音乐“合辑”。目前，这种功能性还限制为所有有效的音轨只能来自一个乐队。为了开始创建CD“合辑”，我们可以采用多种途径。其中，在访问者查看某乐队的CD 页面的时候可以使用最通用的选项。CD页面存在的某个链接能够启动构建新CD“合辑”的进程，该进程会发送一个ID，这个ID对应于乐队的特定CD。<br/>
上面这个进程的第一个构建代码块是CD类。通类，为了构建CD对象，需要从数据库中检索出与被请求ID匹配的具体信息：

~~~ php

class CD
{
    public $band = '';
    public $tranckList = array();
    public function _construct($id)
    {
        $handle = mysql_connect('localhost','user','pass');
        mysql_select_db('cd',$handle);

        $query = "select band , title  from CDs where id={$id}";

        $resulte = mysql_query($query , $handle);

        if ($row = mysql_fetch_array($resulte)) {
            $this->band = $row['band'];
            $this->title = $row['title'];
        }
    }

    public function buy()
    {
        //cd buying magic here
        echo '<pre>';
        var_dump($this);
    }
}

~~~ 

这个类具有标砖的公共属性 $band $title $trackList <br/>
构造函数接受 $id 参数 ID ，并且针对数据库执行查询。当发现指定ID时，相对应乐队和标题会分别指派给公共属性 $band 和 $title .<br/>
此外，CD类还增加了一个 buy() 函数。在最后的代码中，该方法可以处理CD 对象提供用于购买。<br/>
下一个需要创建的类代表混合CD 实体。这种特点的对象利用了PHP 的克隆能力:

~~~ php

class MixtapeCD extends CD
{
    public function __clone()
    {
        $this->title = 'Mixtape';
    }
}

~~~

MixtapeCD 实际只是一种特殊化CD ， 扩展了CD 对象。<br/>
执行PHP 的 clone 命令是，就会对指定的对象执行 _clone() 方法。在MixtapeCD 对象中，初始CD的属性被重写。这个 MixtapeCD 对象不再对应于一个乐队和标题的CD 组合。此时，该对象关联 $band , 但具有新的标题 Mixtape 。<br/>
下面展示了某用户基于制定乐队制定两个混合标题的情况:

~~~ php

$externalPurchaseInfoBandID = 12;
$bandMixProto = new MixtapeCD($externalPurchaseInfoBandID);

$externalPurchaseInfo = array();
$externalPurchaseInfo[] = array('brrr' , 'goodbye');
$externalPurchaseInfo[] = array('what it means','brrr');

foreach ($externalPurchaseInfo as $mixed) {
    $cd = clone $bandMixProto;
    $cd->trackList = $mixed;
    $cd->buy();
}

~~~

$bandMixProto 对象根据 MixtapeCD 的新实例创建的。
传递该对象的参数 $extemalPuchaseInfoBandID 被用于实际 CD类构造函数执行的查询。<br/>
一旦创建了原型，就能够循环遍历用于特定访问者的CD合辑的音轨列表。对于 foreach() 循环的每个实例来说， $cd 都指派给 $bandMixProto 新副本。接下来特定曲目列表会被添加指定的对象。因为使用了克隆技术，所以每个新的循环都不需要针对数据库新查询。克隆对象已经存储了所有信息，最后，通过执行方法 buy() 就可以购买制定的 $cd 对象。








