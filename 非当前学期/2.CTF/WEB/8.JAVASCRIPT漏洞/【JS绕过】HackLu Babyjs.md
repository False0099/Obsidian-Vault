## JS 黑魔法
要求：要求一个整数的负大数次方为 0，负数则为 -0.
```js
assert('1,1',a===b);
assert('1.2',1337/a!=1337/b);
```

这里，我们的可以通过我们的一个传奇的方法，因为 js 中，我们的 `0` 是区分正负的。于是，我们可以构造 `a=1145141919810`, `b=-1145141919810`


要求：
```js
let {c,d}=json;
isnt(c,'undefined');
isnt(d,'undefined');
const cast=(f,...a)=>a.map(f);
[c,d]=cast(Number,c,d);
assert('2.1',c!==d);
[c,d]=cast(String,c,d);
assert('2.2',c===d);
```

这里我们可以利用我们的 `Nan` 和  `NaN` 不相等来进行处理。我们构造：
`c=NaN,d=NaN`

要求：
```json
let{e}=json;
is(e,'number');
const iscorrect=e++<e--&&!e++<!e--;
assert('3',isCorrect);
passed('3');
```

构造：首先，我们的 `e++<e--` 肯定是满足的，我们作比较是 true 为 1，false 为 0，于是我们著需要构造 `e=-1` 即可

要求：
```
assert(f=!f);
```

构造：我们的任何的非空字符串都为真，js 中两个等号为弱类型判断
```
f=false
```

要求：
```json
is(h,'Number');
try:
	json.parse(String(h));
catch:
	111
```

构造：
我们将我们的大数设置为一个很大的数字，然后将我们的对应的数字就会变成我们的 Infinity，从而让我们能够处理对应的数字。产生对应的报错


要求：
```json
assert(a in ['a'])
```

构造：
这里，我们的 in 运算，并不是我们的常见的 in 运算，而是一种其他的理解，in 运算对于数组来说，是判断数组的 key （下表）是否再。

要求：
```
const {y,z}=json;
isnt(y,'undefined');
isnt(z,'undefined');
y[y][y][z]()(FLAG);
```

构造：
```
''['constructor']['constructor']("console.log(123)")()
```

