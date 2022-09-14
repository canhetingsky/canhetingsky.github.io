---
title: C语言柔性数组
tags:
  - C语言
categories:
  - 编程
keywords:
  - C语言
  - 柔性数组
abbrlink: 5c4a1bb8
date: 2022-09-14 22:18:24
---

> 结构中最后一个元素允许是未知大小的数组，这个数组就是`柔性数组`。

结构中的柔性数组前面必须至少一个其他成员，柔性数组成员允许结构中包含一个大小可变的数组，`sizeof`返回的这种结构大小不包括柔性数组的内存。包含柔数组成员的结构用`malloc`函数进行内存的动态分配，且分配的内存应该大于结构的大小以适应柔性数组的预期大小。柔性数组到底如何使用？

## 不完整类型

C和C++对于不完整类型的定义是一样的，不完整类型是这样一种类型，它缺乏足够的信息（例如长度）去描述一个完整的对象。

不完整类型举例：

前向声明就是一种常用的不完整类型

```c
struct test; //test 只给出了声明，没有给出定义
```

不完整数据类型必须通过某种方式补充完整，才能使它们进行实例化，否则只能用于定义指针或引用，因为此时实例化的是指针或引用本身，不是base和test对象

一个未知长度的数组也属于不完整类型：

```c
extern int a[];
```

extern关键字不能去掉，因为数组的长度未知，不能作为定义出现。不完整类型的数组需要补充完整才能使用。不完整类型的数组可以通过几种方式补充完整，大括号形式的初始化就是其中的一种方式：

```c
int a[] = {10, 20};
```

## 结构体

首先，我们需要知道，所谓变量，其实是内存地址的一个抽像名字罢了。在静态编译的程序中，所有的变量名都会在编译时被转成内存地址。机器是不知道我们取的名字的，只知道地址。

所以就有了**栈内存区**、**堆内存区**、**静态内存区**、**常量内存区**，我们代码中的所有变量都会被编译器预先放到这些内存区中。

有了上面这个基础，我们来看一下结构体中的成员的地址是什么？我们先简单化一下代码：

```c
struct test{
    int i;
    char *p;
};
```

上面代码中，test结构中i和p指针，在C的编译器中保存的是相对地址，也就是说，它们的地址是相对于`struct test`的实例的。如果我们有这样的代码：
下面做个实验：

```c
#include <stdio.h>

struct test{
    int i;
    char *p;
};

int main(void)
{
    struct test t;
    printf("%p\n", &t);
    printf("%p\n", &(t.i));
    printf("%p\n", &(t.p));
    return 0;
}
```

{% note info modern %}

格式控制符`%p`中的p是pointer（指针）的缩写。`printf`函数中`%p`是打印地址（指针地址）的，是十六进制的形式，但是会全部打完，即有多少位打印多少位，附加前缀0x。

{% endnote %}

运行结果:

