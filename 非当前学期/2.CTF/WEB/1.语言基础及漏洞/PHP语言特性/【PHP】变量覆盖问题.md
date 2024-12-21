## 功能：
我们的变量覆盖函数本来的功能是：把我们的某一个数组直接转换为我们的一系列变量，因为我们的 php 数组本质上是一个 map，所以我们的上述操作是可行的。

**但是，如果我们对应的用户名已经存在了，那么我们要怎么办?** 这个时候，我们的就会**直接覆盖**我们原来就有的变量，所以我们的这一个问题就叫做我们的变量覆盖问题。

相关函数：
```
Extract()
Parse_str()
Import_requests_variables()//decrepete
```

## Extract 函数
### 样例 ：
```
$a="Original";
var='$a=1';
extract($var);
```

### 常见形式：

**学习样例：

```
<?php
extract($_GET);  

echo $name.'<br>';
echo $age.'<br>';
echo $phone.'<br>';

//GET传参:?name=xiaohua&age=22&phone=112323123

//结果:
// xiaohua
// 22
// 112323123
?>
```

```
$test=xxxx;
$content=yyyy;
extract($_GET);
if($test==$content){
	echo flag;
}
```

对应的 payload
```
?test=&content=
```

### 罕见形式：
如果我们给我们的 extract 当中传入的是我们的如下信息：
```cpp
template=array();
if(isset($_GET['var'])&&is_array($_GET['var'])){
	extract($GET['var'],EXTR_OVERWRITE);
}else{
	highlight_file(__file__);
	die();
}
```

并且我们的**目的是修改我们的 template 中的变量**, 那么我们可以通过我们的下面的语法来实现：
```
?var[template][tp1]=template.php&tp=tp1
```

分层解析：
```
var[template]=$c 定义了我们的var是一个叫做template的数组

$c[tp1]=template.php 表明我们的这一个字典中我们的tp1对应的是template.php
```



## `$$` 形式：
我们上面的这一种形似也可以叫做我们的动态变量。PHP 动态变量是指一个变量名的变量名可以动态的设置和使用，一个变量获取另一个变量的值作为这个变量的变量名。（有点类似于我们 C 语言中的指针）

`$$` 导致的变量覆盖问题在CTF代码审计题目中经常在foreach中出现，如以下的示例代码，
```
foreach ($_GET as $key => $value)
  $$key = $value;   //这里进行了覆盖 $$key传入的值是name 传入进入成为$name 所以造成了name外部的变量被覆盖
  var_dump($key);
  var_dump($value);
  var_dump($$key);
```
我们首先就是把我们的 `$_GET` 拆成了若干个键值对，就是我们的 `key,value`,然后我们的 `$$key` 就是把我们的 $key 的值作为我们的名字，产生一个对应的变量，我们对应的变量值为 `value`

### 利用方法：
我们如果让我们的 `GET` 参数中的某一个键值等于我们已经出现过的变量，那么我们就有可能会覆盖了我们之前的那一个变量。例如
```
?name=test
```
就会将$name 的值覆盖，变为 test。


## parse_str 形式：
parse_str() 函数把查询字符串解析到变量中。**如果未设置 array 参数，由该函数设置的变量将覆盖已存在的同名变量。**

例如：
```
<?php
parse_str("name=xiaohua&age=22");
echo $name."<br>";
echo $age;
?>
//xiaohua
//22
```

