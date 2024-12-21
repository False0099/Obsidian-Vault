## 构造指针
在我们的 php 反序列化中，我们可能会碰到下面的一类问题，我们需要让我们的某一个可以复制的元素等于一个随机的不可预测的元素，这一步，我们就需要通过我们的序列化中的引用来实现。例如：
```php
class{
	public $correct='';
	public $input='';
	public function __destruct(){
		try{
			$this->correct=base64_encode(uniqid());
			if($this->correct===$this->input){
				echo file_get_contents("/flag");
			}
		}
	}
}
```

这个时候，我们就需要构造我们的指针, 引用如下：
```
class Buu{
	$this->input;
	$this->correct;
}
$b=new Buu;
$b->input=&$b->correct;
```

