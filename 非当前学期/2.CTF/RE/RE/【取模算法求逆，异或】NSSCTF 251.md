我们拿到一个 pyc 文件，首先我们的第一步，就是把我们的文件转换到我们的 python 文件，这里我们运用到我们的在线反编译网站：
```text
https://tool.lu/pyc/
```

我们在这里我们能得到我们的答案如下：
```python
#!/usr/bin/env python

# visit https://tool.lu/pyc/ for more information

# Version: Python 2.7

  

print 'Welcome to Re World!'

print 'Your input1 is your flag~'

l = len(input1)

for i in range(l):

    num = ((input1[i] + i) % 128 + 128) % 128

    code += num

  

# for i in range(l - 1):

#     code[i] = code[i] ^ code[i + 1]

  
  

code = [

    '%1f',

    '%12',

    '%1d',

    '(',

    '0',

    '4',

    '%01',

    '%06',

    '%14',

    '4',

    ',',

    '%1b',

    'U',

    '?',

    'o',

    '6',

    '*',

    ':',

    '%01',

    'D',

    ';',

    '%',

    '%13']

l=len(code);

for i in range(l-1):

    code[i]=code[i]^code[i+1]

  

for i in range(l):

    num=code[i]

    input1+=((num-i)%128+128)%128

  

print(input1)
```

这里，对于我们的第一个部分的求逆，我们可以很简单的求出来：
```cpp
for(int i=sizeof(code)-2;i>=0;i--){
code[i]=code[i]^code[i+1];
}
```

然后，因为我们后面的是我们的一个取模运算，一般来说我们是不能直接得到的。但是，因为我们这里有一个隐形条件，就是我们的字符必须都是可见字符，因此，我们可以考虑枚举我们的模数 k，然后就能得到哦我们的答案。
```cpp
for(int i=0;i<sizeof(code);i++){
	for(int j=0;j<3;j++){
		int tmp=code[i]-i+j*128;
		if(tmp>=33&&tmp<=127){
			cout<<tmp<<endl;
			break;
		}
	}
}
```