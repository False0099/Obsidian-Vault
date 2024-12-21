## 除法转换
除法转换为我们的运算：
思路：
1. 把我们的元素按照我们的 a 属性分组
```
select distinct	A.X
from			A A1
where			not exists()
```

2. 确定我们分组后的属性中是否有一个属性没有在 B.y 中，如果有，返回那一个属性对应的 `B.y`
```
select			B.Y
from			B
where			not exists(
	select			*
	from			A A2
	where			A1.X = A2.X
	and			A2.Y = B.Y
)
```


我们可以朴素的理解为一个三重循环，前两重是我们的限制条件，最后一重是我们的结束条件。
```
select R1.a 
from R1
where not exitst(
	select S.b
	from S
	where not exists(
		select R2.s
		from R2
		where R2.a=R1.a and R2.a=R1.a
	)
)
```