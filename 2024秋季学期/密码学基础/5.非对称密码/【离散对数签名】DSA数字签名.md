## 密钥生成：
已知公共参数：素数 p、素数 q、模 p 的 q 阶元 g

随机选取 (1, q)中的 x，作为私钥

计算 $y=g^x$ (mod p), 作为公钥


## 签名：
$$\begin{aligned}\text{签名:}&\text{明文为m}\\&计算m的Hash值H(m),表示为(0,q)中的值\\&随机选取(0,q)中的k\\&计算r=(g^kmodp)(modq)\\&计算s=(H(m)+xr)k^{-1}(modq)\\&签名(r,s)附于消息m后\end{aligned}$$

## 验证：
验证：计算消息的 Hash 值 H(m)
计算 w= $s^{- 1}$ ( mod q) 
验证是否有 r=($g^{H ( m)w}y^{\mathrm{rw}}modp) ( modq)$
