PHP 带有很多内置 URL 风格的封装协议，PHP 提供了一些杂项输入/输出（IO）流，允许访问 PHP 的输入输出流、标准输入输出和错误描述符，内存中、磁盘备份的临时文件流以及可以操作其他读取写入文件资源的过滤器。（**是我们访问文件甚至访问自己创造的文件的一种方法**）

假设我们有下面的一段代码：
```php
<?php
include $_REQUEST['file'];
>
```
我们就可以让我们的 `file` 等于我们的某一个伪协议，之后就能得到我们的答案。

例如:
- [file://](https://www.php.net/manual/zh/wrappers.file.php) — 访问本地文件系统,_文件系统_ 是 PHP 使用的默认封装协议，展现了本地文件系统。 当指定了一个**相对路径**（不以/、\、\\或 Windows 盘符开头的路径）提供的路径将基于当前的工作目录。 在很多情况下是脚本所在的目录，除非被修改了。 使用 CLI 的时候，目录默认是脚本被调用时所在的目录。
- [http://](https://www.php.net/manual/zh/wrappers.http.php) — 访问 HTTP(s) 网址,允许通过 HTTP 对文件/资源进行可读访问。默认使用 HTTP 1.0 GET。 HTTP 请求会附带一个 `Host:` 头，用于兼容基于域名的虚拟主机。如果在你的 php.ini 文件中或字节流上下文（context）配置了 [user_agent](https://www.php.net/manual/zh/filesystem.configuration.php#ini.user-agent) 字符串，它也会被包含在请求之中。数据流允许读取资源的 _body_，而 headers 则储存在了 [$http_response_header](https://www.php.net/manual/zh/reserved.variables.httpresponseheader.php) 变量里。

如果需要知道文档资源来自哪个 URL（经过所有重定向的处理后）， 需要处理数据流返回的系列响应报头（response headers）。

The [from](https://www.php.net/manual/zh/filesystem.configuration.php#ini.from) directive will be used for the `From:` header if set and not overwritten by the [上下文（Context）选项和参数](https://www.php.net/manual/zh/context.php).
- [ftp://](https://www.php.net/manual/zh/wrappers.ftp.php) — 访问 FTP(s) URLs
- [php://](https://www.php.net/manual/zh/wrappers.php.php) — 访问各个输入/输出流（I/O streams）
- [zlib://](https://www.php.net/manual/zh/wrappers.compression.php) — 压缩流
- [data://](https://www.php.net/manual/zh/wrappers.data.php) — 数据（RFC 2397）
- [glob://](https://www.php.net/manual/zh/wrappers.glob.php) — 查找匹配的文件路径模式
- [phar://](https://www.php.net/manual/zh/wrappers.phar.php) — PHP 归档
- [ssh2://](https://www.php.net/manual/zh/wrappers.ssh2.php) — 安全外壳协议 2
- [rar://](https://www.php.net/manual/zh/wrappers.rar.php) — RAR
- [ogg://](https://www.php.net/manual/zh/wrappers.audio.php) — 音频流
- [expect://](https://www.php.net/manual/zh/wrappers.expect.php) — 处理交互式的流

## Filter 流
Filter 的作用是对我们当前的 `wrapper` 进行一定的处理，例如我们常见的：转换为 base64 编码。

假设服务端 **include 函数**的**路径参数**可控，正常情况下它会将目标文件当作  PHP 文件去解析，如果解析的文件中存在“<？php”等 PHP 的相关标签，那么标签中的内容会被作为 PHP 代码执行。我们如果直接将这种**含有 PHP 代码的文件的文件名传入 include 函数**，那么由于 PHP 代码被执行而**无法通过可视文本的形式泄露**。但这时可以通过**使用 Filter 避免这种情况的发生**。例如，比较常见的 Base64相关 Filter 可将文件流编码成 Base64  的形式，这样读取的文件内容中就不会存在 PHP 标签。而更严重的是，如果服务端开启了远程文件包含选项 allow_url_include，我们就可以直接执行远程 PHP 代码

