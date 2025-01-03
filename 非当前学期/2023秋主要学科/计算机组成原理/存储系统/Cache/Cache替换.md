上一篇：[[Cache映射]]
下一篇：[[Cache写]]

## 四、Cache 替换算法

![在这里插入图片描述](https://img-blog.csdnimg.cn/11084a779a9b4790a30fb560577c7734.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)

### 1．替换算法解决的问题

（1）全相联映射：Cache 完全满了才需要替换，需要在全局选择替换哪一块  
（2）直接映射：如果对应位置非空，则毫无选择地直接替换  （**不存在替换策略**）
（3）组相连映射：分组内满了才需要替换，需要在分组内选择替换哪一块  
（4）故只有全相联映射和组相连映射需要进行选择替换

### 2． [随机算法](https://so.csdn.net/so/search?q=%E9%9A%8F%E6%9C%BA%E7%AE%97%E6%B3%95&spm=1001.2101.3001.7020)（RAND，random）
![[Pasted image 20230810234458.png]]
（1）若 cache 已满，则随机选择一块替换。  
（2）设总共有 4 个 cache 块，初始整个 Cache 为空。采用全相联映射，依次访问主存块{1,2,3,4,1,2,5,1,2,3,4,5}   （3）实现简单，但完全没考虑局部性原理，命中率低，实际效果很不稳定

### 3．先进先出算法（[FIFO](https://so.csdn.net/so/search?q=FIFO&spm=1001.2101.3001.7020)，first in first out）

（1）若 cache 已满，则替换最先被调入 Cache 的块  
（2）设总共有 4 个 cache 块，初始整个 Cache 为空。采用全相联映射，依次访问主存块{1,2,3,4,1,2,5,1,2,3,4,5}  
![[Pasted image 20230810234632.png]]
（3）实现简单，最开始按 #0 #1 #2 #3 放入 Cache ，之后轮流替换 #0 #1 #2 #3 ，FIFO 依然没考虑局部性原理，最先被调入 Cache 的块也有可能是被频繁访问的  
（4）抖动现象: 频繁的换入换出现象（刚被替换的块很快又被调入)

### 4．近期最少使用（LRU，Least Recently Used）
![[Pasted image 20230810235403.png]]
（1）为每一个 Cache 块设置一个“计数器”，用于记录每个 cache 块已经有多久没被访问了。当 cache 满后替换“计数器”最大的  
（2）设总共有 4 个 cache 块，初始整个 Cache 为空。采用全相联映射，依次访问主存块{1,2,3,4,1,2,5,1,2,3,4,5}   （3）计数器规则  
①命中时，所命中的行的计数器清零，比其低的计数器加 1，其余不变  
②未命中且还有空闲行时，新装入的行的计数器置 0，其余非空闲行全加 1;  
③未命中且无空闲行时，计数值最大的行的信息块被淘汰，新装行的块的计数器置 0，其余全加 1。  
（4）Cache 块的总数=2^n, 则计数器只需 n 位。且 Cache 装满后所有计数器的值一定不重复  
（5）基于“局部性原理”，近期被访问过的主存块，在不久的将来也很有可能被再次访问，因此淘汰最久没被访问过的块是合理的. LRU 算法的实际运行效果优秀，cache 命中率高。  
（6）若被频繁访问的主存块数量>Cache 行的数量，则有可能发生“抖动

### 5．最近不经常使用（LFU, Least Frequently Used）

（1）为每一个 Cache 块设置一个“计数器”，用于记录每个 cache 块被访问过几次。当 Cache 满后替换“计数器”最小的  
（2）设总共有 4 个 cache 块，初始整个 Cache 为空。采用全相联映射，依次访问主存块{1,2,3,4,1,2,5,1,2,3,4,5}  
![[Pasted image 20230810235923.png]] 
（3）新调入的块计数器=0，之后每被访问一次计数器+1。需要替换时，选择计数器最小的一行, 若有多个计数器最小的行，可按行号递增、或 FIFO 策略进行选择  
（4）曾经被经常访问的主存块在未来不一定会用到（如: 微信视频聊天相关的块)，并没有很好地遵循局部性原理，因此实际运行效果不如 LRU