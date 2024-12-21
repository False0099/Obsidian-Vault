集合根据等价关系可分为两两互不相交的集合。整数的同余关系是一个等价关系。给定正整数 m，全体整数可按照模 m 是否同余分为若干两两不相交的集合，使得每一个集合中的任意两个正整数对模 m 一定同余，而属于不同集合的任意两个整数对模 m 不同余，每一个这样的集合称为模 m 的同余类或剩余类。


## 定理
定理 2.2.1 对于给定的正整数 m ,有且恰有 m 个不同的模$\mathbf{m}$ 的剩余类。

证明：
根据带余除法，对于任意整数 a，都有
$$\mathrm{a~=~qm+r~0\leqslant r<m}$$
也就是说任何一个整数模 m 必然与 $\{0,1,2,\cdots,m-1\}$ 中的一个同余，而且这 m 个整数模 m 互不同余。所以模 m 的剩余类有且恰有 m 个。


$$\begin{aligned}&\text{定义}2.2.1\quad\text{一个剩余类中的任一数叫做该类的剩余或}\\&\text{代表元,若}r_{0},r_{1},\cdots,r_{m-}\text{是}m\text{个整数,并且其中任何两个数}\\&\text{都不在同一个剩余类里,则}r_{0},r_{1},\cdots r_{m-}\text{叫做模 }m\text{ 的一个}\\&\text{完全剩余系.(在模}m\text{的所有剩余类中各取一个代表元组}\\&\text{成完全剩余系)(}r_{i}\in[i])\\&\text{注:1.模}m\text{的剩余类有}m\text{个,在同一剩余类中的数互}\\&\text{相同余,不在同一剩余类的数互不同余}.\\&2.\text{ 模}m\text{的完全剩余系恰有}m\text{个整数}.\\&m\text{个连续的整数也是模}n\text{的完全剩余系}.\\&\text{完全剩余系 }0,1,2,...,m-1\text{称为最小非负完全剩余系。}\end{aligned}$$


定理 2.2.2 设 $m$ 是正整数，$(a,m)=1,b$ 是任意整数，若 $x$ 遍历模 $m$ 的一个完全剩余系，则 $ax+b$ 也遍历模 $m$ 的一个完全剩余系.

证明采用反证法。

定理 2.2.3 若 $(m_1,m_2)=1,m_1>0,m_2>0$,而 $X,Y$ 分别遍历模 $m_1,m_2$ 的完全剩余系则 $m_2X+m_1Y$ 遍历模 $m_1m_2$ 的完全剩余系.

证因 $X, Y$ 分别遍历 $m_1, m_2$ 个整数时，$m_2X+$ $m_1Y$ 遍历 $m_1m_2$ 个整数. 所以只需证明这 $m_1m_2$ 个整数对模 $m_1m_2$ 两两不同余即可.

若有整数 $X= x_1$, $Y= y_1$,和 $X= x_2$, $Y= y_2$,使得
$$m_2x_1+m_1y_1\equiv m_2x_2+m_1y_2\mathrm{~(mod~}m_1m_2)$$

则因 $m_1\mid m_1m_2$,所以 $m_2x_1+m_1y_1\equiv m_2x_2+m_1y_2({\mathrm{mod}}m_1)$ 所以 $m_2x_1\equiv m_2x_2$ $( {\mathrm{mod}}m_1)$ 于是 $m_1\mid m_2(x_1-x_2)$ 但因 $(m_1,m_2)=1$,所以 $m_1\mid x_1-x_2.$ 于是 $x_1\equiv x_2$ (mod $m_1)$,同理 $y_1\equiv y_2$ (mod $m_2)$，故定理成立。

## 欧拉函数：
定义 2.2.3 与 $m$ 互素的剩余类的个数称为欧拉函数，记作 $\varphi(m).$

定义如果模 $m$ 的剩余类里面的数与 $m$ 互素，就把这个类叫做一个与模 $m$ 互素的剩余类，(又称既约/简化剩余类) 。简化剩余类与剩余的选取无关 ( ?).

