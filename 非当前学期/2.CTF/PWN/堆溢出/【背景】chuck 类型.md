1. `free chunck`
我们有三类我们的 `free chunk`,分别是我们的  `fastbin free chunck`, `largebin free chunck`, `small bin free chunck`,我们的每一种 ,我们的格式都不完全相同

对于我们的 `small bin free chunck`,我们的结构如下，相比于我们的 `malloc chunck`,我们多了两个控制字段，分别是我们的 `fd,bk`，这两个是存储我们的**逻辑链表**

特别：如果我们这个位置是我们的 `free`,并且我们的 `p` 标记也为 `0`，那么我们就会把我们的上下两个 `chunck` 来进行合并，节省我们的空间。

通过我们的 `prev size` 和 `size`,我们就能得到我们每一个链表的位置，以此形成我们的物理链表
![[Pasted image 20231211195809.png]]

## Large bin chunck
![[Pasted image 20231211200357.png]]
相比之下，我们多了两个段，分别是我们的 `fd_nextsize`, `bk_nextsize`。

## Fast bin free chunck
![[Pasted image 20231211200432.png]]




2. `malloc chunck`
我们的 `malloc chunck` 结构如下图所示 ![[Pasted image 20231211194014.png]]
我们的 `prev size` 记录了我们上一个物理相邻的 `chunck` 的大小：上一个是 `free chunck`,就是我们的 `free chunck` 的大小，
我们的 `size` 记录了我们当前字段的 (包含控制字段的）大小，我们下面的就是我们的具体数据。同时，在我们的 `size` 的第三位，存在我们的对应的三个控制字段，分别是我们的 `A,M,P`,
其中，我们的 `P` 表示我们的前一个 `chunck` 是否是我们的 `free chunck`,如果是，那么我们的 `P` 的值就是 `0`,