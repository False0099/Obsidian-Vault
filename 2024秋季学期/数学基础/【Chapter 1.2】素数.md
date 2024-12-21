## 素数：
定义1.3.1(素数):设整数 $n\neq0,\pm1$,如果除了 $\pm1$ 和 $\pm n$ 外，$n$ 没有其它因数，则 $n$ 叫做素数(或质数) 或不可约数),否则 $n$ 叫做合数.


定义 1.3.1（互素）：
设 $p$ 是一个素数，$a,b$ 为任意整数。

(1) 如果 $p\mid a$,则 $p$ 与 $a$ 互素；
证：设 $(p,a)=d$,则有 $d\mid p$,因 $p$ 是素数，所以$d=1$ 或 $p.$ 若 $d=p$,则因 $d\mid a.$ 于是 $p\mid a$,与题设 $p\mid\vec{a\text{矛盾}}$,故必 $d=1$, 从而 $(p,a)=1.$

(2) 若 $p|ab$,则 $p|a$ 或 $p|b$。一般地若 $p|a_1a_2\cdots a_k$ ，则必然存在某个 $i$,使得 $p|a$ 成立。
证：若 $p\mid a$ 则结论成立。若 $p\mid a$,则 $p$ 与 $a$ 互素，故有 $p\mid b$。

## 算术基本定理：
任一整数 $n(n>1)$ 都可以表成素数的乘积. 且在不考虑乘积次序的情况下，表达式是唯一的.
即
$$n=p_1p_2\mathscr{L}p_s,\quad p_1\leq p_2\leq\mathscr{L}\leq p_s$$

其中 $p_i$ 是素数，且若

$$n=q_1q_2\mathscr{L}q_t,\quad q_1\leq q_2\leq\mathscr{L}\leq q_t$$
其中 $q_j$ 是素数。则 $s=t,p_i=q_i,1\leq i\leq s$

证明：数学归纳法：
$$\begin{aligned}&\text{证 用数学归纳法证明表达式}\\&n=p_1p_2\cdots p_s,\quad p_1\leq p_2\leq\cdots\leq p_s\quad(1)\\&n=2时,结论显然成立.\\&\text{假设对于小于}n\text{的正整数},\text{结论成立}.\\&\text{对于正整数}n.\text{ 若}n\text{是素数},\text{则式}(1)\text{显然成立}.\\&\text{若}n\text{是合数},\text{则存在正整数}b,c,\text{使得}\\&n=bc,\quad1<b<n,\quad1<c<n\\&\text{由归纳假设,有}\\&b=p_1’p_2’\cdotp\cdotp\cdotp p_a’,\quad c=p_{a+1}’p_{a+2}’\cdotp\cdotp\cdotp p_s’\\&\text{于是}\quad n=bc=p_1’p_2’\cdotp\cdotp\cdotp p_a’p_{a+1}’p_{a+2}’\cdotp\cdotp\cdotp p_s’\\&\text{适当改变}p_i’\text{的次序,即得}(1\text{或}.\end{aligned}$$

##  素数定理：
1. 素数有无限个：
证 (反证法) 假设整数中只有有限个素数，

设为 $p_1p_2\cdots;p_k$ 令
$$n=p_1p_2\cdots p_k+1,$$

则 $n> p_i$ $( i= 1, 2, \cdots , k)$,所以 $n$ 是合数. 于是 $n$ 的大于 $\mathbf{1}$ 的最小正因数 $p$ 是素数，这里 $p=$ 某个 $p_i$ (1≤ $i≤k)$,因此 $p\mid p_1p_2\cdots p_k$, $\Rightarrow p\mid 1$ 这是不可能的. 故素数有无穷多个.（这一个也是显然的，这是**假设我们的一个元素是有限的**，然后通过反证法）

![[Pasted image 20241103120000.png]]

