我们首先入药知道，我们的 FTP 服务器和我们的聊天功能相比，我们在服务端，用户端上有哪些差距，首先，在我们的服务端上，我们有：
1. 我们的服务端对于接收到的所有信息，不应该是直接传递给我们的后续的所有的用户，我们需要对我们的用户按照我们的用户级别来进行分组
2. 我们服务端并不是想所有的用户都展示相同的信息，需要根据我们使用用户不同，来决定我们能够看到的信息是否是相同的。

首先，我们在运行我们的 FTP 服务器时，我们考虑使用我们一个已经存在的非常好用的库：`pyftpdlib`，这个库搭建的 FTP 服务器仍然是 C-S 模型。

对于我们的客户端，我们主要使用我们的 `DummyAuthorizer` 来验证我们的用户，`FTPHandler` 用来运行我们的 FTP 文件。而我们的 ``