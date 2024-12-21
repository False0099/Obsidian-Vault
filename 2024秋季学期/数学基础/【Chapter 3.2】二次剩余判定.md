## 二次剩余方程：
![[Pasted image 20241103152806.png]]

![[Pasted image 20241103152813.png]]

![[Pasted image 20241103152816.png]]


因此，只需要考虑模为素数的方幂 $p^\alpha$ 的同余方程： (3.8) $$ax^2+bx+c\equiv0({\mathrm{mod}}-p^\alpha),\quad p\mid a$$

## 定义：

定义 3.2.1 设 $m$ 为正整数，若同余式

$$x^2\equiv a({\mathrm{mod}}\quad m),\quad\gcd(a,m)=1$$
有解，则称 $a$ 为模 m 的二次剩余，否则称 $a$ 为模 $m$

## 解的判定：
首先，我们要判断我们的一个二次剩余是否有解。并不是对于所有的 $(a,p)$,我们的二次剩余都是可以解的。我们对于我们的一个 $p$,我们纪录对应有解的 a 为我们的二次剩余的集合 $(\mathbb{Z}_{n}^{*})^{2}$。

在不同的 $n$ 当中，我们的**集合的性质是不同的**，一般来说，它会根据我们的 p 是奇素数还是和数，有不同的性质。

### 奇素数
在这种情况下，我们对于我们的集合中的元素，我们有：
1. $b\in Z_{p}$,那么我们就存在：$b^{2}=1\implies b=\pm1$
2. 设 $b,c\in\mathbb{Z}_p^*$,则
$$b^2=c^2\Longleftrightarrow b=\pm c$$
也就是说，如果 $p$ 是一个奇素数，那么我们的二次剩余集合中的元素的大小应该满足数量为完全剩余系大小的一半。$$\begin{aligned}&\text{定理 3.2.1在模}p\text{的一个既约剩余系中,恰有}\frac{p-1}2\text{个模}p\text{的}\\&\text{二次剩余,}\frac{p-1}2\text{个模}p\text{的二次非剩余。}\end{aligned}$$
#### 欧拉准则：
$$\begin{aligned}&\text{设 }p\text{是奇素数,}a\in Z_p^*,\text{ 则}\\&\textcircled{1}a^{\frac{p-1}2}=\pm1\\&\textcircled{2}a\in\left(Z_p^*\right)^2\Rightarrow a^{\frac{p-1}2}=1\\&\textcircled{3}a\notin\left(Z_p^*\right)^2\Rightarrow a^{\frac{p-1}2}=-1\end{aligned}$$

欧拉准则给定了我们怎么判断一个数字 $a$ 是否在我们的二次剩余中。如果 $a\in(\mathbb{Z}_p^*)^2$,则存在 $b\in\mathbb{Z}_p^*$,使得 $a=b^2$。则
$$a^{\frac{p-1}2}=b^{p-1}=b^{\phi(p)}=1$$
我们两个数相乘是否是二次剩余，类似于我们的 1 和-1 相乘得到的结果。
$$a\in\left(Z_p^*\right)^2\xrightarrow{ab\in\left(Z_p^*\right)^2}b\in\left(Z_p^*\right)^2$$

### 模 $p^{k}$ 下的结果
假设我们的数字是一个奇素数，$k$ 是一个正整数，那么我们
$$\text{设 }b\in Z_{p^k}\text{,则}\\b^2=1\Leftrightarrow b=\pm1$$

性质 2：
$$\text{设 }b,c\in Z_{p^k}^*,\text{ 则}\\b^2=c^2\iff b=\pm c\\\left|(Z_{p^k}^*)^2\right|=\phi(p^k)/2$$

性质 3：
设 $p$ 是奇素数，$a\in Z_p^*$,则
$\phi(p^k)$
$$2\quad a\in\left(z_{p^k}^*\right)^2\Rightarrow a^{\frac{\phi(p^k)}2}=13\quad a\notin\left(z_{p^k}^*\right)^2\Rightarrow a^{\frac{\phi(p^k)}2}=-1$$

性质 4：
$$a\text{ 是模}p^k\text{下二次剩余}\Leftrightarrow a\text{ 是模}p\text{下二次剩余}$$



## 勒让德符号：
定义 3.2.2 设 $p$ 是奇素数，勒让德符号 $\left(\frac ap\right)$ 定义如下

$\left(\frac ap\right)=\begin{cases}1,&\text{若}a\text{是模 }p\text{ 的二次剩余；}\\-1,&\text{若}a\text{是模 }p\text{ 的二次非剩余；}\\0,&\text{若}p|a.&&\end{cases}$

由定义可知，同余式
$$x^2\equiv a\mathrm{~(mod~}p)\text{ 有解 }\Leftrightarrow\left(\frac ap\right)=1.$$

定理 ：
(欧拉判别法)设 $p$ 是奇素数，则对任意整数 $a$,

$$\left(\frac ap\right)\equiv a^{\frac{p-1}2}(\operatorname{mod}p)$$

推论 1：
设 $p$ 是奇素数，则
(1) $\left ( \frac 1p\right ) = 1;$ 
(2) $\left ( \frac {- 1}p\right ) = \left ( - 1\right ) ^{\frac {p- 1}2}$

定理 3.2.3 设 $p$ 是奇素数，则
$( \mathbf{i} )$ $\left ( \frac {a+ p}p\right ) = \left ( \frac ap\right ) ;$


(ii) $\left ( \dfrac {ab}p\right ) = \left ( \dfrac ap\right ) \left ( \dfrac bp\right ) ;$ 扩展 $\left(\frac{a_1a_2\cdots a_n}p\right)=\prod_{i=1}^n\left(\frac{a_i}p\right)$


(iii) 设 $(a,p)=1$,则 $\left(\frac{a^2}p\right)=1$

 定理 3.2.5 设 $p$ 是奇素数 ,则 (i) $\left ( \frac 2p\right ) = ( - 1) ^{\frac {p^2-1 }8}$。

定理 3.2.6 (二次互反律*) 若 $p$ 及 $q$ 是互素的不同奇素数，则
$$\left(\frac qp\right)=(-1)^{\frac{p-1}2\cdot\frac{q-1}2}\left(\frac pq\right)$$$$\left(\frac qp\right)\left(\frac pq\right)=(-1)^{\frac{p-1}2\cdot\frac{q-1}2}$$

运用：快速判断我们的一个二次剩余方程是否是有解的。

例如：因为 227 是素数，根据定理 3.2.6 有
$$\begin{aligned}\left(\frac{7}{227}\right)&=\quad(-1)^{\frac{7-1}{2}\cdot\frac{227-1}{2}}\left(\frac{227}{7}\right)\\&=\quad(-1)\left(\frac{3}{7}\right)\\&=\quad(-1)(-1)^{\frac{3-1}{2}\cdot\frac{7-1}{2}}\left(\frac{1}{3}\right)\\&=\quad\left(\frac{1}{3}\right)\\&=\quad1\end{aligned}$$

![[Pasted image 20241103160712.png]]

![[Pasted image 20241103160735.png]]

