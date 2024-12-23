1. SRAM 存储器
每个存储单元由 6 个晶体管（MOS）组成，只要加上电源，信号就能一致保持。对外部电磁干扰不敏感，价格较贵。
记忆单元-----》存储器芯片（存储体（记忆单元构成的存储阵列）+外围电路（地址译码器、读写控制）-----》内存条

存储芯片基本结构
		1. 存储矩阵
		2. 译码器
		3. 读写电路

读过程
		1. 给出地址
		2. 给出片选与读命令
		3. 读出内容

写过程
		1. 给出地址
		2. 给出片选与待写入数据
		3. 给出写命令

结构
![[Pasted image 20230809002052.png]]
![[Pasted image 20230809003428.png]]
如果我们是采用每个地址对应一个线的这种方法的话，我们的结果就是如果地址线有 n 根，那么我们译码器另一端就要链接 2^n 个线，但是如果我们采用地址线复用计数，就能够减少我们的线的个数。![[Pasted image 20230809210146.png]]


![[Pasted image 20230809210216.png]]
		1. 存储体（4096×1）
			1. 通常把各个字的同一位集成在一个芯片中，4096=64×64
![[~8 UKRLT 4 OG]QGF 6[R 991 ENM_tmb. Png]]
		2. 地址译码器
			1. 双译码的方式（减少选择线条数）
			2. $A_{0}-A_{5}$ 为 X 地址（行地址）
			3. $A_{6}-A_{11}$ 为 Y 地址（列地址）
		3. 驱动器
		4. I/O 电路
			1. 位于被选择单元和数据总线之间，控制被选择单元读出和写入，具有信号放大的作用。
		5. 控制电路
			1. 片选
				1. 选中芯片
			2. 读/写
				1. 读操作控制
				2. 写操作控制
		6. 输出驱动电路
![[YT]1%OC 6_AS`% WA@IDF5 %5 R 1. Png]]
晶体管电路：
写入时，位线上的信息是被写入的二进制信息，然后我们置字线为 1，存储单元按位线的状态设置为 0 或 1。
读出时，我们置两个位线为高电平，置字线为 1，存储单元状态不同，位线的输出就会不同。

![[Pasted image 20230725224740.png]]
	1. 实例（2114 逻辑结构框图）
![[LKE_~BA%68YPM533)(3EGAT.png]]
	3. 存储器容量扩充
			1. 位扩展（扩展内存）
				1. 增加存储器字长
![[F` _3P1W@L8 ]21 OG{2 P`~~T 5. Png]]
			2. 字扩展（扩展地址）
				1. 增加存储器字的数量（线选法，额外增加若干根线来控制选区的芯片的位置。注意，不能同时为 0，否则会矛盾。
	![[Pasted image 20230809220521.png]]
				2. 译码器片选法，将部分多余的地址线与译码器链接后，根据译码器发出的信号来选择具体的芯片。
![[6ZAVP7~1B$8NZJV_Y3P8NCX_tmb.png]]
	5. SRAM 时序---》读周期
![[HNM 6 AG]U 9}7{72 U 7[RJWGC 1. Png]]
2. DRAM 存储器
	DRAM 利用存储元电路中**栅极电容上的电荷**来存储信息的，DRAM 的基本存储元通常只用一个晶体管，所以他比 SRAM 的密度要搞很多。DRAM 电容上的电荷一般只能维持 1-2 ms，因此即使电源不断电，信息也会自动消失，为此，**每隔一段时间必须刷新**，通常取 2 ms，称为刷新周期，我们同上有下面三中刷新方式：集中刷新，分散刷新，异步刷新。
	![[Pasted image 20230920085842.png]]
刷新操作：由刷新计数器产生行地址，选择当前要刷新的行，读出方式更新，刷新一行所需时间就是一个存储周期

刷新周期：从对整个存储器刷新结束时起，到对整个 DRAM 全部刷新一边为止时间间隔

刷新信号周期：相邻两行位元之间刷新的时间间隔单元。

刷新间隔时间：DRAM 允许的最大信息保持时间一般为 64 ms

刷新行数：单个芯片的单个矩阵行数。

**对于内部包含多个存储矩阵的芯片，各个矩阵的同一行是被同时刷新的对于多个芯片连接构成的 DRAM，DRAM 控制器将选中所有芯片的同一行来进行逐行刷新。**

一、集中刷新
1. 集中刷新在规定的一个刷新周期内，对全部存储单元几种一段时间逐行进行刷新，此刻必须停止读/写操作。![[Pasted image 20230809211041.png]]
		优点：读写操作不受刷新工作的影响
		缺点：在死区内不能访问存储器
	我们每一次刷新所需要的时间，就是我们访问所有格子所需要的时间，而我们每一次访问都是以行为单位进行的，假设我们访问一行所需要的时间叫做**单元刷新间隔**为 $dt$,那么我们进行一次集中刷新需要的时间就是 $n*dt$,其中 n 是我们的行数。
	而我们在集中刷新中，我们的刷新时间是在我们的存储周期当中，如图所示：![[Pasted image 20230920104737.png]]
	那么除去掉刷新时间，剩下的就会是我们的访存操作时间。
二、分散刷新（把我们的**每一次刷新都放到我们的存储周期之后**）
1. 分散刷新**对每行存储单元的刷新分散到每个存取周期内**完成。其中把机器的存取周期分为两段，前半段用来读/写或维持信息（访存），后半段用来刷新。我们系统存储周期为存储器存储周期的两倍，即 500 ns * 2=1 us, 我们的刷新周期缩短，为 128 * 1 us=128 us。但是，在我们的 2 ms 的单元刷新间隔时间内，对 DRAM 刷新了 15 遍
![[Pasted image 20230809211029.png]]
			优点：没有死区
			缺点：加长了系统的存取周期
三. 异步刷新
1. 对前两种方式的结合，即可缩短死时间，又可以充分利用最大刷新间隔 ![[Pasted image 20230809211050.png]] 在 2 ms 内分散地把各行刷新一遍。
优点：避免 CPU 连续等待长时间

关于我们的刷新，我们要注意: 1. 刷新对 CPU 是透明的，刷新不依赖于外部的访问 2. 动态 RAM 的刷新单位是行，由芯片内部自动生成地址。3. 刷新操作类似于读操作，但又有所不同
![[Pasted image 20230920105834.png]]
**每出现新一代 DRAM 芯片，容量至少提高到四倍，因为行地址和列地址分时复用，每出现一代，至少增加一根地址线，每增加一根地址线，行地址列地址就各增加一倍**
1. DRAM 与 SRAM 的比较
	1. DRAM（动态）
		1. 在主存中使用
		2. 访问时问较慢
		3. 高密度
		4. 低成本
		5. 需要刷新
		6. 读操作时破坏 ing 的
		7. 地址线分时复用
	2. SRAM（静态）
		1. 在 caches 中使用
		2. 较快访问时间
		3. 低密度
		4. 高成本
		5. 不需要刷新
		6. 读操作不是破坏性的
		7. 地址线不是分时复用

存储器的内部结构：
![[Pasted image 20230920110122.png]] ![[Pasted image 20230920110158.png]]

