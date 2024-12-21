## 保护介绍：
Canary 保护，就是在我们的栈中 old_rbp 后生成一个随机数，如果我们发现这个位置发生了更改，那么我们的程序就会直接退出。
![[Pasted image 20240117163719.png]]

而我们的 Canary 保护有下面的两大特点：
1. 长度一定为 8 个字节，并且是随机生成的
2. 末尾一定是 `/x00`，以防止我们直接读出。

## 绕过方式：
### 方法一：
我们通过我们的 `print` 函数来获得我们的 `canary` 的数据，然后再用我们这一个数据去构造我们的 payload。

原理在于，我们给定一个地址后，我们如果 print 参数是 `%s`,那么我们就会从我们的地址开始一直向后打印，直到遇到我们的 `\x00`



## 格式化字符串直接打印
前提：我们的函数当中存在我们的 printf，也就是我们可以利用我们的格式化字符串漏洞。

第一步：通过我们的第一个栈溢出来泄露出我们的 canary。这里，我们首先要在我们的 ida 中找到我们的 canary 的位置是哪里，我们才能得到我们的准确的结果。

这里，需要我们找到存在缓冲区溢出漏洞的函数的 canary 值地址。为此，我们就需要把我们的原来的 `canary` 的末尾的 `\x00`,这里，我们只需要简单的通过我们的栈溢出就可以做到。于是，之后，我们就可以通过我们的 printf 得到我们的数据
```python
payload1=b'a'*9
p.sendline(payload);
p.recvuntil('a'*9)
canary=u32(p.recv(7).ljust(8,b'/x00'))

```

## 修改 got 表绕过法
我们 Canary 检测我们的是否被修改的一个依据是，通过我们的一个 `_stack_chk_fai` 来进行的。而我们可以考虑修改我们的 `_stack_chk_fai` 的 got 表地址，来绕过我们的对应的函数。
这里，我们需要的前置条件是：存在一个能够修改任意地址值的函数。
![[Pasted image 20240120154520.png]]

我们一般来说可以把我们的函数修改为我们的 `return`，也就是不执行任何操作。


## 堆溢出覆盖 Canary 副本（TLS 溢出修改 canary）

在我们正常的进程中，我们的 Canary 是是不会放在我们的栈当中的，我们的 Canary 会被存放在一个所谓的 TLS 结构当中，而在我们的**多线程**中，我们的 TLS 结构结构会被放在我们的栈当中。于是，如果我们使用了不安全的多线程，我们的 **Canary 原始数值就会被存放在我们的栈地址当中**。


### 3.2.1 方法简介

我们知道，当我们需要创建一个新线程时，需要为该线程指定一个 **线程函数** 。一旦某线程被启动，那么该线程就会执行对应的 **线程函数** 。然而，对于通过 **pthread_create** 函数创建的线程，Glibc 在实现时，会将 **线程函数** 的 Canary 存放在其栈帧的某个高地址处。这就意味着，如果 **线程函数** 本身存在一个足够大的缓冲区溢出（或其他能允许我们修改 Canary 的漏洞），我们就可以覆盖原始的 Canary 值，这一个值被我们存放在我们的栈当中。实现绕过。这一过程的示意图如下图所示：

