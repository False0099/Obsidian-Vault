## 简介
## 一、**原理解析**

off by null漏洞，顾名思义就是溢出了一个空字节，核心是让其堆块的prev inuse位溢出为0，从而认为它的低地址堆块处于了free状态，然后加以利用。  

主要发生在_int_free的unlink中：

```text
//2.23 when size>global_max_fast
 
/* consolidate backward */
if (!prev_inuse(p)) {
    prevsize = p->prev_size;
    size += prevsize;
    p = chunk_at_offset(p, -((long) prevsize));
    unlink(av, p, bck, fwd);
}
 
 
if (nextchunk != av->top) {
    /* get and clear inuse bit */
    nextinuse = inuse_bit_at_offset(nextchunk, nextsize);
 
    /* consolidate forward */
    if (!nextinuse) {
        unlink(av, nextchunk, bck, fwd);
        size += nextsize;
    }
 
 
 
/* Take a chunk off a bin list */
#define unlink(AV, P, BK, FD) {                                            
    FD = P->fd;                                      
    BK = P->bk;                                      
    if (__builtin_expect (FD->bk != P || BK->fd != P, 0))              
        malloc_printerr (check_action, "corrupted double-linked list", P, AV);  
    else {                                      
        FD->bk = BK;                                  
        BK->fd = FD;                                  
        if (!in_smallbin_range (P->size)                      
            && __builtin_expect (P->fd_nextsize != NULL, 0))
        {              
            if (__builtin_expect (P->fd_nextsize->bk_nextsize != P, 0)          
                || __builtin_expect (P->bk_nextsize->fd_nextsize != P, 0))    
                malloc_printerr (check_action,                      
                                 "corrupted double-linked list (not small)",    
                                 P, AV);                          
            if (FD->fd_nextsize == NULL) {                      
                if (P->fd_nextsize == P)                      
                    FD->fd_nextsize = FD->bk_nextsize = FD;              
                else {                                  
                    FD->fd_nextsize = P->fd_nextsize;                  
                    FD->bk_nextsize = P->bk_nextsize;                  
                    P->fd_nextsize->bk_nextsize = FD;                  
                    P->bk_nextsize->fd_nextsize = FD;                  
                }                                  
            } else {                                  
                P->fd_nextsize->bk_nextsize = P->bk_nextsize;              
                P->bk_nextsize->fd_nextsize = P->fd_nextsize;              
            }                                      
        }                                      
    }
}
```

  

为了方便，依据物理地址相邻来命名如下：

