## 通用脚本：

## Session 文件包含
首先，我们的 Session 在 php 中是一个全局数组，通过在服务器上存储用户信息以便我们后续使用，Session 的工作机制是：为每个访客创建一个唯一的 id (UID)，并基于这个 UID 来存储变量。UID 存储在 cookie 中，或者通过 URL 进行传导。

Session 的作用是保持我们的会话在一定的时间内都保持通常，而不需要反复的输入我们的对应密码和账号，因此，我们需要将我们的 Session 相关信息存储在一个文件当中。

在得到了我们的这个文件后，我们考虑能不能修改我们的其中的内容是多少，如果可以修改这个文件中的内容，并且我们的代码中是存在一个文件包含漏洞的，我们就可以考虑通过我们的文件包含来得到我们的答案。

## 第一步：找到session文件位置
而我们 Session 的文件存储，一般来说都是存储在我们的文件当中的，这一点我们可以通过我们的 phpinfo 来得到，如果我们默认值为空，那么我们就存储在我们下面两个文件之一：
```
/tmp
/val/lib/php/session
```



## 第二步：修改 session 对应文件：

#### 方法一：我们可以直接控制 session 的值
而我们在每一次访问页面的时候，就可以把我们的对应的 session 存储在我们的对应目录下，那么如果我们的用户可以控制我们的 session 文件，那么我们就可以做到我们的远程命令执行。
```php
<?php
	session_start()
    if(isset($_GET["file"])){
        $a=$_GET['a'];
        $_SESSION['username']=$a
        include($_GET["file"]);
    }else{
        highlight_file(__FILE__);
    }
>
```

这个时候，我们只需要给我们的 `a=(你要执行的php文件)&file=sess_文件`，其中我们的 session 可以通过我们的下面的方式来获得：![[Pasted image 20240323113232.png]]

另外，如果我们不提供上述操作的时候，我们就需要使用到我们的 `session.upload_progress.name` 来进行处理。

这个时候，我们只需要传递参数：
```
?a=(恶意代码)
```
即可修改我们的对应文件恶意信息
#### 方法二：Session_upload_progress
（要求我们的服务器端开启了相关的设置，）


当我们的一个上传在处理中，同时 POST 一个与 INI 设置的 `session.upload_progress.name` 同名变量时，上传进度可以在 `$_SESSION` 中获得，当 php 检测到这种 POST 请求时，他会在 `_SESSION` 中添加一组数据，索引是两拖东西拼起来的值。

这个功能的本意是为了向我们的用户提供一个类似于进度条的功能，在一个上传在处理的时候，我们的上传进度就可以通过我们的 `session` 文件中的相关键值对来获得。

其中，我们的 phpinfo 参数中，下面和我们的相关的信息
![[Pasted image 20240324114155.png]]
![[Pasted image 20240324114251.png]]
其中，我们要求我们的 cleaup 开启后，我们读取 post 请求后会自动删除我们的进度信息。其他的都是默认值。

`strict_mode` 选项，决定了我们是否可以使用我们手动床在的 `session_id` ，如果开启了，我们就不能够使用，否则，我们只能使用我们系统初始化的值。

这个时候，我们可以通过上传一个文件，然后修改我们的 session_id 为一个目标值，之后我们就能创建一个已知的 `sess` 文件。
```text
POST /haha.php
...
...
Cookie=PHPSESSION=对应的文件名
Accept-Encoding:
Accept-Language:
Connection:

Content-Language:
Connection:
Content

--WebKitFormBoundarydsH3huyV98--
Content-Disposition:form-data;name="PHP_SESSION_UPLOAD_PROGRESS"//必须要有，没有也要自己加（）
(写入你要插入的恶意代码)
--WebKitFormBoundarydsH3huyV98--

```

我们上传完这一个之后，我们就会在对应的位置生成一个 `sess_你取的名字` 的文件，其中包含有下面的信息：
```text
upload_progress_你写的恶意代码
```

