基本工作原理：
Cache 位于存储器结构层次的顶层，通常**由 SRAM 构成**，基本结构如图所示：
![[Pasted image 20230927100307.png]]
为便于 Cache 和主存间交换信息，**Cache 和主存都被划分为单位面积相等的块**，Cache 块又称 Cache 行，每块由若干字节组成，**快的长度称为块长**，Cache 中的**块数量要远少于主存中的块数**，他**仅保存主存中最活跃的若干块**的副本。

工作过程：
1.CPU 发出读请求
如果**访存地址在 Cache 中命中，就把此地址转换为 Cache 地址**，**直接对 Cache 进行读操作**，
若 Cache 不命中，就再去访问我们的主存，并**把此字在的块一次性的从主存调入 cache**，
·若 Cache 已满，我们就需要根据我们的替换算法，把这个块拿去替换 Cache 中原来的某块信息。
注意：**CPU 与 Cahe 之间的数据交换以字为单位，而 Cache 与主存之间的数据交换以 Cache 块为单位。**

2. CPU 发出写请求
	若 Cache 命中
		Cache 与主存中的内容不一致-》按照一定的写策略处理

数据计算：
Cpu 欲访问的信息已经在 Cache 中的比率称为 Cache 的命中率。设一个程序执行期间，Cache 的总命中次数为 $N_{c}$,访问主存的总次数为 $N_{m}$,则命中率 H 为 $H=\frac{N_{c}}{(N_{c}+N_{m})}$

设 $t_{c}$ 为命中时的 Cache 访问时间，$t_{m}$ 为未命中时的访问时间，则我们的 Cache-主存系统的平均访问时间为 $T_{a}=Ht_{c}+(1-H)t_{m}$

![[Pasted image 20230927101131.png]]


Cache 和主存的映射方式
Cache 行中的信息时主存中某个块的副本，地址映射是指把主存地址空间映射到 Cache 地址空间，即把存放在主存中的信息按照某种规则装入 Cache。

由于 **Cache 行数比主存块数少得多**，因此主存中只有一部分块的信息可以放在 CAche 中

1. 直接映射
	主存中的每一块只能装入 Cache 中的唯一位置。若这个位置已有内容，则产生块冲突，**原来的块将无条件的替换出去**，我们对应的映射关系可以直接定义为 **Cache 行号=主存块号 mod Cache 总行数。**
	
	假设 Cache 一共有 $2^c$ 行，主存有 $2^m$ 块，在直接映射方式中，主存的第 0 块，第 $2^c$ 块... 只能映射到第 0 行，以此类推。
	
	由映射函数可以看出，**主存块号的低 c 为正好是他要装入的 cache 行号**。给每个 cache 行设置一个**长为 $t=m-c$ 的标记**，标记我们现在的 cache 是**同余系下哪一个的映射**，也就是我们的 k 是多少，当主存某块调入 cache 后，就将其块号的高 t 位设置在对应的 Cache 行的标记中，如图
	![[Pasted image 20230927102900.png]]

	我们在 CPU 访存过程中，首先根据访存地址的中间的 c 位，找到对应的 Cache 行，将对应 Cache 行中的标记和贮存地址的高 t 位标记进行比较，若相等且有效位为 1，则访问 Cache 命中，否则不命中。

2. 全相联映射
	主存中的每一块可以装入 Cache 中的任何位置，每行的标记用于指出该行取自主存的哪一块，所以 CPU 访存时需要与所有 Cache 行的标记进行比较。
	![[Pasted image 20230927103635.png]]

3. 组相联映射
	将 Cache 分成 Q 个大小相等的组，每个主存块可以装入固定组中的任意一行，组间采用直接映射，组内采用全相联映射。
	![[Pasted image 20230927111335.png]]
![[Pasted image 20230927111404.png]]



![[Pasted image 20230927114140.png]]
我们这里的行长，**仅仅只是指我们有效数据部分的大小**，即我们一个 cache 行中能存储多少主存中的信息，这里我们是 64 B，512bit。
我们之后标记位，我们的主存地址有 28 位，其中 6 位位块内地址（$\log_{2}64$），3 位为行号 ($\log_{2} 64)$，标记字段长度=主存地址总长度-**块内地址**总长度-行号= $28-6-3$。再加上一位有效位，我们就一共有 20 位改行对应的标记位，总容量为 $8*(512+1+19)=4256$
![[Pasted image 20230927121022.png]]
![[Pasted image 20230927121016.png]]

在直接映射方式中，主存按照块的大小划分，主存地址 3200 对应的字块号为 $\frac{3200B}{64B}=50$,而 Cache 只有 8 行，则 Cache 行号为 2

**我们把我们的块理解成我们存储器中的一片，我们的块内地址就相当于我们的片内地址**
## 四、Cache 替换算法

