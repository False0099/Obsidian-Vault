上一篇：[[非当前学期/2023秋主要学科/计算机组成原理/存储系统/系统概述]]
下一篇：[[DRAM]]

S（Static）RAM 存储器（不需要刷新）由**触发器**构成，读出不是破坏性的。

结构：**存储元**（每一个触发器 单元）->**元阵列**（许多存储元按照矩形排列形成的结构）->**存储阵列**（多个元阵列并排排列形成的结构）->**存储芯片**

存储芯片基本结构
1. **存储矩阵**：由多个元阵列构成的存储阵列，由行数 ,列数，字长三个参数共同决定，我们的芯片大小，就是由我们的行数乘以列数乘以字长所计算的。（对应于[[系统连接方式]]中的**数据总线**）
2. **译码器**：我们的芯片采用二级译码，分为行地址和列地址：我们的行地址译码会选中我们存储阵列中的某一行，列地址同理。被选中的行与列相交位置存储单元被选中，即每个元阵列地址相同的存储元被选中。（对应于[[系统连接方式]] 中的**地址总线**）
3. **读写电路**：由地址线，控制线，数据线构成，地址线由我们的行数、列数分别决定，数据线由我们的字长决定。(对应于[[系统连接方式]]种的**控制总线**)

![[Pasted image 20231129214622.png]]

## 读周期时序 ：

控制流程：
1. 地址信号有效-》片选有效-》读出数据-》片选无效-》下一个信号

我们的几个时间：
1. $t_{AQ}$ 读出时间，从给出有效地址到数据总线上稳定出现读出数据所经历的时间。
2. $t_{RC}$,读周期，完成一次读操作所需要的时间
3. $t_{EQ}$: 片选有效到数据稳定读出的时间
4. $T_{GQ}$: 使能有效到数据稳定读出需要的时间
![[Pasted image 20231130103524.png]]
## 写周期时序：
控制流程：地址信号有效-》片选信号有效-》写命令有效-》写数据-》写命令无效-》片选信号无效-》下一个地址信号

1. 地址信号有效
2. 片选/使能信号有效
3. 写命令有效
4. 写数据
5. 写命令无效
6. 片选/使能信号无效
7. 下一个读/写信号有效，开始下一个操作

我们的几个时间：
1. $t_{WD}$ 写入时间，数据写入存储单元的时间
2. $t_{WC}$,写周期，完成一次读操作所需要的时间
3. $t_{SA}$: 写命令开始有效片选信号有效到数据稳定读出的时间
4. $T_{AD}$: 写命令无效后，写数据继续维持的时间
（读操作和写操作的区别在于我们的写周期还多了一个对于我们的写信号的识别。）**表明我们依次只能写一个数据**

![[Pasted image 20231130103503.png]]

