# C 语言

## 第一个C语言程序

```c
#include <stdion.h>

int main()
{
    // 我的第一个C语言程序
    printf("Hello World!\n");
    return 0;
}
```

==编译==

- gcc hello.c	----> 编译.c文件，在windows下自动生成a.exe，在Linux下自动生成a.out
- gcc hello.c -o hello.exe    ----> 编译.c文件，并制定生成的文件名
- gcc -o hell hello.c

==运行==

* windows下运行: a.exe
* Linux下，指向用户编译的可执行程序，如果没有配置环境，当前路径前面加`./`: ./a.out