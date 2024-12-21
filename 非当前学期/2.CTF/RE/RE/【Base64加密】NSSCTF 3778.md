首先，我们查壳发现有一个简单的 UPX 壳，于是我们考虑使用脱壳软件进行脱壳。

之后，我们把脱壳后的结果丢入我们的 IDA 中得到如下所示：
```cpp
__int64 __cdecl main()
{
  int i; // [rsp+2Ch] [rbp-4h]

  _main();
  func(a, encode_a);
  puts("Please input your flag:");
  scanf("%s", s);
  for ( i = 1; i <= 32; ++i )
    f3(s);
  func(s, encode_s);
  if ( !strcmp(encode_s, a) )
    puts("Congratulations! You get the flag");
  else
    puts("Sorry,try again");
  return 0i64;
}
```

```
void __cdecl f3(char *ss_0)
{
  char t; // ST27_1
  int l; // [rsp+28h] [rbp-8h]
  int i; // [rsp+2Ch] [rbp-4h]
  char *ss_0a; // [rsp+40h] [rbp+10h]

  ss_0a = ss_0;
  l = strlen(ss_0);
  for ( i = 0; i < l / 2; ++i )
  {
    t = ss_0a[i];
    ss_0a[i] = ss_0a[l - i - 1];
    ss_0a[l - i - 1] = t;
  }
}
```

```cpp
void __cdecl func(char *x, char *y)
{
  int v2; // eax
  unsigned __int8 *v3; // rax
  unsigned __int8 v4; // cl
  int v5; // ecx
  int v6; // eax
  int v7; // ecx
  int v8; // eax
  int v9; // eax
  int v10; // eax
  unsigned __int8 a4[4]; // [rsp+29h] [rbp-67h]
  unsigned __int8 a3[3]; // [rsp+2Dh] [rbp-63h]
  char b[65]; // [rsp+30h] [rbp-60h]
  int k; // [rsp+7Ch] [rbp-14h]
  int j; // [rsp+80h] [rbp-10h]
  int i; // [rsp+84h] [rbp-Ch]
  int len; // [rsp+88h] [rbp-8h]
  int index; // [rsp+8Ch] [rbp-4h]
  char *xa; // [rsp+A0h] [rbp+10h]
  char *ya; // [rsp+A8h] [rbp+18h]

  xa = x;
  ya = y;
  index = 0;
  len = strlen(x);
  i = 0;
  j = 0;
  k = 0;
  strcpy(b, "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/");
  while ( 1 )
  {
    v2 = len--;
    if ( v2 == 0 )
      break;
    v3 = (unsigned __int8 *)xa++;
    v4 = *v3;
    LODWORD(v3) = i++;
    a3[(signed int)v3] = v4;
    if ( i == 3 )
    {
      a4[0] = a3[0] >> 2;
      a4[1] = (16 * a3[0] & 0x30) + (a3[1] >> 4);
      a4[2] = (4 * a3[1] & 0x3C) + (a3[2] >> 6);
      a4[3] = a3[2] & 0x3F;
      for ( i = 0; i <= 3; ++i )
      {
        v5 = a4[i];
        v6 = index++;
        ya[v6] = b[v5];
      }
      i = 0;
    }
  }
  if ( i )
  {
    for ( j = i; j <= 2; ++j )
      a3[j] = 0;
    a4[0] = a3[0] >> 2;
    a4[1] = (16 * a3[0] & 0x30) + (a3[1] >> 4);
    a4[2] = (4 * a3[1] & 0x3C) + (a3[2] >> 6);
    a4[3] = a3[2] & 0x3F;
    for ( j = 0; i >= j; ++j )
    {
      v7 = a4[j];
      v8 = index++;
      ya[v8] = b[v7];
    }
    while ( 1 )
    {
      v9 = i++;
      if ( v9 > 2 )
        break;
      v10 = index++;
      ya[v10] = 61;
    }
  }
  ya[index] = 0;
}
```

这里，我们不难发现，我们的 f3 函数的功能，就是对我们的一个字符串进行置换，因为我们置换了 32 次，相当于没有置换（乐）

之后，我们再去对我们的一个函数进行 base64 加密。

于是，我们只需要把我们的对应内容进行 base 64 解密即可。