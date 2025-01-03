>整数分解方案：有效公钥方案基于这样一个事实：因式分解大整数是非常
困难的。这类算法最突出的代表就是 RSA。

>离散对数方案：有不少算法都基于有限域内的离散对数问题，最典型的例
子包括 Diffie-Hellman 密钥交换、Elgamal 加密或数字签名算法 (DSA)。ElGamal

>椭圆曲线 (EC)方案：离散对数算法的一个推广就是椭圆曲线公钥方案。典型例子包括椭圆曲线 Diffie-Hellman 密钥交换 (ECDH)和椭圆曲线数字签名算法
$(\mathbf{ECDSA})$。



<table>
	<tbody>
		<tr>
			<th rowspan="2">算法家族</th>
			<th rowspan="2">密码体制</th>
			<th colspan="4">安全级别 (位)</th>
		</tr>
		<tr>
			<th>80</th>
			<th>128</th>
			<th>192</th>
			<th>256</th>
		</tr>
		<tr>
			<td>整数分解</td>
			<td>$RSA$</td>
			<td>1024 位</td>
			<td>3072 位</td>
			<td>7680 位</td>
			<td>0 位 15360 T</td>
		</tr>
		<tr>
			<td>离散对数</td>
			<td>DH、DSA、 Elgamal</td>
			<td>1024 位</td>
			<td>$3072 位$</td>
			<td>7680 位</td>
			<td>15360 位</td>
		</tr>
		<tr>
			<td>椭圆曲线</td>
			<td>ECDH、ECDSA</td>
			<td>160 位</td>
			<td>$256 位$</td>
			<td>$384 位$</td>
			<td>512 位</td>
		</tr>
		<tr>
			<td>对称密钥</td>
			<td>AES、3 DES</td>
			<td>80 位</td>
			<td>128 位</td>
			<td>192 位</td>
			<td>$256 位$</td>
		</tr>
	</tbody>
</table>
