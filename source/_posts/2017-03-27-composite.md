---
title:  "PHP设计模式-组合"
date: 2017-03-27 12:00:00
updated: 2017-03-27 12:00:00
comments: true
categories: PHP
tags:
    - PHP
    - 设计模式
---
## 适用性

 * 定义：将对象以树形结构组织起来，以达成“部分－整体”的层次结构，使得客户端对单个对象和组合对象的使用具有一致性
 * 我的理解：把对象构建成树形结构
<!-- more -->

## 代码示例

~~~ PHP


/**
 * 根节点和树节点都要实现的接口
 */
interface CompositeInterface
{
    /**
     * 增加一个节点对象
     *
     * @return mixed
     */
    public function add(CompositeInterface $composite);

    /**
     * 删除节点一个对象
     *
     * @return mixed
     */
    public function delete(CompositeInterface $composite);

    /**
     * 实体类要实现的方法
     *
     * @return mixed
     */
    public function operation();

    /**
     * 打印对象组合
     *
     * @return mixed
     */
    public function printComposite();
}


/**
 * 文件实体.
 */
class File implements CompositeInterface
{
    /**
     * 文件名称.
     *
     * @var string
     */
    private $_name = '';

    /**
     * 文件内容.
     *
     * @var string
     */
    private $_content = '';

    /**
     * 构造函数.
     *
     * @param string $name
     */
    public function __construct($name = '')
    {
        $this->_name = $name;
    }

    /**
     * 魔法函数
     * @param  string $name  属性名称
     * @return mixed
     */
    public function __get($name='')
    {
        $name = '_' . $name;
        return $this->$name;
    }

    /**
     * 增加一个节点对象
     *
     * @return mixed
     */
    public function add(CompositeInterface $composite)
    {
        throw new Exception('not support', 500);
    }

    /**
     * 删除节点一个对象
     *
     * @return mixed
     */
    public function delete(CompositeInterface $composite)
    {
        throw new Exception('not support', 500);
    }

    /**
     * 打印对象组合.
     *
     * @return mixed
     */
    public function printComposite()
    {
        throw new Exception('not support', 500);
    }

    /**
     * 实体类要实现的方法.
     *
     * @return mixed
     */
    public function operation($operation = '', $content = '')
    {
        switch ($operation) {
            case 'write':
                $this->_content .= $content;
                echo 'write success';
                break;
            case 'read':
                echo $this->_content;
                break;

            default:
                throw new \Exception("not support", 400);
                break;
        }
    }
}

/**
 * 文件夹实体
 */
class Folder implements CompositeInterface
{
    /**
     * 对象组合
     * @var array
     */
    private $_composite = [];

    /**
     * 文件夹名称
     * @var string
     */
    private $_name = '';

    /**
     * 构造函数
     *
     * @param string $name
     */
    public function __construct($name='')
    {
        $this->_name = $name;
    }

    /**
     * 魔法函数
     * @param  string $name  属性名称
     * @return mixed
     */
    public function __get($name='')
    {
        $name = '_' . $name;
        return $this->$name;
    }

    /**
     * 增加一个节点对象
     *
     * @return void
     */
    public function add(CompositeInterface $composite)
    {
        if (in_array($composite, $this->_composite, true)) {
            return;
        }
        $this->_composite[] = $composite;
    }

    /**
     * 删除节点一个对象
     *
     * @return void
     */
    public function delete(CompositeInterface $composite)
    {
        $key = array_search($composite, $this->_composite, true);
        if (!$key) {
            throw new Exception("not found", 404);
        }
        unset($this->_composite[$key]);
        $this->_composite = array_values($this->_composite);
    }

    /**
     * 打印对象组合
     *
     * @return void
     */
    public function printComposite()
    {
        foreach ($this->_composite as $v) {
            if ($v instanceof Folder) {
                echo '---' . $v->name . "---\n";
                $v->printComposite();
                continue;
            }
            echo $v->name . "\n";
        }
    }

    /**
     * 实体类要实现的方法
     *
     * @return mixed
     */
    public function operation()
    {
        return;
    }
}



try {
    // 构建一个根目录
    $root = new Folder('根目录');

    // 根目录下添加一个test.php的文件和usr,mnt的文件夹
    $testFile = new File('test.php');
    $usr = new Folder('usr');
    $mnt = new Folder('mnt');
    $root->add($testFile);
    $root->add($usr);
    $root->add($mnt);
    $usr->add($testFile);// usr目录下加一个test.php的文件

    // 打印根目录文件夹节点
    $root->printComposite();

} catch (\Exception $e) {
    echo $e->getMessage();
}


~~~
