## FILE 协议（读文件，全局通用）
### 使用条件：
- `allow_url_fopen`: off/on
- `allow_url_include` : off/on
### 作用：
用于访问本地文件系统，在 CTF 中通常用来**读取本地文件**的且不受 `allow_url_fopen` 与 `allow_url_include` 的影响。 

`include()/require()/include_once()/require_once()` 参数可控的情况下，如导入为非 `.php` 文件，则仍按照 php 语法进行解析，这是 `include()` 函数所决定的。

### 使用语法：
```
file://[文件的绝对路径和文件名]
```

```
file=http://127.0.0.1/phpinfo.txt
```

## PHP Filter（写文件）
### 作用：
`php://` 访问各个输入/输出流（I/O streams），在 CTF 中经常使用的是 `php://filter` 和 `php://input`，`php://filter` 用于**读取源码**，`php://input` 用于**执行 php 代码**。

### 功能：
PHP 提供了一些杂项输入/输出（IO）流，允许访问 PHP 的输入输出流、标准输入输出和错误描述符，  
内存中、磁盘备份的临时文件流以及可以操作其他读取写入文件资源的过滤器。

| 协议                      | 作用                                                                                                                                                         |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| php://input             | 可以访问请求的原始数据的只读流，在POST请求中访问POST的`data`部分，在`enctype="multipart/form-data"` 的时候`php://input` 是无效的。                                                            |
| php://output            | 只写的数据流，允许以 print 和 echo 一样的方式写入到输出缓冲区。                                                                                                                     |
| php://fd                | (>=5.3.6)允许直接访问指定的文件描述符。例如 `php://fd/3` 引用了文件描述符 3。                                                                                                        |
| php://memory php://temp | (>=5.1.0)一个类似文件包装器的数据流，允许读写临时数据。两者的唯一区别是 `php://memory` 总是把数据储存在内存中，而 `php://temp` 会在内存量达到预定义的限制后（默认是 `2MB`）存入临时文件中。临时文件位置的决定和 `sys_get_temp_dir()` 的方式一致。 |
| php://filter            | (>=5.0.0)一种元封装器，设计用于数据流打开时的筛选过滤应用。对于一体式 `（all-in-one）` 的文件函数非常有用，类似 `readfile()`、`file()` 和 `file_get_contents()`，在数据流内容读取之前没有机会应用其他过滤器。                   |

- **可用的过滤器列表（4类）**
    
| 字符串过滤器 | 作用                |                                  |
| ------ | ----------------- | -------------------------------- |
|        | string.rot13      | 等同于`str_rot13()`，rot13变换         |
|        | string.toupper    | 等同于`strtoupper()`，转大写字母          |
|        | string.tolower    | 等同于`strtolower()`，转小写字母          |
|        | string.strip_tags | 等同于`strip_tags()`，去除html、PHP语言标签 |
    
| 转换过滤器 | 作用                                                                |                                                   |
| ----- | ----------------------------------------------------------------- | ------------------------------------------------- |
|       | convert.base64-encode & convert.base64-decode                     | 等同于`base64_encode()`和`base64_decode()`，base64编码解码 |
|       | convert.quoted-printable-encode & convert.quoted-printable-decode | quoted-printable 字符串与 8-bit 字符串编码解码               |
    
| 压缩过滤器 | 作用                                |                                                                   |
| ----- | --------------------------------- | ----------------------------------------------------------------- |
|       | zlib.deflate & zlib.inflate       | 在本地文件系统中创建 gzip 兼容文件的方法，但不产生命令行工具如 gzip的头和尾信息。只是压缩和解压数据流中的有效载荷部分。 |
|       | bzip2.compress & bzip2.decompress | 同上，在本地文件系统中创建 bz2 兼容文件的方法。                                        |
    
| 加密过滤器 | 作用         |                  |
| ----- | ---------- | ---------------- |
|       | mcrypt.*   | libmcrypt 对称加密算法 |
|       | mdecrypt.* | libmcrypt 对称解密算法 |
    
- **示例**：
    
1. `php://filter/read=convert.base64-encode/resource=[文件名]` 读取文件源码（针对 php 文件需要 base64编码）
```
`http:``//127.0.0.1/include.php?file=php://filter/read=convert.base64-encode/resource=phpinfo.php`
```

2. `php://input + [POST DATA]`执行php代码

```
http://127.0.0.1/include.php?file=php://input

[POST DATA部分]

<?php  phpinfo(); ?>
```

## data 协议（写文件）
`PHP&gt;=5.2.0` 起，可以使用 `data://` 数据流封装器，以传递相应格式的数据。通常可以用来执行 PHP 代码。

### 用法：
```
data://text/plain,你要写入的内容

data://text/plain;base64,你要写入内容的base64
```

```
?file=data://text/plain,<?php phpinfo();?>
```


![[Pasted image 20240429202924.png]]