![](https://media.canheting.cn/img/202209142354670.png)

我们可以看到，`t.i`的地址和`t`的地址是一样的，`t.p`的址址相对于t的地址多了个8。说白了，`t.i`其实就是`(&t + 1*0)`,`t.p`的其实就是`(&t + 1*8)`。`1*0`和`1*8`这个偏移地址就是成员`i`和`p`在编译时就被编译器给hard code了的地址。于是，你就知道，不管结构体的实例是什么——访问其成员其实就是加成员的偏移量。

下面再来做个实验：

```C
#include <stdio.h>

struct test{
    int i;
    short c;
    char *p;
};

int main(void)
{
    struct test *pt=NULL;
    printf("%p\n", &(pt->i));
    printf("%p\n", &(pt->c));
    printf("%p\n", &(pt->p));
    return 0;
}
```

运行结果:

![](https://media.canheting.cn/img/202209142354827.png)

注意：上面的`pt->p`的偏移之所以是`0*8`而不是`0*6`，是因为内存对齐了（我在64位系统上）。关于内存对齐，可参看《C语言内存对齐详解》一文。

## 柔性数组

柔性数组成员（flexible array member）也叫伸缩性数组成员，这种代码结构产生于对动态结构体的需求。在日常的编程中，有时候需要在结构体中存放一个长度动态的字符串，一般的做法，是在结构体中定义一个指针成员，这个指针成员指向该字符串所在的动态内存空间，例如：

```c
struct s_test
{
    int a;
    double b;
    char* p;
};
```

p指向字符串，这种方法造成字符串与结构体是分离的，不利于操作。把字符串和结构体连在一起的话，效果会更好，可以修改如下：

```C
char a[] = "Hello world";
struct s_test *ptest = (struct s_test*)malloc(sizeof(s_test)+streln(a)+1);
strcpy(ptest+1,a);
```

这样一来，`(char*)(ptestt + 1)`就是字符串“hello world”的地址。这时候p成了多余的东西，可以去掉。但是，又产生了另外一个问题：老是使用`(char*)(ptestt + 1)`不方便。如果能够找出一种方法，既能直接引用该字符串，又不占用结构体的空间，就完美了，符合这种条件的代码结构应该是一个非对象的符号地址，在结构体的尾部放置一个0长度的数组是一个绝妙的解决方案。不过，C/C++标准规定不能定义长度为0的数组，因此，有些编译器就把0长度的数组成员作为自己的非标准扩展，例如：

```c
struct s_test2
{
    int a;
    double b;
    char c[0];
};
```

c就叫柔性数组成员，如果把ptest指向的动态分配内存看作一个整体，c就是一个长度可以动态变化的结构体成员，柔性一词来源于此。c的长度为0，因此它不占用test的空间，同时`ptest->c`就是“hello world”的首地址，不需要再使用`(char*)(ptestt + 1)`这么丑陋的语法了。

鉴于这种代码结构所产生的重要作用，C99 甚至把它收入了标准中：

> As a special case, the last element of a structure with more than one named member may have an incomplete array type; this is called a flexible array member.

C99使用不完整类型实现柔性数组成员，标准形式是这样的：

```c
struct s_test
{
  int a;
  double b;
  char c[];
};
```

c同样不占用test的空间，只作为一个符号地址存在，而且必须是结构体的最后一个成员。柔性数组成员不仅可以用于字符数组，还可以是元素为其它类型的数组，例如：

```c
struct s_test
{
    int a;
    double b;
    float[];
};
```

首先，我们要知道，0长度的数组在ISO C 和C++的规格说明书中是不允许的。这也就是为什么在VC++2012下编译你会得到一个警告：“arning C4200: 使用了非标准扩展:结构/联合中的零大小数组”。

那么为什么gcc可以通过而连一个警告都没有？那是因为gcc为了预先支持C99的这种玩法，所以，让`零长度数组`这种玩法合法了。关于GCC对于这个事的文档在这里：“Arrays of Length Zero”，文档中给了一个例子，完整代码如下：

```c
#include <stdlib.h>
#include <string.h>

struct line {
   int length;
   char contents[0]; // C99的玩法是：char contents[]; 没有指定数组长度
};

int main(){
    int this_length=10;
    struct line *thisline = (struct line *)
                     malloc (sizeof (struct line) + this_length);
    thisline->length = this_length;
    memset(thisline->contents, 'a', this_length);
    return 0;
}
```

上面这段代码的意思是：我想分配一个不定长的数组，于是我有一个结构体，其中有两个成员，一个是length，代表数组的长度，一个是contents，代码数组的内容。后面代码里的this_length（长度是10）代表是想分配的数据的长度。

### 实例代码

```c
#include <stdio.h>
#include <malloc.h>
#include <string.h>

typedef struct line
{
    int len;
    char* content;
}line;

typedef struct line2
{
    int len;
}line2;

typedef struct line3
{
    int len;
    char content[0];
}line3;

typedef struct line4
{
    int len;
    char content[];
}line4;


void* test1(char* v,int n){
    line* p_str = (line*)malloc(sizeof(line) + n);
    p_str->len = n;

    p_str->content = memcpy((char*)(p_str + 1), v, n);

    printf("ret = %s\n", v);
    printf("test1 str=%s\n", p_str->content);
    free(p_str);
}

void* test2(char* v,int n){
    line2* p_str = (line2*)malloc(sizeof(line2) + n);
    p_str->len = n;

    memcpy((char*)(p_str + 1), v, n);

    printf("ret = %s\n", v);
    printf("test2 str=%s\n", (char*)(p_str+1));
    free(p_str);
}

void* test3(char* v,int n){
    line3* p_str = (line3*)malloc(sizeof(line3) + n);
    p_str->len = n;

    memcpy((char*)(p_str + 1), v, n);

    printf("ret = %s\n", v);
    printf("test3 str=%s\n", p_str->content);
    free(p_str);
}

void* test4(char* v,int n){
    line4* p_str = (line4*)malloc(sizeof(line4) + n);
    p_str->len = n;

    memcpy((char*)(p_str + 1), v, n);

    printf("ret = %s\n", v);
    printf("test4 str=%s\n", p_str->content);

    free(p_str);
}

int main(){
    char v[16] = "Hello world!";

    test1(v,16);
    test2(v,16);
    test3(v,16);
    test4(v,16);

    return 0;
}
```

### 使用柔性数组的好处

在进行Linux内核开发或者嵌入式开发时，经常会遇到结构体的最后出现char data[], char data[0], char data[1]，这样的代码，这就是柔性数组的实现。柔性数组也并没有定义柔性数组，只是所支持的不完整类型产生了柔性数组这样神奇的结构。

- 不需要初始化，数组名直接就是所在的偏移
- 不占任何空间，指针需要占用int长度空间，空数组不占任何空间。（注意，char data[1]；这种形式是占用一个单位的空间的）
- 空间一次分配，防止内存泄漏
- 分配连续的内存，减少内存碎片化。（因为指针所分配的空间不是连续的，而数组占用连续的空间）

### 使用柔性数组需要注意的

- 必须是结构体的最后一个成员
- 柔性数组之上，需要有其他的成员（结构体中不能只有一个柔性数组）
- sizefo返回的结构体的大小不包括柔性数组的内存（如果是char data[1]就会有一个单位的空间）
