**POST 传参不能够传毒%ff 之类的非可见字符**, 否则会出现
![[Pasted image 20240424091812.png]]
提示

## 传递非法参数
在 php 中变量名只有数字字母下划线，被 [get](https://so.csdn.net/so/search?q=get&spm=1001.2101.3001.7020) 或者 post 传入的变量名，如果含有 `空格、+、.、[ ` 则会被转化为_，但 php 中有个特性就是如果传入 `[`，它被转化为_之后，后面的字符就会被保留下来不会被替换。因此我们可以构造出来该变量名咯。

diao_s.i =》diao_s_i（也就是 PHP 网页用 diao_s_i 可以正常接收 diao_s.i 传递过来的值，中间会有个[自动转换](https://so.csdn.net/so/search?q=%E8%87%AA%E5%8A%A8%E8%BD%AC%E6%8D%A2&spm=1001.2101.3001.7020)的过程

`diao[s.i => diao_s.i`，利用上面的 `[` 经过一次转换后可以正常接收

