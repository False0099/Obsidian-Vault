题目：
```cpp
flag = 'xxxxxxxxxxxxxxxxxxx'

  

s_box = 'qwertyuiopasdfghjkzxcvb123456#$'

tmp = ''

for i in flag:

    tmp += str(bin(ord(i)))[2:].zfill(8) #把字符串转换为8位二进制字符表示

b1 = int(tmp,2) #将我们的tmp转换为二进制表示

s = ''

while b1//31 != 0:

    s += s_box[b1%31]

    b1 = b1//31

  

print(s)

  

# s = u#k4ggia61egegzjuqz12jhfspfkay
```

我们本题的思路是：把我们的原来的字符串按位转换为我们的 32 进制后，再把我们的字符串转换为 31 进制，并且用一个 s_box 对应，同时，我们舍弃我们的最高位。

这里，我们需要枚举我们的最高位数字是多少，这里，我们的范围需要满足是我们的可见字符，因此需要我们的范围是 $0-5$。

因此，我们的解密函数就是：
```python
for i in range(31):
	result1=i;
	for i in range(len(s)):
		temp=s_box.index(s[len-i-1])
		result1=result1*31+temp
	print(result1)
```

