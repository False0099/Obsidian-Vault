首先将 DSS 与 RSA 的签名方式做一比较。RSA 算法既能用于加密和签名，又能用于密钥交换。与此不同， DSS 使用的算法只能提供数字签名功能。图7-2用于比较 RSA 签名和 DSS 签名的不同方式：

![[Pasted image 20250101211757.png]]

其中 $k$ 表示一个随机数，与 RSA 相比，主要在于引入了随机数 $K$。并且 $DSA$ 签名基于内容不同。


DSA 是在 ElGamal 和 Schnorr 两个签名方案 (看下一节)的基础上设计的，其安全性基于求离散对数的困难性。

$$\begin{aligned}&(1)\text{ 全局公开钥}\\&p\text{:满足 }_{2^{L-1}<p<2^L}\text{的大素数,其中512}\leq L\leq1024\text{ 且L是64的}\\&\text{倍数。}\\&q\mathrm{:~}p\text{-1的素因子,满 足}2^{159}<q<2^{160}\text{ 即}q\text{长为160比特。}\\&g.\quad g\equiv h^{(P-1)/q}\mathrm{~mod~}p\text{ ,其中 }h\text{ 是满足 }1<h<p-1\text{ 且使得}\\&h^{(p-1)/q}\mathrm{~mod~}p>1\text{ 的任一整数。}\end{aligned}$$


$$\begin{aligned}&(2)\text{用户秘密钥}\\&(2)\text{用户秘密钥}\\&(3)\text{ 用户的公开钥 }y\\&y\equiv g^x\mathrm{~mod~}p\mathrm{~。}\\&(4)\text{ 用户为志签消息选取的秘密数 }k\\&k\text{ 是满足 }0<k<q\text{ 的随机数或伪随机数。}\\&(\bullet)\text{ 签名过程}\\&\text{用户对消息 }M\text{ 的签名为}(r,s),\\&\text{其中 }r\equiv(\operatorname{g}^k\operatorname{mod}p)\operatorname{mod}q,s\equiv[k^{-1}(H(M)+xr)]\operatorname{mod}q\\&H(M)\text{是由SHA求出的哈希值。}\end{aligned}$$

