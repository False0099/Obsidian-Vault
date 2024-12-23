## 密钥生成：
$\bullet$ 选取两个 n 比特的素数 p，q
$\cdot$ 令 $\mathbf{N}=\mathbf{p}\cdot\mathbf{q}$,
$\bullet$ 计算 $\varphi(N)=(p-1)(q-1)$
· 选取与 $\varphi(N)$ 互素的正整数 e，
·求正整数 d，使得 $ed\equiv 1($ mod $\varphi ( N) )$
·公共参数是 N，公钥是 e，私钥是 d


## 签名：
$\bullet$ 消息 $m\in Z_N^*$
$\bullet$ 签名 $s=m^d$ mod $N$
$\cdot$ 将签名附在消息后，即 (m||s)

## 验证：
消息及签名 $(m\parallel s)$

计算 $m^\prime=s^e$ mod $N$,再比较 m'和 m，若相等，则验证通过；否则，验证不通过


