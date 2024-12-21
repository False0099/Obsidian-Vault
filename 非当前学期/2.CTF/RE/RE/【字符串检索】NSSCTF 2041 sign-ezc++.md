直接进 IDA 看逻辑：

```
int __cdecl main(int argc, const char **argv, const char **envp)
{
  Man *v3; // rbx
  Human *v4; // rbx
  std::string name; // [rsp+20h] [rbp-20h] BYREF
  char v7; // [rsp+37h] [rbp-9h] BYREF
  Human *m; // [rsp+38h] [rbp-8h]

  _main();
  std::allocator<char>::allocator(&v7);
  std::string::string(&name, "NISACTF", &v7);
  v3 = (Man *)operator new(0x18ui64);
  Man::Man(v3, (std::string)&name, 4);
  m = v3;
  std::string::~string(&name);
  std::allocator<char>::~allocator(&v7);
  (*((void (__fastcall **)(Human *))m->_vptr_Human + 1))(m);
  v4 = m;
  if ( m )
  {
    Human::~Human(m);
    operator delete(v4);
  }
  return 0;
}
```

1  
2  
3  
4  
5  
6  
7  


对`Human`这个命名空间进行搜索，发现`Human::give_flag`这个函数：

```
void __cdecl Human::give_flag(Human *const this)
{
  int i; // [rsp+2Ch] [rbp-54h]

  for ( i = 0; i < strlen(flag); ++i )
    flag[i] ^= 0xAu;
}
```



并在内存中发现：

```
.data:000000000046A020 flag            db 44h, 2 dup(59h), 49h, 5Eh, 4Ch, 71h, 7Eh, 62h, 63h
.data:000000000046A020                                         ; DATA XREF: Human::give_flag(void)+21↑o
.data:000000000046A020                                         ; Human::give_flag(void)+37↑o ...
.data:000000000046A020                 db 79h, 55h, 63h, 79h, 55h, 44h, 43h, 59h, 4Bh, 55h, 78h
.data:000000000046A020                 db 6Fh, 55h, 79h, 63h, 6Dh, 64h, 77h, 14h dup(0)
```



编写脚本解密：

```
s=[0x44,0x59,0x59,0x49,0x5E,0x4C,0x71,0x7E,0x62,0x63,0x79,0x55,0x63,0x79,0x55,0x44,0x43,0x59,0x4B,0x55,0x78,0x6F,0x55,0x79,0x63,0x6D,0x64,0x77,0x14]
for letter in s:
    print(chr(letter^0xA),end="")
>>> NSSCTF{this_is_NISA_re_sign}
```