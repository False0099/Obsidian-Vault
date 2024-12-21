所谓的 TCP 服务代理，就是我们作为一个代理，发送方和接收方不能直接通信的时候，我们就可以创建一个代理，协助发送方和接收方来处理我们的数据。（类似于我们的 VPN？）

而协助发送方和接收方处理数据，我们又可以分成下面的几个部分：第一个部分，将发送方的数据发送给我们的接收方。第二个部分，将我们接收方返回的信息返回给我们的接收方。

根据我们上面所说，我们可以写出一个简单的程序：
```python
import socket

import sys

import threading

def receive_from(connection):

    buffer=b""

    connection.settimeout(5)

  

    try:

        while True:

            data=connection.recv(4096)

            if not data:

                break

            buffer+=data

  

    except Exception as e:

        pass

  

    return buffer

  
  

def proxy_func(local_host,local_port,remote_host,remote_port,receive_first):

    local_socket=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

    local_socket.bind(local_host,local_port)

   remote_socket=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

    remote_socket.connect(remote_host,remote_port)

  

    local_send_buffer=receive_from(local_socket)

  

    remote_socket.send(local_send_buffer)

  

    remote_receive_buffer=receive_from(remote_socket)

    local_socket.send(remote_receive_buffer)

    while(True):
```

在这里，我们只是搭好了一个大致的框架，我们还存在着下面的一些问题需要解决：
1. 我们接受和发送的信息如果很大的话，可能不是一次性就能发送的，而我们采用上面的方式，可能会导致部分信息丢失。

解决方案：我们通过设置一个 while 循环来保证我们只有当接受完数据，发送完数据的时候，才会有更新。
```python
def proxy_handler(remote_host,remote_port,receive_first):

    client_socket=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

    client_socket.bind(local_host,local_port)

    client_socket,addr=client_socket.accept()

  

    remote_socket=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

    remote_socket.connect((remote_host,remote_port))

  

    if receive_first:

        remote_buffer=receive_from(remote_socket)

        hexdump(remote_buffer)

  

    remote_buffer=response_handler(remote_buffer)

    if len(remote_buffer):

        print("[<==] Sending %d bytes to localhost." %len(remote_buffer))

        client_socket.send(remote_buffer)

  

    while True:

        local_buffer=receive_from(client_socket)

        if len(local_buffer):

            line=f"[==>] Received {len(local_buffer)}bytes from localhost."

            print(line)

            hexdump(local_buffer)

  

            local_buffer=request_handler(local_buffer)

            remote_socket.send(local_buffer)

            print(f"[<==] Send to localhost .")

  

        remote_buffer=receive_from(remote_socket)

        if len(remote_buffer):

            print(f"[<==] Received {len(remote_buffer)} bytes form remote")

            hexdump(remote_buffer)

  

            remote_buffer=response_handler(remote_buffer)

            client_socket.send(remote_buffer)

            print("[<==] Send to localhost.")

  

        if not len(local_buffer) or not len(remote_buffer):

            client_socket.close()

            remote_socket.close()

            print("[*] No more data. Closing connections.")

            break
```


2. 我们的程序并行效率低。
解决方案：将我们创建 client_socket 的过程下放到我们的外部，这样我们就能实现我们的多线程处理。
```cpp
def server_loop(local_host,local_port,remote_host,remote_port,receive_first):

  

        server=socket.socket(socket.AF_INET,socket.SOCK_STREAM)

        try:

            server.bind((local_host,local_port))

        except Exception as e:

            print('problem on bind :%r'%e)

  

            print("[!!] Failed to listen on %s:%d"%(local_host,local_port))

            print("[!!] Check for other listening sockets or correct permissions.")

            sys.exit(0)

  

        print("[*] Listening on %s:%d"%(local_host,local_port))

        server.listen(5)

  

        while True:

            client_socket,addr= server.accept()

  

            line="> Received incoming connection from %s:%d"%(addr[0],addr[1])

            print(line)

  

            proxy_thread=threading.Thread(target=proxy_handler,args=(client_socket,remote_host,remote_port,receive_first))

            proxy_thread.start()
```