例题 2.46 求极限：$\lim_{n\to\infty}\frac{\sum_{k=0}^n\ln C_n^k}{n^2}$


$\operatorname*{lim}_{n\to\infty}\frac{\sum_{k=0}^{n}\ln C_{n}^{k}}{n^{2}}\stackrel{Stolz}{\operatorname*{=}}\operatorname*{lim}_{n\to\infty}\frac{k=0}{\operatorname*{lim}_{n\to\infty}\frac{k=0}{(n+1)^{2}-n^{2}}}$
$=\operatorname*{lim}_{n\to\infty}\frac{\sum_{k=0}^{n}\ln\frac{C_{n+1}^{k}}{C_{n}^{k}}-\ln C_{n+1}^{n+1}}{2n+1}=\operatorname*{lim}_{n\to\infty}\frac{\sum_{k=0}^{n}\ln\frac{n+1}{n-k+1}}{2n+1}$
$=\operatorname*{lim}_{n\to\infty}\frac{(n+1)\ln(n+1)-\sum_{k=0}^{n+1}\ln k}{2n+1}$
${\frac{Stolz}{=}}\operatorname*{lim}_{n\to\infty}{\frac{(n+1)\ln(n+1)-n\ln n-\ln(n+1)}{(2n+1)-(2n-1)}}$
$=\operatorname*{lim}_{n\to\infty}\frac{\ln\left(\frac{n+1}{n}\right)^{n}}{2}=\frac{1}{2}$
