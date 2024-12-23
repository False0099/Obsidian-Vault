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


![[Pasted image 20230809232957.png]]

全相联映射：
主存块可以放在任意位置
假设某个计算机主存地址大小为256MB（2^28说明有主存有28位地址），按字节编址，其数据CACHE有8个cache行，行长为64B（ 同时也是主存杭长）。

用总地址位数（28）减去主存 块内地址（6），就是我们的主存块地址位数(22)，

我们拿到一个主存地址，我们先拿到主存地址的前22位，拿着与cache中的所有快对比
若标记匹配且有效位为1，则cache命中。
否则访问主存
![[Pasted image 20230810233633.png]]

组相连映射
假设某个计算机主存地址空间为256MB，按字节编制，其数据Cache有8个Cache行，行长为64B。
组相连映射，所属分组=主存快好%分组数
![[Pasted image 20230810233959.png]]


## Cache替换算法
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



Cache写策略。
写命中

写回法：
当CPU对cache写命中时，只修改cache的内容，而不理解写入主存，只有当此块被换出时才写会主存。（我们需要一个脏位表示是否需要写会主存）

全写法：
当CPU对cache写命中时，必须把数据同时写入主存和Cache。一般使用写缓冲。

写不命中



写分配
当CPU对Cache写不命中时，把主存中的块调入Cache，在Cache中修改，通常搭配回写法。


非写分配
当CPU对Cache写不命中时，只写入主存，不调入Cache


多级Cache
越接近CPU，速度越快
越原理CPU，速度越慢![[Pasted image 20230811000945.png]]
![[Pasted image 20230811001059.png]]