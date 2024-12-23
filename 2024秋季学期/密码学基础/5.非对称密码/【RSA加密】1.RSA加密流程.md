## 密钥生成 ：
我们的 RSA 中的密钥生成方案可以遵循下面的方法进行：
1. 选择两个大素数 $P,Q$,
2. 计算 $N=P*Q$
3. 计算 $\phi(N)=(P-1)*(Q-1)$
4. 从自然数中，选择一个 $e$，要求满足 $(e,\phi(N))=1$。
5. 找到一个和 $e$ 对应的 $d$,要求满足 $e*d=1\text{ mod }\phi(N)$

同时，我们向我们的外界用户公布 $N,d$，作为外界用户与自己沟通的**公钥**。
## 加密：

给定公钥 $(n, e)=k$ 和明文 $x$,则加密函数：$y=e_{k_{pub}}(x)\equiv x^{e}\bmod n$，其中 $x$, $y\in \mathbb{Z} _n$ 。并且我们的公钥 $N$ 就是我们的**两个大质数相乘**。

## 解密：
RSA 解密给定私钥 $d=k_pr$ 及密文 y, 则解密函数为：$x=d_{k_{pr}}(y)\equiv y^d\bmod n$。其中 $x$, $y\in \mathbb{Z} _n$ 。并且我们的公钥 $N$ 就是我们的**两个大质数相乘**。



![[Pasted image 20241118091850.png]]
![[Pasted image 20241118091858.png]]


# $\mathbb{RSA}$ 示例

$\cdot$ 密钥生成：p=11, q=13,
$$\mathrm{N=p\cdot q=143,~}\varphi(N)=120$$
公钥 e=13, 私钥 d=37

$\cdot$ 加密：明文 $\mathfrak{m}=3$,

$\text{密 文  c= m}^{\mathrm{e} }\left ( \mathrm{mod~N}\right ) = 3^{13}($ mod 143)=16

$\cdot$ 解密：密文 $\mathfrak{c}=16$,

解密后明文 m' $=c^d({\mathrm{mod~N}})=16^{37}({\mathrm{mod}}143)=3$

## 安全性：
·理论上基于大数分解的困难性，即分解模数 N 的困难性

·随着计算机计算能力的发展，为了确保模数 N 不被分解，
N 需要取得越来越大

·现今一般 N 都取成 1024 bit 或 2048 bit，即 p，q 都是 512bits 或
者 1024 bits