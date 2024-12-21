我们这一题的关键在于下面的一个片段：
```cpp
memset(qword_202040, 0, sizeof(qword_202040));
  v8 = 0;
  v7 = 0;
  dest = 0LL;
  while ( !*a1 || *a1 != '\n' && (*a1 != '\r' || a1[1] != '\n') )
  {
    if ( v8 <= 5 )
      qword_202040[2 * v8] = a1;
    sb = strchr(a1, ':');
    if ( !sb )
    {
      puts("error.");
      exit(1);
    }
    *sb = 0;
    for ( sc = sb + 1; *sc && (*sc == ' ' || *sc == '\r' || *sc == '\n' || *sc == '\t'); ++sc )
      *sc = 0;
    if ( !*sc )
    {
      puts("abort.");
      exit(2);
    }
    if ( v8 <= 5 )
      qword_202040[2 * v8 + 1] = sc;
    sd = strchr(sc, '\n');
    if ( !sd )
    {
      puts("error.");
      exit(3);
    }
    *sd = 0;
    a1 = sd + 1;
    if ( *a1 == '\r' )
      *a1++ = 0;
    s1 = (char *)qword_202040[2 * v8];
    nptr = (char *)qword_202040[2 * v8 + 1];
    if ( !strcasecmp(s1, "opt") )
    {
      if ( v7 )
      {
        puts("error.");
        exit(5);
      }
      v7 = atoi(nptr);
    }
    else
    {
      if ( strcasecmp(s1, "msg") )
      {
        puts("error.");
        exit(4);
      }
      if ( strlen(nptr) <= 1 )
      {
        puts("error.");
        exit(5);
      }
      v9 = strlen(nptr) - 1;
      if ( dest )
      {
        puts("error.");
        exit(5);
      }
      dest = calloc(v9 + 8, 1uLL);
      if ( v9 <= 0 )
      {
        puts("error.");
        exit(5);
      }
      memcpy(dest, nptr, v9);
    }
    ++v8;
  }
  *a1 = 0;
  sa = a1 + 1;
  if ( *sa == 10 )
    *sa = 0;
  switch ( v7 )
  {
    case 2:
      sub_DA8((const char *)dest);              // send shellcode
      break;
    case 3:
      sub_EFE((const char *)dest);
      break;
    case 1:
      sub_CBD((const char *)dest);              // get root
      break;
    default:
      puts("error.");
      exit(6);
  }
```

这一段话我们的结论就是：只有当我们的格式为：
```
'opt:1\n'+'msg:xxxx\r\n'
```

于是，我们可以考虑把我们的字符串写入到这个里面，然后就能得到我们的结果。

