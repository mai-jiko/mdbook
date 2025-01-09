# 第三章 目标文件里有什么

> 《程序员的自我修养--链接、装载与库》第三章

## 3.1 目标文件格式

| ELF文件类型 | 说明 | 实例 |
| :---: | :---: | :---: |
| 可重定位文件 | 包含代码和数据，可以被用来链接成可执行文件或共享目标文件，<br> 静态链接库也可以归为这一类 | Linux的 `.o` <br> windows的 `.obj` |
| 可执行文件 | 包含了可以直接执行的程序 | 比如 `/bin/bash` 文件 <br> windows 的 `.exe` |
| 共享目标文件 | 动态库 | --- |
| 核心转储文件 | 进程意外终止，系统可以将该进程的地址空间的内容以及终止时的一些其他信息转储到核心转储文件按 | Linux 下的 `core dump` |

``` bash
$ file /bin/bash
/bin/bash: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=2f77b36371c214e11670c7d9d92727e9a49f626b, for GNU/Linux 3.2.0, stripped
```

``` bash
$ file /lib64/../lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
/lib64/../lib/x86_64-linux-gnu/ld-linux-x86-64.so.2: ELF 64-bit LSB shared object, x86-64, version 1 (GNU/Linux), static-pie linked, BuildID[sha1]=353e1b6cb0eebc08cf3ff812eae8a51b4efd684e, stripped
```

实例代码 3-1

``` c
int printf(const char* format, ...);

int g_init_var = 84;
int g_uninit_var;

void func_1(int i)
{
    printf("%d\n", i);
}

int main(void)
{
    static int static_var = 85;
    static int static_var2;

    int a = 0;
    int b;

    func_1(static_var + static_var2 + a + b);

    return a;
}

```

``` bash
$ gcc -c simple_section.c
$ objdump -h simple_section.o

simple_section.o:     file format elf64-x86-64

Sections:
Idx Name          Size      VMA               LMA               File off  Algn
  0 .text         00000062  0000000000000000  0000000000000000  00000040  2**0
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, CODE
  1 .data         00000008  0000000000000000  0000000000000000  000000a4  2**2
                  CONTENTS, ALLOC, LOAD, DATA
  2 .bss          00000008  0000000000000000  0000000000000000  000000ac  2**2
                  ALLOC
  3 .rodata       00000004  0000000000000000  0000000000000000  000000ac  2**0
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  4 .comment      0000002c  0000000000000000  0000000000000000  000000b0  2**0
                  CONTENTS, READONLY
  5 .note.GNU-stack 00000000  0000000000000000  0000000000000000  000000dc  2**0
                  CONTENTS, READONLY
  6 .note.gnu.property 00000020  0000000000000000  0000000000000000  000000e0  2**3
                  CONTENTS, ALLOC, LOAD, READONLY, DATA
  7 .eh_frame     00000058  0000000000000000  0000000000000000  00000100  2**3
                  CONTENTS, ALLOC, LOAD, RELOC, READONLY, DATA
```

- `CONTENTS` 表示该段在文件中存在
