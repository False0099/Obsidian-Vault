在我们的 PHP 5.6 版本中，我们的 intval 不仅仅可以处理我们的十进制数，还可以处理我们的八进制数，以及我们的十进制数字。

那么我们就可以通过**输入一个科学计数法**的数字，然后再去用这个数字去进行处理。
例如：
```
$a='2e5'
echo intval($a)
echo intval($a+1)
$a=2e5
$a+1=2e51
```
