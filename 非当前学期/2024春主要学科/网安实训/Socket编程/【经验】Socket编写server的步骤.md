1. 创建 socket 对象，调用 socket 构造函数：
```
socket=socket.socket(family,type)
```
其中我们的 family 参数代表地址家族，其中，AF_INET 家族包括互联网地址，AF_UNIX 用于同一台机器之间的进程通信。

我们的 type 参数表示我们的套接字类型，可为 SOCK_STREAM(流套接字，TCP)和 SOCK_DGRAM(数据报套接字，UDP)。

2. 将 socket 绑定到指定地址，这是通过 socket 的 bind 函数实现的
```
socket.bind((ip,port))
```
我们的 bind 相当于告诉了我们的服务器端，我们应该去哪一个端口，去找到我们的对应的信息，其中我们的 ip 一般来说可以填写成 localhost，二我们的 port 相对来说比较重要

3. 使用 socket 套接字的 listen 方法接受请求：
```
socket.listen(100)
```
backlog 指定最多允许多少个客户连接到服务器。它的值至少为1。收到连接请求后，这些请求需要排队，如果队列满，就拒绝请求。

4. 服务器接套字通过 accept 方法等待客户端的一个请求：
```
conn,addr=socket.accept()
```
调用我们的 accept 方法的时候，首先要求我们的 socket 已经绑定了一个端口和 ip 地址，socket 会进入 waiting 状态。客户端连接时，方法建立连接并返回服务器。accept 方法会返回一个新的 socket 对象，**就是我们的 conn**。

5. 处理我们的客户端的对应的请求，这里我们常常会用到我们的 `socket.recv(),socket.send()` 函数，并且我们还会采用我们的一个多线程来处理我们的这一个请求。
```python
def clientthread(conn, addr):

    conn.send("Welcome to this chatroom!".encode())

    #sends a message to the client whose user object is conn

    while True:

            try:    

                message = conn.recv(2048)    

                if message:

                    print("<" + addr[0] + "> " + message)

                    message_to_send = "<" + addr[0] + "> " + message

                    broadcast(message_to_send,conn)

                    #prints the message and address of the user who just sent the message on the server terminal

                else:

                    remove(conn)

            except:

                continue

  

def broadcast(message,connection):

    for clients in list_of_clients:

        if clients!=connection:

            try:

                clients.send(message)

            except:

                clients.close()

                remove(clients)

  

def remove(connection):

    if connection in list_of_clients:

        list_of_clients.remove(connection)

```