![](https://pic1.zhimg.com/80/v2-0afb873ce7df235d8f15c1c9e0e88ff4_720w.webp)

从图中可以看到， **线程函数** 栈帧的高地址某处保存着 **原始 Canary** ，且其中存储了一个可溢出的局部变量 **overflow_variable** 。如果 **overflow_variable** 能够溢出的长度足以修改 **原始 Canary** 所在的内存空间，那么我们就可以将栈帧中的 **副本 Canary** 和 **原始 Canary** 设为完全相同的值（例如图中的 **b"\x 11" * 8** ），进而实现绕过。


接下来，我们构造的 payload 就需要满足下面的功能：
1. 把 start 函数的返回地址修改为我们的后门函数的地址
2. 覆盖 start 函数栈上的副本 canary 和位于某处的原始的 canary
为了构造上述 **Payload** ，首先需要理清 **start** 函数的栈帧结构。使用 GDB 调试程序，依次输入 **"8"** 和 **"AAAAAAAA"** 后，观察到的栈帧结构如下：

![](https://pic3.zhimg.com/80/v2-ab32e2b5961d53bb7d2f3b13684becee_720w.webp)

其中，有几个关键的地址需要留意：

1. 0 x 7 ffff 7 d 99 ee 0：存放输入的首地址。
2. 0 x 7 ffff 7 d 9 aee 8：存放 **副本 Canary** 的地址。
3. 0 x 7 ffff 7 d 9 aef 8：存放返回地址的地址。

我们通过阅读源码可以知道，我们最多能够输入 **0 x 10000** 字节的数据。也就是说，地址在区间 **[0 x 7 ffff 7 d 99 ee 0**, **0 x 7 ffff 7 da 9 ee 0]** 的内存空间是由我们完全控制的。如果 **原始 Canary** 恰好保存在上述地址空间中，我们就可以将其覆盖成任意数据。

那么，此时就出现了一个问题：我们该如何确定 **原始 Canary** 的具体地址呢？

我们知道，正常情况下 **副本 Canary** 和 **原始 Canary** 是完全相同的。而我们通过 GDB 可以知道， **副本 Canary** 的取值为：0 x 3399087 bb 2 fcea 00。所以，我们只需搜索 **[0 x 7 ffff 7 d 99 ee 0**, **0 x 7 ffff 7 da 9 ee 0]** 中所有保存了 0 x 3399087 bb 2 fcea 00 的内存空间即可。我们可以利用以下命令完成这一任务：

```text
find 0x7ffff7d99ee0, 0x7ffff7da9ee0, 0x3399087bb2fcea00
```

运行上述命令后，GDB 给出了四个地址：

![](https://pic3.zhimg.com/80/v2-7f64b2aa227cca89ff3b004244541e1a_720w.webp)

这四个地址中，0 x 7 ffff 7 d 9 aee 8 是 **副本 Canary** 的地址；另外三个地址中，其中一个是 **原始 Canary** 的地址。事实上，我们不需要知道到底哪个地址是 **原始 Canary** 的地址，我们只需将上述所有地址都覆盖为同一个值即可。

为了能更方便地构造 **Payload** ，在 **不考虑 Canary** 的情况下构造一个 **原始 Payload** ；随后，再修改 **原始 Payload** 中的特定偏移，完成覆盖。如本例中，我们需要修改原始 Payload 中的4个偏移：

1. 0 x 7 ffff 7 d 9 aee 8 - 0 x 7 ffff 7 d 99 ee 0 = 0 x 1008。
2. 0 x 7 ffff 7 d 9 af 98 - 0 x 7 ffff 7 d 99 ee 0 = 0 x 10 b 8。
3. 0 x 7 ffff 7 d 9 b 728 - 0 x 7 ffff 7 d 99 ee 0 = 0 x 1848。
4. 0 x 7 ffff 7 d 9 c 768 - 0 x 7 ffff 7 d 99 ee 0 = 0 x 2888。

我们只需将 **原始 Payload** 中以上述偏移为首字节的连续 8 个字节覆盖为相同的数据，即可完成 **Canary** 的绕过。完成这一功能的代码如下：

```python
# 在本例中，offsets = [0x1008, 0x10b8, 0x1848, 0x2888]
def replace_canary(offsets, origin_payload): 
    canary = b'\x11\x11\x11\x11\x11\x11\x11\x11'    # 这里的Canary可以是任意长度为8的字节数组

    result = bytearray(origin_payload)    # 由于Python中的字节数组不能直接修改，故将其转化为bytearray对象再做修改

    # 将片段result[offset: offset + 8]修改为canary
    for offset in offsets:
        for i in range(len(canary)):
            result[offset + i] = canary[i]

    return bytes(result)
```



步骤一：
找到我们的溢出初始地址和我们的 `fs_base` 的地址，对于我们的溢出初始地址，我们可以通过打断点找我们的栈的起始地址在哪里，
![[Pasted image 20240120170145.png]]
然后再通过我们的
```
i r fs_base
```
就能得到我们的最后的真正的地址在哪里。之后，我们把两个地址相见，就能得到我们需要构造的 payload 的长度是多少。

步骤二：
撰写我们的 payload
```python
payload=p64(0)*5+p64(0x1234)+b'a'*8//构造我们的溢出
payload+=p64(0x4012b8)//构造我们的shellcode或者rop链
payload=payload.ljust(a*(0x848),b'\x00')+p64(0x1234)//修改我们的canary的值。
```

步骤三:
如果我们要通过我们的 ret 2 libc 来进行我们的 hack 的的话，我们这个时候，还需要注意的问题是：如果我们吧所有的东西都给覆盖了，那么我们会产生错误，我们就不能直接用我们的 `elf.get` 去获得，我们需要静态获得我们的地址
```
payload=b'a'*0x28+p64(0x1234)+p64(0)
payload+=p64(rdi)+p64(0x404020)(这个是我们的puts函数在got表中的地址)+p64(0x4010c0)(这个是puts函数在plt表中的地址)+p64(elf.sym['vul'])
payload=payload.ljust(0x868,b'\x00')+p64(0x1234)
```

然后，我们才能最终获得我们的地址。

