## 属性赋值的三种方法：
1. 直接写
```php
class DEMO1{
	public $func='evil'
	public $argv='phpinfo();'
}
```
缺点：我们使用者一种方法只能赋值我们的字符串

2. 外部赋值：
```php
class DEMO1{
	public $func='evil'
	public $argv='phpinfo();'
}
$o=new DEMO1();
$o->$func="ls";
$o->$argv="ls";
```
缺点：我们使用这一种方法只能构造我们的 public 方法（PHP 7.1 以上的版本中，反序列化对于属性类型不敏感。我们可以直接强制修改为 public 即可）

3. 构造函数问题（推荐）：
```
class DEMO1{
	public $func='evil';
	public $argv='phpinfo()';
	function __construction(){
		$this->func=sb;
		$this->argv=901;
	}
}
```

## 强行调用类内函数

我们想要强行调用我们某一个类内部的函数，需要使用下面的一个语法规则：
```php
class A{
    public f(){
        echo "a"
    }
}
$arr=[new A,"f"];
$arr()
```

执行完后，我们的结果就是：`a`，这是因为我们在这里，我们用一个数组来执行的时候。也就是说，当我们的一个数组被当作函数调用的时候，我们会把我们的第一个参数当作我们的对象，第二个当作我们的方法名字
