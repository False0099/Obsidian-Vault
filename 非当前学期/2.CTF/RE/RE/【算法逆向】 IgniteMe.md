## Exeinfo

![](https://pic2.zhimg.com/80/v2-c248d32939f6355cd8cd4a91e9d677dd_720w.webp)

当爷放在ida中瞬间心情不好，又要一个一个的去看Microsoft docs了

---------------正题-------------

```c
void __noreturn start()
{
  DWORD NumberOfBytesWritten; // [esp+0h] [ebp-4h] BYREF

  NumberOfBytesWritten = 0;
  hFile = GetStdHandle(0xFFFFFFF6);
  dword_403074 = GetStdHandle(0xFFFFFFF5);
  WriteFile(dword_403074, aG1v3M3T3hFl4g, 0x13u, &NumberOfBytesWritten, 0);
  sub_4010F0(NumberOfBytesWritten);
  if ( sub_401050() )
    WriteFile(dword_403074, aG00dJ0b, 0xAu, &NumberOfBytesWritten, 0);
  else
    WriteFile(dword_403074, aN0tT00H0tRWe7r, 0x24u, &NumberOfBytesWritten, 0);
  ExitProcess(0);
}
```

- GetStdHandle检索指定标准设备的句柄
- WriteFile将数据写入指定的文件或输入/输出 (I/O) 设备。

这里的writeFile的话其实就是输出aG1v3M3T3hFl4g和aG00dJ0b和aN0tT00H0tRWe7r这些字符串到屏幕上

进入sub_4010f0函数

```c
int sub_4010F0()
{
  unsigned int v0; // eax
  char Buffer[260]; // [esp+0h] [ebp-110h] BYREF
  DWORD NumberOfBytesRead; // [esp+104h] [ebp-Ch] BYREF
  unsigned int i; // [esp+108h] [ebp-8h]
  char v5; // [esp+10Fh] [ebp-1h]

  v5 = 0;
  for ( i = 0; i < 0x104; ++i )
    Buffer[i] = 0;
  ReadFile(hFile, Buffer, 0x104u, &NumberOfBytesRead, 0);
  for ( i = 0; ; ++i )
  {
    v0 = sub_401020(Buffer);
    if ( i >= v0 )
      break;
    v5 = Buffer[i];
    if ( v5 != 10 && v5 != 13 )
    {
      if ( v5 )
        byte_403078[i] = v5;
    }
  }
  return 1;
}
```

这个函数没有什么用。就是将\n和\r去掉，10对应的\n 13对应的\r ，剩下的自行去查api文档即可。

接下来会调用sub_401050()这个函数如果返回值不为0则right，跟进

```c
int sub_401050()
{
  int v1; // [esp+0h] [ebp-Ch]
  int i; // [esp+4h] [ebp-8h]
  unsigned int j; // [esp+4h] [ebp-8h]
  char v4; // [esp+Bh] [ebp-1h]

  v1 = sub_401020(byte_403078);
  v4 = sub_401000();
  for ( i = v1 - 1; i >= 0; --i )
  {
    byte_403180[i] = v4 ^ byte_403078[i];
    v4 = byte_403078[i];
  }
  for ( j = 0; j < 0x27; ++j )
  {
    if ( byte_403180[j] != (unsigned __int8)byte_403000[j] )
      return 0;
  }
  return 1;
}
```

这个函数看上去结构清晰，v4给了值，然后将数据与v4进行异或，最后的值与403000比较，全都正确即可。

结果有了

![](https://pic2.zhimg.com/80/v2-ba32264fdb1f8b0a6f7f3f53e4901ae9_720w.webp)

倒过来异或就可以了。v4这个定值可以动态调试出来也可以写代码手撸。

![](https://pic3.zhimg.com/80/v2-a974d03a132134809828427fc42b70c6_720w.webp)

这里笔者用的动态调试，输入值为aaaaaa，然后调试到v4这里最后结果为4

v4也知道了写脚本即可。

```python
str1 = [0x0D,0x26,0x49,0x45,0x2A,0x17,0x78,0x44,0x2B,0x6C,0x5D,0x5E,0x45,0x12,0x2F,0x17,
        0x2B,0x44,0x6F,0x6E,0x56,0x09,0x5F,0x45,0x47,0x73,0x26,0x0A,0x0D,0x13,0x17,0x48,
        0x42,0x01,0x40,0x4D,0x0C,0x02,0x69]
v4 = 4

final = []

for i in range(len(str1)-1, -1, -1):
    final.append(str1[i] ^ v4)
    v4 = final[-1]
flag = ''
for i in range(len(final)):
    flag += chr(final[i])

print(flag[::-1])
```


或者：

`flag{R_y0u_H0t_3n0ugH_t0_1gn1t3@flare-on.com}`