例题  2. 7 设 $\lim _{n\to \infty }x_n= a.$ 求证 :  $\lim _{n\to \infty }\frac {x_1+ 2x_2+ \cdots + nx_n}{n^2}= \frac {a^2}2$

我们对于本题来说，我们的最直观的方法，一定是采用我们的**Stolz 方法**，来进行我们的处理。而我们的**证明**，则需要通过我们的严格的数学语言。

解：
我们有
$$x_n=a+a_n$$
这里 $\{a_n\}$ 是无穷小数列. 于是

因为
$$\begin{aligned}\text{对. J 定}\\\frac{x_1+2x_2+\cdots+nx_n}{n^2}&=\frac{(a+a_1)+2(a+a_2)+\cdots+n(a+a_n)}{n^2}\\&=\frac{n+1}{2n}a+\frac{\frac1na_1+\frac2na_2+\cdots+\frac nna_n}n\end{aligned}$$
$$\left|\frac{\frac1na_1+\frac2na_2+\cdots+\frac nna_n}n\right|\leqslant\frac{|a_1|+|a_2|+\cdots+|a_n|}n$$
$$\begin{aligned}\lim_{n\to\infty}\frac{x_1+2x_2+\cdots+nx_n}{n^2}&=\frac{n+1}{2n}a+\frac{\frac1na_1+\frac2na_2+\cdots+\frac nna_n}n\\&=\frac a2+0=\frac a2\end{aligned}$$
所以