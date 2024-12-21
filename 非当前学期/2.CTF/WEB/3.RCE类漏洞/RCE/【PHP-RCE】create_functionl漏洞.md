我们的 `create_function` 函数的功能和对应的语法定义如下所示：
```
$f=create_function(参数，函数实现);
$f(参数)
```

其中我们的参数和我们的函数实现都是通过我们的对应的**字符串传递事先的**，我们下面是一个具体的实现
```php
<?php  
$newfunc = create_function('$a,$b', 'return "ln($a) + ln($b) = " . log($a * $b);');  
echo $newfunc(2, M_E) . "\n";  
?>
```

## 漏洞：
我们注意到，我们的 create_function 是和我们的 eval 函数相关的，于是，我们看见如下所示的题目是，就可以考虑使用我们额 create_function:
```
$a('code',$b);
```

其中，我们只需要构造我们的 `$a=create_function`, `$b=?>phpinfo();?>//`。就可以实现我们的 `PHP-RCE`，之后的内容就很简单了。

