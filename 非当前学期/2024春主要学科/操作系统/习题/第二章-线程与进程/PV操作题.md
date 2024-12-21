### 生产者消费者
![[Pasted image 20240627132621.png]]

我们本题是我们的生产者消费者为题的扩展，我们的**生产者生产不同的物品**，我们的**消费者消费不同的物品。

于是我们就可以考虑用我们的变形的生产者消费者模型：
```
semaphore mutex=1;
semaphore emtpy=n;
semaphore full1=0;
semaphore full2=0;
void produce(){
	while(1){
		p(empty);
		p(mutex);
		put();
		v(mutex);
		x=produce();
		if(x%2==0){
			v(full1);
		}else{
			v(full2);
		}
	}
}
void consumer1(){
	while(1){
		p(full1);
		p(mutex);
		eat();
		v(mutex);
		v(empty);
	}
}
void consumer1(){
	while(1){
		p(full2);
		p(mutex);
		eat();
		v(mutex);
		v(empty);
	}
}
```

![[Pasted image 20240627133617.png]]
我们分析本题，发现我们本题中没有明显的生产者消费者要求，只有我们的互斥要求，于是我们只需要使用我们的互斥即可, 同时我们再去记录我们的最大人数。
```
P(mutex)
if(count>500){
	v(mutex);
	return
}else{
	count++;
}

count++;
V(mutex)
参观
P(mutex)
出门
V(mutex)
```

![[Pasted image 20240627135315.png]]

这一题类似于我们的多生产者多消费者问题，我们的 A 是 B 的生产者，同时也是 B 的消费者。对于这样的程序，我们只需要考虑使用我们的 PV 操作即可
```cpp
semaphore box_A_mutex = 1;    // A的信箱互斥信号量
semaphore box_A_empty = M-x;  // A的信箱空闲位置数量
semaphore box_A_full = x;     // A的信箱邮件数量
semaphore box_B_mutex = 1;    // B的信箱互斥信号量
semaphore box_B_empty = N-y;  // B的信箱空闲位置数量
semaphore box_B_full = y;     // B的信箱邮件数量

CoBegin
A {
    while (true) {
        P(box_A_full);    // 请求A的信箱一个邮件
        P(box_A_mutex);
        从A的信箱中取出一个邮件;
        V(box_A_mutex);
        V(box_A_empty);    // A的信箱空位数量加一
        回答问题并提出一个新问题;
        P(box_B_empty);    // 请求B的信箱一个空位
        P(box_B_mutex);
        将新邮件放入B的信箱;
        V(box_B_mutex);
        V(box_B_full);    // B的信箱邮件数量加一
    }
}
B {
    while (true) {
        P(box_B_full);    // 请求B的信箱一个邮件
        P(box_B_mutex);
        从B的信箱中取出一个邮件;
        V(box_B_mutex);
        V(box_B_empty);    // B的信箱空位数量加一
        回答问题并提出一个新问题;
        P(box_A_empty);    // 请求A的信箱一个空位
        P(box_A_mutex);
        将新邮件放入A的信箱;
        V(box_A_mutex);
        V(box_A_full);    // A的信箱邮件数量加一
    }
}
CoEnd
```

