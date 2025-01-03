本文讨论的加密方式是最简单的一种：简单异或。准备一个 key 字符串，然后利用 key 去异或相同长度的明文，即得到密文。如果每个密文都利用新的 key 去加密，那么这种方式称为“一次一密”(One-Time-Pad)。OTP 是无条件安全的：即使攻击者拥有无限的计算资源，都不可能破译 OTP 加密的密文。

然而，一次一密的密钥分发是比较困难的。首先，Alice 想要给 Bob 发送长度为 n 的信息，则必须在这之前传送长度为 n 的密钥，相当于传输的数据总量翻了倍。其次，尽管密文是无条件安全的，但密钥的传输信道未必是安全的，攻击者一旦窃听了密钥，则可以解密密文。

那么马上就可以想到一个投机取巧的方法—— Alice 造一个比较长的密钥，然后用非常秘密的方式告诉 Bob. 接下来，Alice 每次向 Bob 发送信息，都把明文异或上这个约定好的字符串；Bob 收到信息之后，把密文异或上：key ,于是就可以拿到明文。

整个过程只需要传送一次密钥，这是很方便的。这种方式称为 Many-Time-Pad (MTP).很遗憾，上述的 MTP 办法是不安全的。攻击者如果截获了足够多的密文，就有可能推断出明文、进而拿到密钥。这个缺陷是异或运算的性质带来的。

## 统计频率分析：
例如，给定下面的结果：
>25030206463d3d393131555f7f1d061d4052111a19544e2e5d54  
0f020606150f203f307f5c0a7f24070747130e16545000035d54  
1203075429152a7020365c167f390f1013170b1006481e13144e  
0f4610170e1e2235787f7853372c0f065752111b15454e0e0901  
081543000e1e6f3f3a3348533a270d064a02111a1b5f4e0a1855  
0909075412132e247436425332281a1c561f04071d520f0b1158  
4116111b101e2170203011113a69001b47520601155205021901  
041006064612297020375453342c17545a01451811411a470e44  
021311114a5b0335207f7c167f22001b44520c15544801125d40  
06140611460c26243c7f5c167f3d015446010053005907145d44  
0f05110d160f263f3a7f4210372c03111313090415481d49530f


回顾异或运算的性质：结合律、交换律、逆元为其白身。这是非常好的性质，然而也为攻击者提供了方便。因为：
$$C_1\oplus C_2=(M_1\oplus key)\oplus(M_2\oplus key)=M_1\oplus M_2$$
这表明，两个密文的异或，就等于对应明文的异或。这是很危险的性质，高明的攻击者可以通过频率分析，来破译这些密
文。我们来看字符串 $C_{1}$ 异或上其他密文会得到什么东西。以下只保留了英文字符，其余字符以“:”代替。

>....S....N.U.....A..M.N...
> ...Ro..I...I....SE....P.I. .E..H...IN..H...........TU ..A.H.R.....E....P......E. ...RT...E...M....M....A.L. d...V..I..DNEt........K.DU .......I....K..I.ST...TiS. .....f...N.I........M.O... .........N.I...I.S.I..I... ....P....N.OH...SA....Sg..

可以观察到，有些列上有大量的英文字符，有些列一个英文字符都没有。这是偶然现象吗？


我们可以注意到一个至关重要的规律：小写字母 xor 空格，会得到对应的大写字母；大写字母 xor 空格，会得到小写字母！ 所以，如果 $x\oplus y$ 得到一个英文字母，那么 $x,y$ 中的某一个有很大概率是空格。再来回头看上面 $C_{1}$ xor 其他密文—也就等于 $M_1$ xor 其他明文的表，如果第 col 列存在大量的英文字母，我们可以猜测 $M_1[col]$ 是一个空格。那一列英文字母越多，把握越大。

知道 $M_{1}$ 的 col 位是空格有什么用呢？别忘了异或运算下，$x$ 的逆元是其自身。所以
$$M_i[col]=M_1[col]\oplus M_i[col]\oplus M_1[col]=M_1[col]\oplus M_i[col]\oplus0\text{x20}$$

于是，只要知道某个字符串的某一位是空格. 我们就可以恢复出所有明文在这一列的值。

