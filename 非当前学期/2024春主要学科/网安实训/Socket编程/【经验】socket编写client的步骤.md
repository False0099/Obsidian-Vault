1. 创建一个 socket 以连接服务器
```
socket=socket.socket(family,type)
```

2. 使用 socket 的 connect 方法来连接服务器：
```
socket.connect((host,post))
```

3. 处理阶段：
在这个阶段，我们的客户端和我们的服务端会通过我们的 recv 和 send 方法来进行通信，其中我们的 socket 可以认为同时负责我们的数据传递和我们的数据接收。
```python
 while True:

    sockets_list = [socket.socket(), server]

    read_sockets,write_socket, error_socket = select.select(sockets_list, [], []) #第一个参数是我们的服务器端的socket，第二个是

    for socks in read_sockets:

        if socks == server:

            message = socks.recv(2048) #接受其他用户发送的数据

            print (message)

        else:

            message = sys.stdin.readline()

            server.send(message) #向我们的服务器发送数据

            sys.stdout.write("<You>")

            sys.stdout.write(message)

            sys.stdout.flush()

```

4. 处理结束，我们关闭我们的服务器：
```
socket.close()
```