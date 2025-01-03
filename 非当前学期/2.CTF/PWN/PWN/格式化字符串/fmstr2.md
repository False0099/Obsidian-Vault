步骤一：分析我们的函数，反汇编我们的函数
![[Pasted image 20231208213815.png]]
![[Pasted image 20231208213822.png]]
步骤二：分析我们的函数逻辑
第一步要求我们像我们的 `format` 输入一个字符串，然后把我们的字符和我们的 `flag` 进行一次比较，然后打印我们的 `format`,之后，如果我们正确，我们就得到我们的 `flag`,如果错误，我们就直接退出

步骤三：分析我们的漏洞
因为我们的 `printf` 输入的是很不严实的 `print(format)`,于是我们可以考虑用我们的格式化字符串

步骤四：分析我们的需求
我们因为我们的 `flag` 本身就在我们的文件中，于是我们就可以直接去求我们的对应字符串的内容泄露。

步骤五：构造我们的泄露形 `payload`
构造泄露型 `payload` 需要下面的几步：
第一步，找到我们的 `printf` 所在的位置，这里时我们的 ``0x7fffffffe208``。
![[Pasted image 20231208214225.png]]

第二步：找到我们需要的内容所在的地址，在这里我们的目标就是打开我们的 `flag`,于是我们就找到 `flag` 所在的位置，**因为我们采用我们的 64 位结构，所以我们只有我们的第 7 个参数才是我们的答案**，

这时，我们应该考虑直接打印我们的第七个参数，**找到我们的对应的位置在哪里，找到我们的参照系**。我们构造一个这样的字符串 (这里用 `%s`,因为我们要获得当中的个内容)
```text
%7$s%8$s%9$s
```
最后我们得到这样的一个值：

然后我们就可以发现，我们
（其中我们发现，我们的 `aaaaaa` 所在的地址有点奇怪，他似乎是什么 `0x602cb0`,不在我们的栈中，在我们的寄存器中）

第三步：我们发现，我们的第九个参数所在的位置，恰好是我们的数据，于是我们就可以直接打印我们的结果。
```python
payload=%9$s
```