定义 2.1.1 (同余)给定一个止整数 m，如果对整数$a,b$ 有 $m\mid a-b$,则称 $a\not b$ 模 $m$ 同余，记作
$$a\equiv b\mathrm{~(mod~}m),$$
否则，则称 $a\textit{, b模 }m$ 不同余，记作 $a\not\equiv b$ (mod $m).$


## 定理：
(1) $a\equiv b$ (mod $m)$ 的充要条件是存在整数 $k$,使得 $a=b+km.$

证：
$a\equiv b$ (mod $m)\Leftrightarrow m\mid a-b$
$\Leftrightarrow$ 存在整数 $k$ 使得 $a-b=km$
$\Leftrightarrow a=b+km.$

(2) 整数 $a,b$ 模 $m$ 同余的充要条件是 $a,b$ 被$m$ 除的余数相同.

证：

设 $a=qm+r,0\leq r<m$,
$b= q^{\prime }m+ r^{\prime }$, $0\leq r^{\prime }< m$
则 $a-b=(q-q^{\prime})m+(r-r^{\prime})$
于是 $m\mid a- b\Leftrightarrow m\mid r- r^{\prime }.$ 但 $0\leq\mid r-r^{\prime}\mid<m$,
所以 $m\mid r-r^{\prime}\Leftrightarrow r-r^{\prime}=0$,即 $r=r^\prime.$

定理 2.1.2 模 $m$ 同余是等价关系，即

(1) 对任一整数 $a, a\equiv a$ ( mod $m) ;$
(2)若 $a\equiv b$ (mod $m)$,则 $b\equiv\alpha$ (mod $m);$ (对称性)
(3)若 $a\equiv b$ (mod $m),b\equiv c$ (mod $m)$,则 $a\equiv c\left(\mathrm{mod}m\right)$

定理 2.1.3 设 $m$ 是一个正整数，$a_1,a_2,b_1,b_2$ 是整数.若
$$a_1\equiv b_1\mathrm{~(mod~}m),\mathrm{~}a_2\equiv b_2\mathrm{~(mod~}m),$$

则

$( \mathbf{i} )$ $a_1\pm a_2\equiv b_1\pm b_2$ (mod $m)$
$\begin{pmatrix}\mathbf{ii}\end{pmatrix}a_1a_2\equiv b_1b_2$ (mod $m)$
特别地，若 $a\equiv b$ (mod $m)$, 则 $ak\equiv bk$ (mod $m)$

证:
因 $a_1\equiv b_1$ (mod $m)$, $a_2\equiv b_2$ (mod $m)$,由定理1：$a_1=b_1+k_1m$ (mod $m)$, $a_2= b_2+ k_2m$,

## 模意义下的除法：
定理 2.1.4 (1)设 m 是一个正整数，$ad\equiv bd$ (mod $m)$
如果 $(d,m)=1$,则$$a\equiv b\quad(\operatorname{mod}m)$$

证：

若 $ad\equiv bd$ (mod $m)$, 则 $m\mid ad-bd$,即
$$m\mid d(a-b)$$

因 $(d,m)=1$,所以 $m\mid a-b$,故

$$a\equiv b^2(\operatorname{mod}m)$$

上面几个性质，其模 $m$ 不发生变化，属于基本性质.

定理 2.1.4 (2) 设 $m$ 是正整数，$a\equiv b$ (mod $m)$,如果 $d$ 是 $a,b$ 及 $m$ 的任一公因数则

$$\frac ad\equiv\frac bd\mathrm{~(mod}\frac md).$$

 证：
 因 $a\equiv b$ (mod $m)$,所以存在整数 $k$,使得
$$a=b+mk$$

于是 $\frac ad=\frac bd+\frac mdk$, 因 $\frac ad,\frac bd,\frac md$ 都是整数，故

$$\frac ad\equiv\frac bd({\mathrm{mod}}\frac md)$$

定理 2.1.5 (2)设整数 n 有十进制表示式：
$$n=a_k10^k+a_{k-1}10^{k-1}+\cdots+a_110+a_0,\quad0\leq a_i<10$$
则 $3|n\Leftrightarrow3|a_k+a_{k-1}+\cdots+a_0$,
而 $9|n\Leftrightarrow9|a_k+a_{k-1}+\cdots+a_0.$
证：
因 $10\equiv1({\mathrm{mod}}3),a_i\equiv a_i({\mathrm{mod}}3),10^i\equiv1({\mathrm{mod}}3)$,
$0\leq i\leq k$,由定理 2.1.3 有
$$a_k10^k+a_{k-1}10^{k-1}+\cdots+a_110+a_0$$
$$\equiv a_k+a_{k-1}+\cdots+a_1+a_0({\mathrm{mod}}3)$$
$n$ 除以 3 的余数与 $a_k+a_k-1+\cdots+a_1+a_0$ 除以 3 的余数相同！

