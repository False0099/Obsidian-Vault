## 概要：
该方法通过上传后缀为 `jpg,114514` 的文件，然后再上传 `.htaccess` 文件，从而将我们的 jpg 文件按照 php 方法进行解析，从而完成我们的绕过：

## 原理
htaccess 是 **apache 服务器**中的一个配置文件，她负责相关目录下的网页配置，通过 htaccess 文件，可以帮助我们实现：网页 301 重定向，自定义 404 错误页面，改变文件扩展名、允许、阻止特定用户或者目录的访问

生效需要两个条件
1. 在 apache 的配置文件中写上：`allowOverride ALL`（给了用户写的权限）
2. Apache 要加载 `mod_Rerwite` 模块（给了用户写的权限）

通过我们的这一个文件，我们就可以把我们原本上传的 `.jpg` 或者其他当作我们的 `.php` 来进行解析，导致我们虽然上传的看似是一个图片，实际上是一个木马。

## 判断
1. 当我们的代码出现下面的情况时，我们考虑使用我们的 `.htacess` 漏洞来进行
```cpp
$is _upload=false;
$msg=null;
if(isset($_POST['submit'])){//检查我们有没有点击我们的上传按钮(原理在于表单可以上传post数据)
	if(file_exists(UPLOAD_PATH)){//判断位置是否存在
		$deny_ext=array('.asp','.aspx','.php','.jsp','.php2'....);
		$file_name=trim($FILES['upload_file']['name']);
		$file_name=deldot($file_name);//删除末尾的点
		$file_ext=strrchr($file_name,'.');
		$file_ext=strtolower($file_ext);//转换为小写
		$file_ext=str_ireplace('::$DATA','',$file_ext)//去除字符串::$DATA
		$file_ext=trim($file_ext);//去空
		if(!in_array($file_ext,$deny_ext)){
			$temp_file=$_FILES['upload_file']['tmp_name'];
			$img_path=UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;
			if(move_uploaded_file($tmp_file,$img_path)){
			$is_upload=true;
			}
		}
}
```

操作：
1. 我们直接上传一个. Htaaccess 文件，在我们的 `.htaccess` 中，我们写入下面的内容：
```text
AddType application/x-httpd-php .(你所伪装的后缀名)
```
![[Pasted image 20231117210649.png]]
2. 上传我们的 `.htaccess` 文件
![[Pasted image 20231126211547.png]]
3. 如果出现 `forbidden`，就说明我们已经上传成功了。
![[Pasted image 20231126211638.png]]
4. 上传我们的对应的伪装后的木马
![[Pasted image 20231126211728.png]]
5. 之后，我们打开对应网站，检查我们是否是正常工作的
![[Pasted image 20231126211824.png]]


