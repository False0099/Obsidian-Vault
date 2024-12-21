## 绕过访问限制

### file 协议
```php
if($host!=='www.baidu.com'){
	die("wrong site");
}
$ci=curl_init();
$res=curl_exec($ci);
curl_close($ci);
```

这个时候，我们要求我们的域名必须是百度，那么我们就可以通过我们的 file 协议来处理, 因为我们的网址对于我们的原来的没有任何的区别。
```
file://www.baidu.com/C:/flag/txt
```


## 后续会添加
用 `#` 或 `?` 来过滤

## 绕过格式化字符
在我们的末尾添加 `%s%`,即可同时把我们的原来的转换为 `%s` 并且还能让我们的末尾转变为 `%d` 的原来的形态，

## 编码方法：
1. 编码绕过
2. IP 地址转 10 进制
3. IP 地址转 16 进制