![在这里插入图片描述](https://img-blog.csdnimg.cn/11084a779a9b4790a30fb560577c7734.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)

### 1．替换算法解决的问题

（1）全相联映射：Cache完全满了才需要替换，需要在全局选择替换哪一块  
（2）直接映射：如果对应位置非空，则毫无选择地直接替换  
（3）组相连映射：分组内满了才需要替换，需要在分组内选择替换哪一块  
（4）故只有全相联映射和组相连映射需要进行选择替换

### 2．[随机算法](https://so.csdn.net/so/search?q=%E9%9A%8F%E6%9C%BA%E7%AE%97%E6%B3%95&spm=1001.2101.3001.7020)（RAND，random）

（1）若cache已满，则随机选择一块替换。  
（2）设总共有4个cache块，初始整个Cache为空。采用全相联映射，依次访问主存块{1,2,3,4,1,2,5,1,2,3,4,5}  
![在这里插入图片描述](https://img-blog.csdnimg.cn/a92ce5fda4c8412f963a8c4b7e957439.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)  
（3）实现简单，但完全没考虑局部性原理，命中率低，实际效果很不稳定

### 3．先进先出算法（[FIFO](https://so.csdn.net/so/search?q=FIFO&spm=1001.2101.3001.7020)，first in first out）

（1）若cache已满，则替换最先被调入Cache的块  
（2）设总共有4个cache块，初始整个Cache为空。采用全相联映射，依次访问主存块{1,2,3,4,1,2,5,1,2,3,4,5}  
![在这里插入图片描述](https://img-blog.csdnimg.cn/8b8af2ecd8e14c89a3edbcd7d9cf8715.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)  
（3）实现简单，最开始按#0#1#2#3放入Cache，之后轮流替换#0#1#2#3，FIFO依然没考虑局部性原理，最先被调入Cache的块也有可能是被频繁访问的  
（4）抖动现象:频繁的换入换出现象（刚被替换的块很快又被调入)

### 4．近期最少使用（LRU，Least Recently Used）

（1）为每一个Cache块设置一个“计数器”，用于记录每个cache块已经有多久没被访问了。当cache满后替换“计数器”最大的  
（2）设总共有4个cache块，初始整个Cache为空。采用全相联映射，依次访问主存块{1,2,3,4,1,2,5,1,2,3,4,5}  
![在这里插入图片描述](https://img-blog.csdnimg.cn/f5ec220cf6b34eac9a6723b56d166bef.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)  
（3）计数器规则  
①命中时，所命中的行的计数器清零，比其低的计数器加1，其余不变  
②未命中且还有空闲行时，新装入的行的计数器置0，其余非空闲行全加1;  
③未命中且无空闲行时，计数值最大的行的信息块被淘汰，新装行的块的计数器置0，其余全加1。  
（4）Cache块的总数=2^n,则计数器只需n位。且Cache装满后所有计数器的值一定不重复  
（5）基于“局部性原理”，近期被访问过的主存块，在不久的将来也很有可能被再次访问，因此淘汰最久没被访问过的块是合理的.LRU算法的实际运行效果优秀，cache命中率高。  
（6）若被频繁访问的主存块数量>Cache行的数量，则有可能发生“抖动

### 5．最近不经常使用（LFU,Least Frequently Used）

（1）为每一个Cache块设置一个“计数器”，用于记录每个cache块被访问过几次。当Cache满后替换“计数器”最小的  
（2）设总共有4个cache块，初始整个Cache为空。采用全相联映射，依次访问主存块{1,2,3,4,1,2,5,1,2,3,4,5}  
![在这里插入图片描述](https://img-blog.csdnimg.cn/0fa69c685f614dc28b8db15407fa0753.png)  
（3）新调入的块计数器=0，之后每被访问一次计数器+1。需要替换时，选择计数器最小的一行,若有多个计数器最小的行，可按行号递增、或FIFO策略进行选择  
（4）曾经被经常访问的主存块在未来不一定会用到（如:微信视频聊天相关的块)，并没有很好地遵循局部性原理，因此实际运行效果不如LRU

## 五、Cache写策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/9438fb6cf7794227a7230ded420f7dc3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)

### 1．问题：CPU修改了cache中的数据副本，如何确保主存中数据母本的一致性?

### 2．写命中

（1）写回法(write-back)——当CPU对Cache写命中时，只修改Cache的内容，而不立即写入主存，只有当此块被换出时才写回主存  
①设置脏位，表示是否被修改过  
②减少了访存次数，但存在数据不一致的隐患。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/6138ca4b3105483081352e69203cfdd4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)  
（2）全写法(写直通法，write-through)——当CPU对Cache写命中时，必须把数据同时写入Cache和主存，一般使用写缓冲(write buffer)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/578547f6dc314b10bff8cfae2c1e1fb9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)  
①访存次数增加，速度变慢，但更能保证数据一致性  
②优化：增加写缓冲（SRAM实现的FIFO队列），在专门的控制电路下逐一写回  
③使用写缓冲，CPU写的速度很快，若写操作不频繁，则效果很好。若写操作很频繁，可能会因为写缓冲饱和而发生阻塞

### 3．写不命中

（1）写分配法(write-allocate)：当CPU对Cache写不命中时，把主存中的块调入Cache，在Cache中修改。通常搭配写回法使用。  
（2）非写分配法(not-write-allocate)：当CPU对Cache写不命中时只写入主存，不调入Cache。搭配全写法使用。

### 4．多级cache

（1）现代计算机常采用多级cache，离CPU越近的速度越快，容量越小离CPU越远的速度越慢，容量越大
