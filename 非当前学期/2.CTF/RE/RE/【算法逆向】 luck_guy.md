首先拖进 exe 里面

![[Pasted image 20240209172906.png]]
可知由 ida 64，然后拖进 ida64
![[Pasted image 20240209172913.png]]

点击 main 进去
![[Pasted image 20240209172920.png]]

根据"try to patch me and find flag"跟进 patch 函数
![[Pasted image 20240209172929.png]]

点击 get_flag 跟进

![[Pasted image 20240209172936.png]]
根据 switch 可知只有 case 1，case 4，case 5有用；

case 1 是由 f 1，f 2 组成 s，点进 f 1 得 GXY{do_not_, 而 f 2 为空。Case 4 是对 f 2 进行赋值，可知 s 为小端模式，转换成字符即"icug `of\x7F"，case5是对f2操作；

编写程序得出 f 2；

```cpp
#include <stdio.h>
int main ()
{
int j;
char key[]="icug`of\x7F";
for (  j = 0; j <= 7; ++j )
        {
          if ( j % 2 == 1 )
            Key[j] -= 2;
          else
            Key[j]-=1;
        }    
         printf ("%s", key);
    }

```

所以本题答案为 GXY{do_not_hate_me};

结合题意最终答案为 flag{do_not_hate_me};
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
                        
原文链接： https://blog.csdn.net/qq_73682634/article/details/129195034