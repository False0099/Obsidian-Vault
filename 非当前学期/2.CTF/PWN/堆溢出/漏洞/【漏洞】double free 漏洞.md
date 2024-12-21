我们在我们 free 掉一块内存前，如果我们没有清除我们的那一个指针，那么我们就可以对我们的那一个指针再次进行我们的 `free` 操作，这里就会导致我们的一个 `double free` 漏洞。

Firstly, remember that for fast chunks in the fastbin, the location of the next chunk in the bin is specified by the `fd` pointer. This means if chunk `a` points to chunk `b`, once chunk `a` is freed the next chunk in the bin is chunk `b`.

In a double-free, we attempt to **control** `fd`. By overwriting it with an arbitrary memory address, we can tell `malloc()` _where the next chunk is to be allocated_. For example, say we overwrote `a->fd` to point at `0x12345678`; once `a` is free, the _next chunk on the list_ will be `0x12345678`_._

As it sounds, we have to free the chunk **twice**. But how does that help?

Let's watch the progress of the fastbin if we free an arbitrary chunk `a` twice:

Char *a = malloc (0 x 20);

Free (a);

Free (a);

![](https://1919401647-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-MEwBGnjPgf263kl5vWP%2F-MK9TNWL7biIXc0-fwXW%2F-MK9VSrgVDh-ZFxWSS42%2Fimage.png?alt=media&token=aa9841ab-2177-4625-8d09-5206a4ef6fb0)

Fairly logical.

But what happens if we called `malloc()` again for the same size?

Char *b = malloc (0 x 20);

Well, strange things would happen. `a` is both allocated (in the form of `b`) _and free at the same time_.

If you remember, the heap attempts to save as much space as possible and when the chunk is free the `fd` pointer is written **where the user data used to be**.

![](https://1919401647-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-MEwBGnjPgf263kl5vWP%2F-MK9TNWL7biIXc0-fwXW%2F-MK9Xit_NuWfjHavDbpF%2Fimage.png?alt=media&token=b0847a19-9fb2-4e69-b8ba-08dc01964a41)

But what does this mean?

When we write into the use data of `b`, we're writing into the `fd` of `a` _at the same time_.

And remember - controlling `fd` means we can control where the next chunk gets allocated!

So we can write an address into the data of `b`, and that's where the next chunk gets placed.

`strcpy(b, "\x78\x56\x34\x12");`

Now, the next alloc will return `a` **again**. This doesn't matter, we want the one afterwards.

`malloc(0x20)` /* This is yet another 'a', we can ignore this */

`char *controlled = malloc(0x20);` /* This is in the location we want */

Boom - an arbitrary write.

### 扩展：
但实际上，我们的 `fast bin` 会进行检查，来进行我们的强行退出。绕过这种防护，我们的方法就是在我们的两个 `fastbin` 之间, 添加一个无关的 `chunck` 即可。
![[Pasted image 20231212224007.png]]

这个时候，如果我们能够 create 一块内容，那么这个内容将被卸载我们的 a 上，然后我们因为 a 被用了两次，所以这个内容也会被作为我们的 `fd` 被写到 a 的前端。
```python
create(p64(0x123456))
```

之后，我们只需要再去申请两次块，把我们的 a 块和 b 块给摘出来，然后我们再去申请的那一个就是我们的需要的元素了。

```python
create(aaa)
create(aaa)
create(p64(system))
```



## 用途：
我们的 double free 可以用于我们的任意地址写，之后，我们就可以去修改我们的 got 表，从而能够执行我们的函数。









