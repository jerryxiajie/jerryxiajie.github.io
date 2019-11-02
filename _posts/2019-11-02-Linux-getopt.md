---
layout: post
title:  "Linux C 之 getopt() 介绍"
date:   2019-11-01 16:44:17
categories: Linux
tags: Linux C
---

* content
{:toc}

第一篇博客，github + jekyll
介绍 Linux 下 getopt() 的使用





## Linux C 之 getopt 函数


#### 1. 解决的问题 

命令行参数的解析。

```
./pi_desktop -f /root/pi_desktop/demo.bin -v 1 -r

```

例如上面的命令中的:  **-f /root/pi_desktop/demo.bin -v 1 -r** 

getopt() 函数就是用来解析命令行参数的；

调用的一般形式如下：

```

while((c = getopt(argc, argv, "xy:z::")) != -1){
      switch(c){
      case 'x':   ... ...
      case 'y':   ... ...
      case 'z':   ... ...
      ... ....
      }
}
... ....

```

#### 2.函数原型

```

#include <unistd.h>
int getopt(int argc, char * const argv[], const char *optstring);
extern char *optarg; 
extern int optind,  // 初始化值为1，下一次调用getopt时，从optind存储的位置重新开始检查选项。 
extern int opterr,  //初始化值为1，当opterr=0时，getopt不向stderr输出错误信息。
extern int optopt;  //当命令行选项字符不包括在optstring中或者选项缺少必要的参数时,该选项存储在optopt中， getopt返回'？’。

```

#### 3.从 main 函数传入参数

```
int main(int argc,char * argv[])
{
    ...
}

```

**argc**：表示了命令行中参数的个数(注意：文件名本身也算一个参数)，argc的值是在输入命令行时由系统按实际参数的个数自动赋予的。

**argv**：是字符串指针数组，其各元素值为命令行中各字符串(参数均按字符串处理)的首地址。 指针数组的长度即为参数个数。数组元素初值由系统自动赋予。

```

#include <stdio.h>

int main (int argc,char * argv)
{
    printf("argc = %d \n", argc);
    printf("argv = %s \n", argv);
    
    return 0;
}

```
```

gcc test.c -o test

```
```
./test

```

输出：

```

argc = 1
argv = ./test

```

#### 4.处理传入的参数

```

int getopt(int argc, char * const argv[], const char *optstring);

```

前两个参数就是 main 函数传入的参数，第三个参数是选项字符串，他规定了后面可以跟的参数以及参数后面是否可以再接参数。这个选项字符串有特定的书写规则,参数后面带有冒号的就表示这个参数后面还需要再跟参数。没有冒号的就不需要。

```

#include <stdio.h>
#include <unistd.h>
#include <getopt.h>

int main (int argc,char *argv[])
{
        int ch;
        while((ch = getopt(argc,argv,"f:F:v:V:rR")) != -1 )
        {
                switch(ch)
                {
                        case 'f':
                                printf("Param f! \n");
                                printf("Follow param = %s",optarg);
                                break;
                        case 'F':
                                printf("Param F! \n");
                                printf("Follow param = %s",optarg);
                                break;
                        case 'v':
                                printf("Param v! \n");
                                printf("Follow param = %s",optarg);
                                break;
                        case 'V':
                                printf("Param V \n");
                                printf("Follow param = %s",optarg);
                                break;
                        case 'r':
                                printf("Param r! \n");
                                break;
                        case 'R':
                                printf("Param R! \n");
                                break;
                        default:
                                printf("ERROR! \n");
                                break;
                }
        }
        return 0;
}

```

带参数的选项：

```

jerry@ubuntu:~/Desktop$ ./getTest -v 4
Param v! 
Folow param = 4

```

不带参数的选项：

```

jerry@ubuntu:~/Desktop$ ./getTest -r
Param r! 

```