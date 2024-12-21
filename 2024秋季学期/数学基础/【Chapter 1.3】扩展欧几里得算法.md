例题：
求整数 $x,y$,使得 $gcd(4864,3458)=4864x+3456y$

$$
\begin{align}
38&=114-76 \\
&=114-(646-5*114) \\
&=-646+6*(1406-2*646) \\
&=6*1406-13*(3458-2*1406) \\
&=-13*3458+32(4864-3458) \\
&=32*4864-45*3458
\end{align}
$$

根据我们的上面的结果，我们可以推到下面的一个式子：我们假设我们的 $r_{i}=a_{i}*x+b_{i}*y$,显然，我们只需要求出我们的 $a_{i}$ 和我们的 $b_{i}$ 的系数，就可以得到我们最后的表示是什么。

现在，我们又有我们的递推式：
$r_{i}=r_{i-2}-q_{i}\times r_{i}$,于是，对于我们的 $a_{i},b_{i}$,我们也有下昂痛的推到：
$$
\begin{align}
r_{i}&=a_{i-2}*x+b_{i-2}-q_{i}*(a_{i-1}*x+b_{i-1}) \\
&=(a_{i-2}-q_{i}\times a_{i-1})+b_{i-2}-q_{i}\times b_{i-1}
\end{align}
$$
于四海，我们就可以通过我们的递推公式来求解我们的对应的答案。

```cpp
long long extend_gcd(long long a,long long b,long long &x,long long &y){
    if(b==0){
        x=1;y=0;return a;
    }
    long long d=extend_gcd(b,a%b,y,x);
    y-=a/b*x;
    return d;
}
```