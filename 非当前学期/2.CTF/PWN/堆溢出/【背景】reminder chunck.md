Reminder chunck 是一种特殊的机制，使得用户能够将我们能够充分利用我们的内存，
当我们用户想要申请一个新的空间的时候，首先，如果我们的大小较小会从我们的 `fast bin` 中申请，如果空间较大，我们会从我们的 `unordered bin` 中申请，并且归类，然后再在我们的 `large bin` 中寻找。
我们的故事就从我们的 `unordered bin` 开始，我们如果出现了我们的一个大小大于我们所需要的空间的时候，我们的多余的空间会变成一个新的 `chunck`,这个就是我们的 `last reminder chunck`,然后这个 `chunck` 就可以由我们的系统再去管理。
```cpp
#include <bits/stdc++.h>
int main(){
	fprintf(stderr, "Thts file doesn't demonstrate an attack, but shows the nature of gltbc's allocator.\n" );
	fprintf(stderr,"glibc uses a first-fit algorithm to select a free chunk.\n");
	 fprintf(stderr,"If a chunk ts free and large enough, malloc will select thts chunk.\n");
	 fprintf(stderr,"This can be exploited in a use-after-free situation,\n");
	 fprintf(stderr, "Allocating 2 buffers. They can be large, don't have to be fastbin.\n");
	 char*a=malloc(0x512);
	 char*b=malloc(0x256);
	 char* c;
	 fprintf(stderr,"1st malloc(0x512): %p(n" a);
 fprintf(stderr, "2nd malloc(0x256): %p\n" b); fprintf(stderr,"we could continue mallocing here...\n")
 fprintf(stderr, "now let's put a string at a that we can read later \"this is All\"\n");
 strcpy(a,"this is A!");
 fprintf(stderr, "first allocation %p points to %s\n", a, a);
 fprintf(stderr,"Freeing the first one$\ldots$\"
 free(a);
 fprintf(stderr,"we don't need to free anything again. As long as we allocate smaller than Ox512, it will end up at %p\n", a);
 fprintf(stderr,"so, let's allocate Ox500 bytes\"
 $c=malloc(0x500);$
 fprintf(stderr,"3rd malloc(0x500):%p(n^n,c);
 fprintf(stderr, "And put a different string here, \"this is Cl\"\"

}
 

 
```