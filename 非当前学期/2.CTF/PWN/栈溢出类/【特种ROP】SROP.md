## 背景
signal 机制是类 unix 系统中进程之间相互传递信息的一种方法。一般，我们也称其为软中断信号，或者软中断。比如说，进程之间可以通过系统调用 kill 来发送软中断信号。一般来说，信号机制常见的步骤如下图所示：
![[Pasted image 20240221010142.png]]
1. 内核向某个进程发送 signal 机制，该进程会被暂时挂起，进入内核态。
2. 内核会为该进程保存相应的上下文，**主要是将所有寄存器压入栈中，以及压入 signal 信息，以及指向 sigreturn 的系统调用地址**。此时栈的结构如下图所示，我们称 ucontext 以及 siginfo 这一段为 Signal Frame。**需要注意的是，这一部分是在用户进程的地址空间的。之后会跳转到注册过的 signal handler 中处理相应的 signal。因此，当 signal handler 执行完之后，就会执行 sigreturn 代码。**
![[Pasted image 20240221010152.png]]
说人话就是：

①保存上下文环境（即各种寄存器）
②执行信号处理函数
③恢复相关栈环境
④继续执行用户程序。而在**恢复寄存器**环境时没有去校验这个栈是不是合法的，如果我们能够控制栈，就能在恢复上下文环境这个环节直接设定相关寄存器的值。

## 原理
通过上面对面signal机制的认识，我们可以敏锐的发现，在1~2的过程中，此时我们保存的**sigFrame**是完全在用户空间的，也就是**对于进程来说可读可写**，而且其实SROP利用的最根本的漏洞是因为，在1的时候内核对于进程挂起后保存下的sigFrame以及恢复环境是的sigFrame是没有关联的，所以我们可以伪造sigFrame从而利用syscall进行调用恶意进程。


我们的这一个系统调用从我们的栈上取值，来设置我们的每一个寄存器。我们有如下所示：
![[Pasted image 20240122151622.png]]

如果这样，我们就可以考虑控制我们的 `rax=0x3b,rdi=binsh,rsi=rdx=0,rip=syscall`
这样，我们就可以执行我们的 system 了。
为了达到这样的攻击目标，我们首先要控制 rax=0 xf, 并且能够执行 syscall 指令，这样才能调用 `rt_sigreturn`。

在我们的题目里面，可以发现 pop rax，syscall，我们就可以实现我们的这一个指令。

![[Pasted image 20240122152302.png]]





第一步：构造我们的 `sys_sigret` 函数

我们想要构造我们的这一个函数，我们就需要用我们的 rax 和 syscall
```python
rax_syscall=0x4011ed
syscall=0x4011da
```

第二步：找到我们的 `binsh`

方法一：我们通过我们的 write 泄露内存信息漏洞，把我们的 binsh 写在我们的栈空间上。，然后在通过动态调试得到两者之间的地址差是多少。我们就能得到我们的 binsh 的最终地址。
```cpp
stack=l64()
binsh=stack-0x28

```

第三步：进行我们的系统调用
```python
sigframe=SigreturnFeature()
sigframe.rax=constants.SYS_execve
sigfram.rdi=binsh
sigfram.rsi=0;
sigframe.rdx=0
sigframe.rip=syscall
payload=b'a'*0x10+p64(rax_syscall)+p64(0xf)+flat(sigfram)
```