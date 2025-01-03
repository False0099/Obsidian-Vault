**ECC 是 Elgamal 在椭圆曲线上的模拟**.

在密码学中使用的椭圆曲线并不是指传统几何意义上的椭圆，而是满足特定方程形式的代数曲线。通常在有限域 $\mathbb{F}_p$ (这里 $p$ 是一个素数) 上考虑的椭圆曲线满足以下方程 (Weierstrass 标准形式):

$$y^2=x^3+ax+b\pmod{p}$$

其中 $a,b\in\mathbb{F}_p$ 且曲线没有奇异点 (无尖点或自交)。

在这条曲线上定义了一种"加法"运算：给定曲线上的两点 $P$ 和 $Q$,可以通过几何构造或对应的代数计算规则获得第三个点 $R=P+Q$ 仍然在曲线上。这种加法满足交换律、结合律，并存在加法单位元 (即"无穷远点")与逆元等，使得曲线上有理点 (包括无穷远点)构成一个代数群。

因为可以在椭圆曲线上构造“加法”。在椭圆曲线 $E$ 上取两点 $P$ 和 $Q$ ,连接 $PQ$ 交 $E$ 于 $R$ ,与 $R$ 有相同横坐标的点记为 $R^{^{\prime}}$ 。记这种运算为 $P\oplus Q=R^{^{\prime}}$。如果是过 $P$ 做切线，记为 $P\oplus P= 2P= R^{^{\prime }}$ .

![[Pasted image 20241216091856.png]]


![[Pasted image 20241216092319.png]]

还有一种特殊情况，当这条直线垂直于 x 轴时，其与椭圆曲线交点数目是少于 3 个的，此时引入一个元素 $\mathcal{O}$ 。这个 $\mathcal{O}$ 就是这种加法定义里的零元素。 $\mathcal{O}\oplus P=P\oplus\mathcal{O}=P$ 。

## 有限域上椭圆曲线：
对于奇素数 $^{+}\mathfrak{p}$ 与椭圆曲线 $E:Y^2=X^3+AX+B,(A\in F_p,B\in F_p,4A^3+27B\neq0)$
,定义在 $F_p$ 上的点集全体即为 $\left\{(x,y)|x,y\in F_p,y^2=x^3+Ax+B\right\}\cup\left\{\mathcal{O}\right\}.$

$4A^3+27B$ 是一元三次方程 $^{*}$ 的判别式，令其不为 0 是为了防止曲线出现奇异点。有关判别式的理论可查阅” 三次方程 “相关条目，这种方程的判别式研究早在文艺复兴时就已经出现。

易于验证表格内点与椭圆曲线 $F_{13}$ 上的加法构成一个有限交换群。

接下来考虑在模 p 意义下会出现多少这样的点。从理论中我们得知 p 的完全剩余系“中应有一半有二次剩余，每个有二次剩余的数对应两个点，再加上零点，所以应该有 p+1 个点。但是改变椭圆曲线的系数可能正好出现 Y 被整除的情况，此时点的数目就会存在一个波动。Hasse 给出了一个范围估计，此处不加证明直接列出：

记点的个数为 $\#(F_p)$ , $\#(F_p)=p+1-t_p,|t_p|\leq2\sqrt{p}$ 。$t_p$ 被称作 $E/F_p$ 的弗罗比尼乌斯迹 (Frobenius trace)。

## 密钥生成：
•随机选取(1, p-1)中整数的 d，计算 P = d∙G。，其中 $G$ 表示我们的基点。
•d 是私钥，P 是公钥。


## 信息加密：
对于明文  $m= ( m_{1}, m_{2}) \in \mathcal{F} _{p}^{* }\times \mathcal{F} _{p}^{* }$, 计算数乘 kP $= ( \mathbf{x} , \mathbf{y} )$,随机选取整数  $k\left ( 1\leq k\leq q- 1\right )$

计算 $C_0=kG=(x,y)$,

$$
\begin{align}
c_1=m_1x\bmod p
\\c_2=m_2y\bmod p
\end{align}
$$


得到密文 $c=(C_0,c_1,c_2)$

## 信息解密：
对于密文 $c=(C_{o},c_{1},c_{2})\in E(\mathcal{F}_{p})\times\mathcal{F}_{p}^{*}\times\mathcal{F}_{p}^{*}$,先计算 $dC_0=(x,y)$

再计算 $m_1= c_1x^{- 1}\mathrm{mod}p$ $m_2=c_2y^{-1}\bmod p$,

得到解密后的明文为 $m=(m_1,m_2)$


## 密码 DH 系统：
Alice 和 Bob 共同选定 $E(F_p)$ 与一个起始点 $P\in E(F_p)$ ,同时 Alice 选定自己的私钥 $n_A$ ,Bob 选定自己的私钥 $n_B$ ,之后 Alice 计算 $Q_A=n_AP$ , Bob 计算 $Q_B=n_BP$ 。两人交换
$Q_A,Q_B$ 再分别计算 $n_BQ_A$ 与 $n_AQ_B$ 。

如果 Eve 想破解 Alice 和 Bob 的问题，她可以去解决 $Q_A=n_AP$ 这种 ECDLP 问题，或者她可以直接利用 Diffie-Hellman 密码系统的漏洞绕开 DLP 问题。很不幸，这种漏洞是该密码系统的痼疾，并未随着数域改变而消失。这就是解决 Diffie-Hellman 问题 (DHP)与 DLP 不等价。在 RSA 中，DHF 表示为已知 $g^a(mod\left.p\right),g^b(mod\left.p\right)$ 求 $g^{ab}(mod\left.p\right)$ 。在这里，ECDHP 表示为已知 $n_AP,n_BP$ 求 $n_An_BP$ 。解决 DLP 肯定可以解决 DHP，但解决了 DHP 未必能解决 DLP。

## EH 系统：
Alice 和 Bob 共同选定 $E(F_p)$ 与一个起始点 $P\in E(F_p)$ ,同时 Alice 选定自己的私钥 $n_A$ ,Bob 选定自己的密文 $M$ ,之后 Alice 将 $Q_A=n_AP$ 当作公钥发布，Bob 再随便选一个数 $k$ 并计算 $C_1= kP$, $C_2= M+ kQ_A$ 。之后 Bob 将 $(C_1,C_2)$ 发给 Alice，Alice 计算 $C_2-n_AC_1=M+kQ_A-n_A(kP)=M+k(n_AP)-n_A(kP)=M$ 得出 Bob 传递的信息。

为了提升安全性，Bob 还可以只送出 x 的坐标，但是由于 Alice 要进行坐标轴的计算，所以 Bob 需要再添加一个信息来表示 y 的符号。例如当 y 小于 $\frac p2$ 时用 O，另一种情况用1。
