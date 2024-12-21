思路：我们发现，我们会对我们的 base64 编码表进行 48 次置换后，然后再用我们置换的表格来作为我们的 key 来修改进行我们的 base64 编码。

于是，我们的思路一：先求出我们的对应的真实的编码表：
或者，我们也可以通过我们的动态调试来得到我们的最终答案。
```cpp
#include<string.h>
int main()
{
	int v3[65] = { 0 };
	char Source[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";
	char Destination[65] = { 0 };
	v3[0] = 16;
	v3[1] = 34;
	v3[2] = 56;
	v3[3] = 7;
	v3[4] = 46;
	v3[5] = 2;
	v3[6] = 10;
	v3[7] = 44;
	v3[8] = 20;
	v3[9] = 41;
	v3[10] = 59;
	v3[11] = 31;
	v3[12] = 51;
	v3[13] = 60;
	v3[14] = 61;
	v3[15] = 26;
	v3[16] = 5;
	v3[17] = 40;
	v3[18] = 21;
	v3[19] = 38;
	v3[20] = 4;
	v3[21] = 54;
	v3[22] = 52;
	v3[23] = 47;
	v3[24] = 3;
	v3[25] = 11;
	v3[26] = 58;
	v3[27] = 48;
	v3[28] = 32;
	v3[29] = 15;
	v3[30] = 49;
	v3[31] = 14;
	v3[32] = 37;
	v3[34] = 55;
	v3[35] = 53;
	v3[36] = 24;
	v3[37] = 35;
	v3[38] = 18;
	v3[39] = 25;
	v3[40] = 33;
	v3[41] = 43;
	v3[42] = 50;
	v3[43] = 39;
	v3[44] = 12;
	v3[45] = 19;
	v3[46] = 13;
	v3[47] = 42;
	v3[48] = 9;
	v3[49] = 17;
	v3[50] = 28;
	v3[51] = 30;
	v3[52] = 23;
	v3[53] = 36;
	v3[54] = 1;
	v3[55] = 22;
	v3[56] = 57;
	v3[57] = 63;
	v3[58] = 8;
	v3[59] = 27;
	v3[60] = 6;
	v3[61] = 62;
	v3[62] = 45;
	v3[63] = 29;
	strcpy(Destination, Source);
	for (int i = 0; i <= 47; ++i)
	{
		for (int j = 0; j <= 63; ++j)
			Source[j] = Destination[v3[j]];
		strcpy(Destination, Source);
	}
	printf("%s", Destination);
}
```

之后，我们可以用两种方法：
第一种是利用我们的逆算法自行编写后得到答案：
```python
a = input(“输入（1.base64加密/2.base64解密）：”)  
base64 = ‘gJ1BRjQie/FIWhEslq7GxbnL26M4+HXUtcpmVTKaydOP38of5v90ZSwrkYzCAuND’  
if a == ‘1’:  
s = input(“\n输入待加密字符串：”)  
t1 = ‘’  
d = 0  
for a in s:  
t1 += ‘0’_(10 - len(str(bin(ord(a)))))+str(bin(ord(a)))[2:] # 补足8位  
if len(t1) % 6 == 2:  
d = 2  
t1 += ‘0000’  
elif len(t1) % 6 == 4:  
d = 1  
t1 += ‘00’  
i = 0  
t2 = ‘’  
while i < len(t1):  
t2 += base64[int(t1[i:i+6],2)]  
i += 6  
if d == 2:  
t2 += ‘==’  
elif d == 1:  
t2 += ‘=’  
print(“\n加密结果：%s”%t2)  
elif a == ‘2’:  
s = input(“\n输入待解密base64码：”)  
d = 0  
if s[-1] == ‘=’ and s[-2] != ‘=’:  
d = 1  
s = s[:-1]  
elif s[-1] == ‘=’ and s[-2] == ‘=’:  
d = 2  
s = s[:-2]  
t1 = ‘’  
for a in s:  
t1 += ‘0’_(8 - len(str(bin(base64.index(a)))))+str(bin(base64.index(a)))[2:] # 补足6位  
if d == 1:  
t1 = t1[:-2]  
elif d == 2:  
t1 = t1[:-4]  
i = 0  
t2 = ‘’  
while i < len(t1):  
t2 += chr(int(t1[i:i+8],2))  
i += 8  
print(“\n解密结果：%s”%t2)
```

第二种是利用相关的网站进行解码：
![[Pasted image 20240209231215.png]]