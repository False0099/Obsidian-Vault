## 条件：
1. 我们的 python 开启了我们的 flask debug 模式
2. 能够得到一个 debug 页面，例如我们返回 500 的时候，或者故意让我们的网站报错
3. 得到了我们的 debug pin 码

拥有了上面的三个点，我们就可以获得我们的一个**python shell**。


## PIN 码获得方式
### username
可以从/etc/passwd或者/proc/self/environ环境变量中读取。

### 网卡地址

读取这两个地址：/sys/class/net/eth0/address或/sys/class/net/ens33/address

getattr(mod, 'file', None)

flask目录下的一个app.py的绝对路径,这个值可以在报错页面看到。但有个坑，python3是app.py，python2中是app.pyc

### machine_id ()

linux的id一般存放在`/etc/machine-id`或`/proc/sys/kernel/random/boot_i`

windows 读取注册表中的 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography`

对于 docker 机则读取读取/proc/self/cgroup 获取 get_machine_id()(docker 后面那段字符串)：

但是注意的是 Flask 下的 werzeug 版本在在2020年1月5号就发生了更新，代码发生了变化，因此要再读取到 machine_id()的值的话需要先读取/etc/machine-id，再读取/proc/self/cgroup，并将第一个获取到的值与第二个获取到的 id 值进行拼接。



我们知道，我们想要计算我们的 FLASK PIN, 需要我们的下面几个元素：
1. 我们的用户名，username, 可以通过 `/etc/passwd` 获得或者通过我们的 `/proc/self/environ` 来获得
![[Pasted image 20240503145306.png]]
![[Pasted image 20240503154512.png]]

![[Pasted image 20240503145609.png]]


2. 我们的模块名，默认为 `flask.py`
2. 我们的模块的模块名称，一般默认为：`Flask`
3. app. Py 的绝对路径, 可以通过 `报错` 获得
4. mac 地址，可以通过 `/sys/class/net/eth0/address` 获得，**注意，这里的 0 不是固定的是可以改变的**。`/sys/class/net/eth0/address`
5. machine-id，可以通过 `/etc/machine-id` 获得。

