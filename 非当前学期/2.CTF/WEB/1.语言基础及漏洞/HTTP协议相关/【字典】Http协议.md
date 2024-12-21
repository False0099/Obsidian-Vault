## 要求：
1. 传递一个 post 参数，要求这个参数中不能包含有 http，不能包含 hello123, 并且读取到的结果也是 hello 123。
我们的绕过思路是：通过我们的 data 协议
```
abc=data//text/plain.base64/(hello123的base64)编码
```

## 本地访问：
1. 修改我们的 X-Forwarded-For
```text
X-Forwarded-For:127.0.0.1
X-Forwarded:127.0.0.1
Forwarded-For:127.0.0.1
X-Forwarded-Host:127.0.0.1
X-remote-IP:127.0.0.1
X-remote-addr:127.0.0.1
True-Client-IP:127.0.0.1
X-Client-IP:127.0.0.1
X-Real-IP:127.0.0.1
Ali-CDN-Real-IP:127.0.0.1
Cdn-Src-IP:127.0.0.1
CF-Connecting-IP:127.0.0.1
X-Cluster-Client-IP:127.0.0.1
WL-Proxy-Client-IP:127.0.0.1
Proxy-Client-IP:127.0.0.1
Fastly-Client-Ip:127.0.0.1
True-Client-Ip:127.0.0.1
```

## 指定浏览器：


## 指定来源网站：
```
Referer:google.com
```

