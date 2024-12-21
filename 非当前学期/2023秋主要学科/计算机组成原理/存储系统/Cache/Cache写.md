上一篇：[[Cache替换]]
下一篇：[[非当前学期/2023秋主要学科/计算机组成原理习题/存储器/虚拟存储器]]

## 五、Cache 写策略

![在这里插入图片描述](https://img-blog.csdnimg.cn/9438fb6cf7794227a7230ded420f7dc3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)

### 1．问题：
假设，CPU 修改了 cache 中的数据副本，如何确保主存中数据母本的一致性?

### 2．写命中
假设我们想要修改我们主存中的某一个单元的数据，那么我们的操作就是先去找这一块是否在我们的 `cache` 中，如果在我们进行我们的 ``写命中操作``，我们有两种思路：第一种是全部写完后再去返回，第二种是写完一个就返回一个

（1）**写回法** (write-back)——当 CPU 对 Cache 写命中时，只修改 Cache 的内容，而**不立即写入主存，只有当此块被换出时才写回主存**  
①设置`脏位`，表示是否被修改过  
②减少了访存次数，但存在数据不一致的隐患。  
![在这里插入图片描述](https://img-blog.csdnimg.cn/6138ca4b3105483081352e69203cfdd4.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)  


（2）**全写法** (写直通法，write-through)——当 CPU 对 Cache 写命中时，**必须把数据同时写入 Cache 和主存**，一般使用写缓冲 (write buffer)  
![在这里插入图片描述](https://img-blog.csdnimg.cn/578547f6dc314b10bff8cfae2c1e1fb9.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5p-g5qqs6Iy2QA==,size_20,color_FFFFFF,t_70,g_se,x_16)  
①访存次数增加，速度变慢，但更能保证数据一致性  
②优化：增加写缓冲（SRAM 实现的 FIFO 队列），在专门的控制电路下逐一写回  
③使用写缓冲，CPU 写的速度很快，若写操作不频繁，则效果很好。若写操作很频繁，可能会因为写缓冲饱和而发生阻塞

### 3．写不命中
如果我们想要修改的主存内容，在我们的 `cache` 中不存在对应的元素，那么我们的操作就应该是：

（1）**写分配法** (write-allocate)：当 CPU 对 Cache 写不命中时，把**主存中的块调入 Cache**，在 Cache 中修改。通常搭配 ``写回法``（全部修改完再调回）使用。  

（2）**非写分配法** (not-write-allocate)：当 CPU 对 Cache 写不命中时只写入主存，**不调入 Cache**（一次修改，一次返回）。搭配全写法使用。

