定义1.1.1(整除) 设$a,b$是任意两个整数，其中 $b\neq0$,如果存在一个整数$q$使得等式$a=bq$成立，则称$b$整除$a$或者$a$被$b$整除，记作$b\mid a.$如果$b\mid a$,则$b$叫做$a$的**因数**，而$a$叫做$b$的 倍数.如果$b$不能整除$a$,则记作$b\not\mid a.$


0是任何整数的倍数。对于任意整数 $a,\pm1,\pm\alpha$都是它的因数，称这四个因数为整数$_{\alpha}$的显然因数或**平凡因数**，整数$_{a}$的其他因数称为非显然因数或非平凡因数。

## 性质：
1. 传递性：
$$\boxed{(1)\text{ 若}c\mid b,b\mid a,\text{则}c\mid a.\quad(\text{整除的传递性}!)}$$
证明：
设$c|b,b|a$,那么我们就有证书$q_{1},q_{2}$,是的我们的$b=cq_{1},a=bq_{1}$,于是就是$a=c(q_{1}q_{2})$因为$q_{1}q_{2}$是整数，所以$c|a$。

(2) 若$c\mid a,c\mid b$,则对任意整数$s,t$,有$c\mid sa+tb.$
证明：同上

(3)设$m\neq0,a|b$当且仅当$ma|mb$
证明：当$m\neq0$ 时，$b=aq\Leftrightarrow mb=(ma)q$。

(4)设$a,b$都是非零整数，若$a\mid b,b\mid a$,则 $a=\pm b.$
$$\begin{equation}
    (4)\text{设}a,b\text{都是非零整数,若}a\mid b,b\mid a,\text{则 }a=\pm b.\\\text{证 因}a\mid b,b\mid a,\text{所以存在整数}q_1,q_2,\text{使得}\\a=bq_1,b=aq_2\\a=bq_1=(aq_2)q_1=a(q_1q_2)\\\text{于是}q_1q_2=1,\text{ 因}q_1,q_2\text{是整数},\text{所以}\\q_{1}=q_{2}=\pm1,\quad\text{从而}a=\pm b.
\end{equation}$$


## 欧几里得除法：
定理1.1.2(欧几里得除法/带余除法) 设$a,b$是两个
整数，其中$b>0$,则存在唯一的整数 $q,r$,使得
$$a=bq+r,\quad0\leq r<b$$

其中$q$叫做$a$被$b$除所得的不完全商$,r$叫做$a$被$b$除所
得的余数.存在性证明：

$$\begin{aligned}&\text{证(}q,r\text{的存在性)考虑数列}\\&\cdots,-3b,-2b,-b,0,b,2b,3b,\cdots\\&\text{它们将实数轴分成长度为的区间,}\\&\text{则}a\text{必定落在其中的一个区间中},\quad\text{即存在整数}q,\\&\text{使得}\quad qb\leq a<(q+1)b\quad\Rightarrow0\leq a-bq<b\\&\text{令}r=a-bq,\quad\text{则有}\quad a=bq+r,\quad0\leq r<b\end{aligned}$$


$(q,r$的唯一性)若有整数$q,r$和$q_1,r_1$,使得


$$\begin{aligned}&a=bq+r,\quad0\leq r<b\\&a=bq_{1}+r_{1},\quad0\leq r_{1}<b\end{aligned}\quad\Rightarrow b(q-q_{1})=r_{1}-r.$$

若$q\neq q_1$,则$\mid b(q-q_1)\mid\geq b$,而$\mid r_1-r\mid<b$,矛盾.

## 公因数：
定义1.1.2(公因数) 设$a_1,a_2,\cdots,a_n$是$n(n\geq2)$个整数若整数$d\mid a_k(k=1,2,\cdots,n)$, 则称$d$是$a_1,a_2,\cdots,a_n$的一个公因数.

定义1.1.3(最大公因数) 若$a_{_1},a_{_2},\cdotp\cdotp\cdotp,a_{_n}$不全为零， 则整数$a_1,a_2,\cdots,a_n$的所有公因数中最大的一个公因数叫做最大公因数.记作$(a_1,a_2,\cdots,a_n).$

特别的，如果我们的$gcd(a_{1},\dots,a_{n})=1$，则认为我们的所有数字之间沪指。


## 性质：
最大公因数可描述为：
$d>0$是$a_1,a_2,\cdots,a_n$的最大公因数
$\Leftrightarrow ( 1)$ $d\mid a_{1}, d\mid a_{2}, \cdotp \cdotp \cdotp , d\mid a_{n};$
(2) 若e$|a_1,e|a_2,\cdots,e|a_n$,则$e|d.$

$\boxed{(4)\text{ 设}a,b,c\text{是三个不全为零的整数},\text{如果 }a=bq+c,}$ 其中$q$是整数，则$(a,b)=(b,c).$

证 ：
设$(a,b)=d,(b,c)=d’$,则$d\mid a,d\mid b$,于是
$$d\mid a+(-q)b\Rightarrow d\mid c,$$
所以$d$是$b,c$的公因数，从而$d\leq d^\prime.$同理可证，$d’$是$a,b$的公因数，因而$d’\leq d$故
$$d=d’.$$

(5)若 $( a, c) = 1\textbf{, }b| c\textbf{, 则 }( a, b) = 1$

