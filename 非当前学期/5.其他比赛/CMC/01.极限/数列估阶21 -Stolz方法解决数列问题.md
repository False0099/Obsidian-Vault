
$$a_1=1,a_{n+1}=a_n+\frac{1}{S_n},S_n=\sum_{k=1}^{n}a_k,\:\text{计算}\lim_{n\to\infty}\frac{a_n}{\sqrt{\ln n}}$$

我们对于我们的**抽象数列进行我们的估计的时候，我们如果进行不下去的时候，我们不妨考虑用 stolz**来进行我们的分子分母降解。

$$\begin{aligned}
&a_{n+1}-a_{n}>0,\text{所以}a_{n}\geq1,\:S_{n}\geq n \\
&\text{假定}\operatorname*{lim}_{n\to\infty}a_{n}=A,\text{故}a_{n}\leq A,S_{n}\leq An \\
&a_{n+1}=a_{n}+\frac{1}{S_{n}}\geq a_{n}+\frac{1}{An}\:,a_{n+1}-a_{1}\geq\frac{1}{A}\sum_{k=1}^{n}\frac{1}{k}\rightarrow+\infty  \\
&\text{矛盾,故}\lim_{n\to\infty}a_{n}=+\infty, \\
&\lim_{n\to\infty}\frac{a_{n}^{2}}{\ln n}=\lim_{n\to\infty}\frac{a_{n+1}^{2}-a_{n}^{2}}{\frac1n}=\lim_{n\to\infty}n\left(a_{n+1}^{2}-a_{n}^{2}\right) \\
&\frac{a_{n+1}}{a_{n}}=1+\frac{1}{a_{n}S_{n}}\rightarrow1,\text{故上式} \\
&=\lim_{n\to\infty}n\frac{a_{n+1}+a_{n}}{S_{n}}=2\lim_{n\to\infty}n\:\frac{a_{n}}{S_{n}}=2\lim_{n\to\infty}\frac{\left(n+1\right)a_{n+1}-na_{n}}{a_{n+1}} \\
&=2\lim_{n\to\infty}\Bigg(n+1-\frac{na_{n}}{a_{n+1}}\Bigg)=2+\lim_{n\to\infty}n\Bigg(1-\frac{a_{n}}{a_{n+1}}\Bigg) \\
&n(1-\frac{a_{n}}{a_{n+1}})=n(1-\frac{1}{1+\frac{1}{a_{n}S_{n}}})=\frac{\frac{n}{a_{n}S_{n}}}{1+\frac{1}{a_{n}S_{n}}}=\frac{n}{1+a_{n}S_{n}}\leq\frac{n}{1+na_{n}} \\
&\text{故}\lim_{n\to\infty}n\Bigg(1-{\frac{a_{n}}{a_{n+1}}}\Bigg)\leq\lim_{n\to\infty}{\frac{n}{1+na_{n}}}=\lim_{n\to\infty}{\frac{1}{\frac{1}{n}+a_{n}}}=0 \\
&\text{故}\operatorname*{lim}_{n\to\infty}\frac{a_{n}}{\sqrt{\ln n}}=\sqrt{2}
\end{aligned}$$