![](https://pic3.zhimg.com/80/v2-874048adf7f5d84f18edbbcb657834c2_720w.webp)

  

即当size大于global_max_fast且不是mmap出来的chunk时，就会进入判断。所以这里我们进行释放用的chunk的大小就必须要大于global_max_fast才行，否则就是改掉了pre_inuse位也是直接进入fastbin，不会进入判断的。

先依据当前chunk(chunkP)的pre_inuse位来**判断前一个chunk(preChunk)是否处于释放状态**，是则进入unlink，将前一个chunk取出。

然后判断下一个chunk(nextChunk)是否是top_chunk，是则直接与top_chunk合并。

若nextChunk不为top_chunk，再判断下一个Chunk的再下一个chunk的pre_inuse位来判断nextChunk是否处于释放状态，若是则进入unlink。

然后unlink中就不细说，就是双向循环链表解链的过程，依据fd和bk来查找并解链，但是我们的off-by-null通常不会涉及到nextsize位的使用，所以基本不用看后面的。需要注意的是，由于这里会检查，即：

```text
if (__builtin_expect (FD->bk != P || BK->fd != P, 0))              
    malloc_printerr (check_action, "corrupted double-linked list", P, AV);
```

  

所以我们需要将进入unlink的chunk的fd和bk来进行伪造或者干脆直接释放使其直接进入unsortedbin中完成双向链表的加持。这里先讲放入unsortedbin中来获取fd和bk的方法，伪造的方法一般用在2.29及以上的高版本中，因为那时候的unlink加入了关于size位的检查，不能简单得伪造fd和bk。其次，这里还需要明白一个寻找chunk的原理。

寻找preChunk：preChunk_addr = chunkP_addr - chunkP->pre_size

寻找nextChunk：nextChunk_addr = chunkP_addr + chunkP->size

即以下源码，这个一直没有变化过：

```text
/* Ptr to previous physical malloc_chunk.  Only valid if !prev_inuse (P).  */
#define prev_chunk(p) ((mchunkptr) (((char *) (p)) - prev_size (p)))
 
/* Ptr to next physical malloc_chunk. */
#define next_chunk(p) ((mchunkptr) (((char *) (p)) + chunksize (p)))
 
/* Get size, ignoring use bits */
#define chunksize(p) (chunksize_nomask (p) & ~(SIZE_BITS))
 
/* extract p's inuse bit */
#define inuse(p)                                  \
  ((((mchunkptr) (((char *) (p)) + chunksize (p)))->mchunk_size) & PREV_INUSE)
```

  

所以，如果我们可以**伪造pre_size和in_use位，就能触发向上任意寻找一个满足fd和bk为双向链表的chunk**，从而将**中间所有的chunk都一并合并为一个Chunk**释放掉。(向下合并也可以的，不过一般不常使用)

![](https://pic4.zhimg.com/80/v2-720cd41ed75dd074ad4d0ca0d3a332d7_720w.webp)

这里就是通过释放chunkP，依据pre_size向上寻找到原本已经在unsortedbin中的preChunk，其FD和BK已经组成双向循环链表，可以绕过检查，所以释放ChunkP之后preChunk+OverlapChunk+chunkP都进入到unsortedbin中。

但是OverlapChunk本身其实并没有被释放，我们再从unsortedbin中申请切割出preChunk大小的chunk，再申请就可以得到OverlapChunk。这样我们就有两个指针都指向OverlapChunk，从而伪造出UAF，之后我们就可以通过OverlapChunk来getshell了。

  

**1、常用布局**
首先我们要用到四个 chunk（我们只利用三个 chunk，高地址的那个 chunk 是防止和 top chunk 合并的）

```python
chunk 0#merged chunk     （不能让这个堆块在fastbin或是tcachebin中）
chunk 1#overflow chunk&&spy chunk
chunk 2#merge chunk      （不能让这个堆块在fastbin或是tcachebin中）
chunk 3#prevent merge chunk
```


```text
add_malloc(0xf8,'\x00'*0xf8)    #0x1
add_malloc(0x68,'\x00'*0x68)    #0x2
add_malloc(0xf8,'\x00'*0xf8)    #0x3
add_malloc(0x68,'\x00'*0x68)    #0x4
free(0x1)
edit(0x2,0x70,'\x00'*0x60+p64(0x70+0x100)+p16(0x100))
free(0x3)
```

  

off-by-null在调试中不太好搞，所以我就借用堆溢出来假设存在off-by-null，将chunk3原本的size位0x101通过off-by-null变成0x100即可。

  

**2、注意事项**

  

(1)顺序

此外需要注意的是，需要先释放chunk1，再溢出修改chunk3。不然如果先修改chunk3，那么释放chunk1的时候，寻找chunk1的nextChunk即chunk2，判断chunk2是否处于释放状态时，会找到chunk3，依据pre_inuse位发现chunk2已经处于释放状态，那么尝试进入unlink合并，但是这里的chunk2的fd和bk并没有组成双向循环链表，所以会出错。

  

(2)size位的设置

0x100：这里注意到上面的布局中size位为0x100和0x70，这里的0x100就是为了通过off-by-null将0x101变成0x100设置的。当然设置为0x201，0x301通常也是一样的。

0x70：这里就通常是为了方便打 fastbin attack，从_malloc_hook 处构造0x7f 字节错位用的。

## 操作脚本
```python
create(0,0x80)
create(1,0x60)
create(2,0x18)
create(3,0xf0)
create(4,0x10)
delete(0)
edit(2,p64(0)*2+p64(0x120)+p8(0))
#伪造prevsize和末尾0
delete(3)

create(6,0x210)
show(6)
```