证明：设$( a, b) = d\textbf{ , 则 由 }d| b, b| c$, 可 得 $d| c$,又$d|a$所以 $d|(a,c)$ 。由$(a,c)=1$,可得 $d=1$即$(a,b)=1$。

(6)$\boxed{(\frac{a}{(a,b)},\frac{b}{(a,b)})=1}$

两个数同时除以最大公因数后一定互素！

## 最小公倍数：
定义1.1.4(公倍数) 设$a_1,a_2,\cdots,a_n$是$n$个整数， 若$a_1\mid m,a_2\mid m,\cdots,a_n\mid m$,则$m$叫做$a_1,a_2,\cdots,a_n$的一个公倍数.

定义1.1.5(最小公倍数)$a_1,a_2,\cdots,a_n$的所有公倍数中最小正整数叫做最小公倍数，记作$[a_1,a_2,\cdots,a_n].$

性质：定理 1.1.4 设 $a,b,m$ 是整数，$a\mid m,b\mid m$,则 $[a,b]\mid m.$

证明 (自学): 不妨设 $m=q[a,b]+r$ ,其中 $q$ 是整数，则 $0\leq r<[a,b]$ ,又 $r= m- q[ a, b]$ , $a| m, b| m, a| [ a, b] , b| [ a, b]$ ,由整除的性质可知 $a|r,b|r$,由 $_[a,b]$ 的最小性可知 $r=0$。因此有 $[ a, b] | m$ 。

## 扩展欧几里得算法
定理 1.2.2 对于任意两个整数 $a,b$ ,存在整数 $x,y$ 使得
$$(a,b)=xa+yb$$

证明：设 $z$ 是全体整数集合。做一个如下集合：$S=\{|xa+yb||x,y\in Z\}$ 。$S$ 中的元素显然大于等于 0。设 $d$ 为 $s$ 中的最小正整数，则 $d$ 可表示为 $a,b$ 的组合，设
$$d=ua+vb$$
现在我们证明 $d|a$ 且 $d|b$ 。做带余除法：
$$a=qd+r,0\leq r<d$$

于是 $r= a- qd= a- q( ua+ vb) = ( 1- qu) a- qvb$。这说明 $r$ 也可表示为 $a,b$ 的组合，则 $r\in S$。由于 $d$ 是 $s$ 中的最小正整数，所以只有 $r=0$。故 $d|a$ 。同理 $d|b$ 。设 $c$ 是 $a,b$ 的任意公因子，由 $c|a$ 和 $c|b$ 得 $c|d=ua+vb_{\bullet}$ 故 $d$ 是 $a,b$ 的最大公因子，证毕。

### 计算方法：
在广义欧几里得除法中，由
$$r_0=r_1q_1+r_2,$$
$$r_1=r_2q_2+r_3$$

$$\boxed{r_n=r_{n-2}-r_{n-1}q_{n-1},}$$
$$r_{n-1}=r_{n-3}-r_{n-2}q_{n-2},$$

$r_{n- 3}= r_{n- 2}q_{n- 2}+ r_{n- 1}$, $\Longrightarrow$ $$r_3= r_1- r_2q_2$$,

$$\boxed{r_{n-2}=r_{n-1}q_{n-1}+r_n,}$$


$r_2=r_0-r_1q_1$

$$r_{n-1}=r_nq_n$$

逐次消去 $r_n-1,r_{n-2},\cdotp\cdotp\cdotp,r_3,r_2$,可找到整数 $x,y$,使得
$$(a,b)=(r_0,r_1)=(0)r_0+(0)r_1=xa+yb=(a,b)$$

$$r_0=a,r_1=b.$$

![[Pasted image 20241103105912.png]]
![[Pasted image 20241103110315.png]]

$$\begin{array}{|c}\text{所以 }x=y_1,&y=x_1-\frac aby_1\end{array}^{\nu}$$

### 推论：
推论 1.2.2 设 $a,b,c$ 为不等于 0 的整数，

$(1) \textit{若  }c| ab, ( a, c) = 1$,则 $c|b$ ;

(2) 若 $a|c,b|c$ 且 $( a, b) = 1\textbf{, 则 }ab| c$;

$\begin{array}{cc}{(3)\text{若 }(a,c)=1,(b,c)=1\textbf{,则 }(ab,c)=1\text{。}}\end{array}$


(1)证明：因为 $(a,c)=1$,根据定理 1.2.3 存在整数 $u,v$,使得
$ua+vc=1$ 两边同时乘以 $b$ 可得 $uab+vcb=b$由于 $c|uab+vcb\_$、因此 $c|b$

(2) 证明：由 $(a,b)=1$ 可知存在整数 $u,v$ 使得 $ua+vb=1$ 两边同时乘以 $c$,可得 $uac+vbc=c$,由于 $a|c,b|c$ ,所以 $ab|uac,ab|vbc$ 。因此有 $ab|c$ 。两边同时乘以 c，可得 $uac+vbc=c$,由于 $a|c,b|c$ ,所以 $ab|uac,ab|vbc$ 。因此有 $ab|c$

(3)若 $( a, c) = 1, ( b, c) = 1\textbf{, 则 }$ $( ab, c) = 1$
证明：根据定理 1.2.3, 存在整数 $s,t$ 使得 $sa+tc=1$。同理，存在整数 $u,v$,使得 $ub+vc=1$ 于是有 $(sa+tc)(ub+vc)=(su)ab+(sva+tub+tvc)c=1$ 根据定理 1.2.3 有 $( ab, c) = 1$ 