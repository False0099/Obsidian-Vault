原理：
我们在程序加了 canary 保护后，如果我们读取的 buffer 覆盖了对应的值，程序就会报错，而一般来说我们不关心报错信息，而我们的 stack smash 技巧就是利用打印信息来得到我们的想要读出的内容。

正常来说，我们的 `__stack_chk_fail` 函数会打印 `argv【0】` 指针指向的字符串。
```cpp
void __attribute__ ((noreturn)) __stack_chk_fail (void)

{

  __fortify_fail ("stack smashing detected");

}

void __attribute__ ((noreturn)) internal_function __fortify_fail (const char *msg)

{

  /* The loop is added only to keep gcc happy.  */

  while (1)

    __libc_message (2, "*** %s ***: %s terminatedn",

                    msg, __libc_argv[0] ?: "<unknown>");

}
```

若通过栈溢出漏洞可修改栈内存中 `argv[0]` 指针，那么触发 Stack smash 时可泄露内存信息。例如把 `argv[0]` 修改为 got 表项可**泄露出内存中函数地址**，为进一步利用提供条件。

步骤：
第一步：