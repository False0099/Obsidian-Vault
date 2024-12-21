1 . 隐藏我们的编码表：
1.1 打乱我们的编码表：
```
new_table=abcdefgSTUVWXYZ01234hijklmnEF56789+/GHIJKLMNOPQRopqrstuvwxyzABCD

old_table=abcdefghijklmnopqrstuvwxyz......
```

这个时候，我们的思路是，在我们的两种不同顺序编码之间建立一个映射。（双射）

然后，我们再把我们的密文进行直接节目后直接去替换即可。


方法一：暴力枚举
```python
for cha in new_encoded:
	idx=new_table.idx(char)
	old_encoded+=old_table[idx]

plain=base64.b64ecode(old_encoded.encode())
print(plain)
```

方法二：利用库函数
```
res=str.maketrans(new_table,old_table)
new_encoded.translate(res)
```