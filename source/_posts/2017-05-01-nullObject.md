---
title:  "PHP设计模式-空对象"
date: 2017-05-01  12:00:00
updated: 2017-05-01  12:00:00
comments: true
categories: PHP
tags:
    - PHP
    - 设计模式
---

## 适用性

 * 当程序运行过程中出现操作空对象时，程序依然能够通过操作提供的空对象继续执行
 * 下面实现老师课堂叫学生回答问题
     <!-- more -->
## 代码示例

```php
/**
 * 抽象类人
 */
abstract class Person
{
    /**
     * 名字
     * @var string
     */
    private $_name = '';

    /**
     * 构造函数
     */
    function __construct($name)
    {
        $this->_name = $name;
    }

    /**
     * 魔术方法
     * 读取私有属性
     *
     * @param  string $name 属性名称
     * @return mixed
     */
    function __get($name='')
    {
        $name = '_' . $name;
        return $this->$name;
    }

    /**
     * 抽象方法
     *
     * @return mixed
     */
    abstract function doSomthing($person);
}

/**
 * 老师
 */
class Teacher extends Person
{
    /**
     * 老师提问
     *
     * @return mixed
     */
    function doSomthing($person)
    {
        if (!$person instanceof Student) {
            $person = new NullPerson('');
        }
        $person->doSomthing($this);
    }
}

/**
 * 学生
 */
class Student extends Person
{
    /**
     * 答题方法
     *
     * @return mixed
     */
    function doSomthing($person)
    {
        echo "老师‘{$person->name}’让学生‘{$this->name}’回答了一道题~ \n";
    }
}

/**
 * 鬼
 */
class NullPerson extends Person
{
    /**
     * 空方法
     *
     * @return mixed
     */
    function doSomthing($person)
    {
        echo "难道这是个鬼吗............ \n";
    }
}


try {
    //创建一个老师：路飞
    $teacher = new Teacher('路飞');

    // 创建学生
    $mc      = new Student('麦迪');
    $kobe    = new Student('科比');
    $paul    = new Student('保罗');

    // 老师提问
    $teacher->doSomthing($mc);
    $teacher->doSomthing($kobe);
    $teacher->doSomthing($paul);
    $teacher->doSomthing('小李');// 提问了一个班级里不存在人名


} catch (\Exception $e) {
    echo 'error:' . $e->getMessage();
}
```
