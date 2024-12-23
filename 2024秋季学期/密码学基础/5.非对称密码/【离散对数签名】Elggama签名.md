## 密钥生成：
已知公共参数：素数 p 与模 p 的原根 g

随机选取 (1, p-1)中的 x，作为私钥

计算 y=gx (mod p), 作为公钥

## 签名：
明文为 m
1. 计算 m 的 Hash 值 H (m), 表示为[1, p-1]中的值
2.  随机选取[1, p-2]中与 p-1 互素的 k
3.  $\operatorname{\text{计算r}=g^k(mod~p)}$
4. 计算 $\mathbf{s}=(\mathsf{H}(\mathfrak{m})-$ xr $)\mathsf{K}^-1($ mod p-1)
5. 签名 (r, s)附于消息 m 后