例如：
模 10 的简化剩余类. 1,3,7,9 是模 10 的一个简化剩余系.
为此，只需在模 $m$ 的最小非负完全剩余系 0,1,2, , $m-1$ 里找出与 $m$ 互素的所有整数，则这些整数的全体就构成模 $m$ 的一个简化剩余系.这样的模 $m$ 简化剩余系称为最小非负简化剩余系 - $\mathbb{Z}_m^*$

定理：
设 $m$ 是一个正整数，$(r_i,m)=1,i=1,2,3$, $...,\varphi(m)$, 且 $r_i\not\equiv r_j$ (mod $m)$ ($i\neq j)$,则 $r_1,r_2,...r_{\varphi(m)}$ 是模 $m$ 的一个简化剩余系.

$$\begin{aligned}&\text{证 因模}m\text{的简化剩余系恰含 }\varphi(m)\text{个整数}.\\&\text{又因}r_{i}\not\equiv r_{j}\mathrm{~(mod~}m)\mathrm{~(}i\neq j\text{),所以}r_{1},r_{2},\cdotp\cdotp\cdotp r_{\varphi(m)}\text{是模}\\m\text{的不同剩余系中的 }\varphi(m)\text{个数}.\\&\text{由题设,这}\varphi(m)\text{个数都与}m\text{互素}.\text{ 故它们组成}\\\text{模}m\text{的一个简化剩余系}.\end{aligned}$$

## 欧拉定理：
定理 2.2.8 (Euler 定理) 设 m 是大于 1 的整数，(a, m)=1,
则 $a^{\varphi ( m) }\equiv 1($ mod $m) .$

证取 $r_1,r_2,\cdots,r_{\varphi(m)}$ 为模 $m$ 的一个简化剩余系，则因 $(a,m)=1$,于是 $ar_1,ar_2,\cdots,ar_{\varphi(m)}$ 也是模 $m$ 的简化剩余系，因此

$$ar_i\equiv r_j({\mathrm{mod}}m)$$

所以 $(ar_1)(ar_2)\cdots(ar_{\varphi(m)})\equiv r_1r_2\cdots r_{\varphi(m)}$ (mod $m)$

即 $a^{\varphi ( m) }r_1r_2\cdots r_{\varphi ( m) }\equiv r_1r_2\cdots r_{\varphi ( m) }$ $( {\mathrm{mod}}m)$

又因   $( r_i, m) = 1$, $i= 1, 2, \cdots , \varphi ( m)$

所以

$$(r_1r_2\cdots r_{\varphi(m)},m)=1$$

故

$$a^{\varphi(m)}\equiv1\quad({\mathrm{mod}}m).$$


## 阶：

![[Pasted image 20241103141952.png]]

![[Pasted image 20241103141941.png]]

## 威尔逊定理：
定理 2.2.10 (Wilson 定理) 设 $p$ 是一个素数，则
$$(p-1)!\equiv-1\mathrm{~(mod~}p)$$

证 ：
$p=2$ 时，$(2-1)!\equiv-1$ (mod 2), 结论成立.
设 $p\geq3$,则对于每个 $a,1\leq a<p$,存在唯一的整
数 $a^{\prime},1\leq a^{\prime}<p$,使得
$$aa^{\prime}\equiv1\mathrm{~(mod~}p)$$
于是 $a^\prime=a\Leftrightarrow a^2\equiv1$ (mod $p)$
这时，$a=1$ 或 $a=p-1.$

因此当 $a$ 与 $a^\prime$ 取 2 $,\cdotp\cdotp\cdotp,p-2$ 中的数时 $,a\neq a^\prime.$
把 $2,3,\cdotp\cdotp\cdotp,p-2$ 中的 $a$ 与 $a‘$ 配对，有
$$\underbrace{(aa^{\prime})(aa^{\prime})\cdotp\cdotp\cdotp(aa^{\prime})}_{\frac{p-3}2\text{对}}=2\cdot3\cdots p-2$$
因   $aa^{\prime}\equiv1$ (mod $p)$
所以   $2\cdot3\cdots(p-2)\equiv1$ (mod $p)$
故 $( p- 1) ! \equiv 1\cdot ( p- 1) \equiv - 1$ (mod $p)$