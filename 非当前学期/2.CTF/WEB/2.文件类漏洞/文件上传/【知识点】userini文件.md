`.user.ini` 不管是 `nginx/apache/iis` 只要是以 fastcgi 运行的 php 都可以用这个方法：
原理：
php 会在每个目录下扫描 `ini` 文件，就是一个可以由用户自定义的 `php.ini`,但是前提是该目录下有 `php` 文件才会生效。
也就是说，如果我们上传了一个 `.user.ini` 配置文件，这个文件会在我们的当前目录下生效。生效的效果就会导致我们 `.user.ini` 中的字段也会被 `php` 视为配置文件来处理，从而导致 `php` 文件解析漏洞。
配置：
`auto_append_file`: 将文件包含到页面的顶部
`auto_prepend_file`: 将文件包含到页面的底部

## 操作
1. 上传一个一句话木马
![[Pasted image 20231127200323.png]]
2. 用 burp 抓包修改，发现我们有非法后缀
![[Pasted image 20231127200341.png]]
3. 我们用 `phtml` 也不行，
![[Pasted image 20231127200413.png]]
4. 将我们的后缀名修改为 `.ini`,我们的后缀名就变得合法了
![[Pasted image 20231127200448.png]]

5. 接下来，我们就可以通过我们的 `script` 标签绕过
![[Pasted image 20231127200525.png]]

6. 之后我们发现我们检测不是图片格式，我们就要在我们的文件中添加一个签名 `GIF89A`，来绕过我们的格式判断。
 ![[Pasted image 20231127200618.png]]
7. 检查之后，我们发现，我们存在 `.user.ini` 漏洞，我们此时再给我们的系统上传一个 `.user.ini` 文件，
```php
auto_append_file=shell.jpg;
```

8. 之后，我们再上传一个假 jpg 文件
![[Pasted image 20231127201225.png]]

9. 之后，我们运行我们所在文件中的 `php` 文件即可。
![[Pasted image 20231127201332.png]]

