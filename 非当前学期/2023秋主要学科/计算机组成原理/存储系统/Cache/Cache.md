**（怎么分块的，重点！）**
Cache 主要有 SRAM 构成

程序访问的局部性原理
程序的访问具有时间局部性、空间局部性，我们现在访问的信息有可能之后会频繁使用。、

工作原理
Cache被集成在CPU内部，用SRAM实现，速度快，成本高。![[Pasted image 20230809232323.png]]
1.检查是否在cache中
2.检查cache是否已满
![[Pasted image 20230809232456.png]]

CPU与cache之间的数据交换以字为单位，主存和cache之间的数据交换以Cache块为单位。Cache中的块![[Pasted image 20230809232627.png]]
性能指标：
命中率=CPU欲访问的信息已在Cache中的比率

![[Pasted image 20230809232728.png]]


Cache与主存的映射方法
直接映射
每个主存块只能放到特定地址
Cache块号=主存块号%cache总块数
相当于只留下cache块好的对应几位，那几位能够反映他在cache中的位置
这个时候，我们依然需要我们的 CAM 表，用来专门存储我们每一个块是对应哪些可能的主存。

![[Pasted image 20230809232957.png]]

全相联映射：
主存块可以放在任意位置
假设某个计算机主存地址大小为256MB（2^28说明有主存有28位地址），按字节编址，其数据CACHE有8个cache行，行长为64B（ 同时也是主存杭长）。

用总地址位数（28）减去主存 块内地址（6），就是我们的主存块地址位数(22)，

我们为此，需要搭建一个映射表，存储我们主存头 s 位和我们的 cache 对应块之间的映射，然后，我们就可以拿着这个表去对照我们的了。

我们拿到一个主存地址，我们先拿到主存地址的前22位，拿着与cache中的所有快对比
若标记匹配且有效位为1，则 cache 命中。

否则访问主存
![[Pasted image 20230810233633.png]]

组相连映射
假设某个计算机主存地址空间为256MB，按字节编制，其数据Cache有8个Cache行，行长为64B。

Cache 空间的地址： $d+r_{1}+w$
一共有 $u=2^d$ 组，每组 $v=2^{r_{1}}$ 行，
![[无标题 16.png]]
组相连映射，所属分组=主存快好%分组数
![[Pasted image 20230810233959.png]]


## Cache 替换算法 (重点)
随机算法
如果cache已满，则随意选择一块替换

![[Pasted image 20230810234458.png]]
先进先出
若Cache满，则替换最先被调入Cache的块
![[Pasted image 20230810234632.png]]

近期最少使用算法
增加一个计数器，计算每个cache有多久没有被访问了，当cache满后替换“计数器”最大的
![[Pasted image 20230810235403.png]]

采用这种算法，我们能够保证计数器只有有限种情况，所以我们只需要log(n)位额外地址来用作计数器即可。

局限：如果被频繁访问的主存快数量》cache行数量，则可能发生抖动。

最不经常使用算法
为每一个cache块设置一个计数器，替换计数器最小的
![[Pasted image 20230810235923.png]]



## Cache 写策略。
## 思维导图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015223402749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTg3NzQw,size_16,color_FFFFFF,t_70#pic_center)

## 存在的问题

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015221656906.png#pic_center)

## Cache写策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015221723534.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTg3NzQw,size_16,color_FFFFFF,t_70#pic_center)

### 写回法

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015221907779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTg3NzQw,size_16,color_FFFFFF,t_70#pic_center)

> **先修改Cache中的数据**，然后通过脏位判断是否被修改过；若修改过写会主存，没修改过不必写会  
> CPU----->Cache------>主存

### 全写法

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015222619165.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTg3NzQw,size_16,color_FFFFFF,t_70#pic_center)

> 1、CPU往Cache写数据的同时也往主存中写数据，一直保存数据一致  
> 2、由于CPU往主存中写数据很慢，所以需要一个写缓冲队列；  
> 3、当往Cache1和Cache3写数据时，会按照某种方式往写缓冲中写一份，然后CPU可以继续做其他事情，写缓冲中的数据由专门的硬件电路写入主存  
> 4、当写频繁时，有可能出现写缓冲满的情况，这时CPU会因为写缓冲饱和而发生阻塞

### 写分配法

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015223023322.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTg3NzQw,size_16,color_FFFFFF,t_70#pic_center)

> 1、未命中，则先将数据从主存调入Cache，然后在对Cache进行写，最后用写回法将Cache中的数据写会主存

### 非写分配法

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015223207534.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTg3NzQw,size_16,color_FFFFFF,t_70#pic_center)

> CPU直接写入主存，不与Cache交互

## 多级Cache

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201015223350427.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTg3NzQw,size_16,color_FFFFFF,t_70#pic_center)