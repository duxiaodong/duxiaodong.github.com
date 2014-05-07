---
layout: post
title: C专家编程(Expert C Programming)问题汇总
description: ""
category: "programming"
tags: [C, skills]
---
{% include JB/setup %}

本文所有示例全部摘自C专家编程(Expert C programming),部分代码需自行补齐为测试程序。这里仅给出部分问题答案，其他答案可在书本对应Page找到，少部分示例书本没有给出最终结果，需要自行编译测试！

###1.计算机时间问题：

关于time_t,什么时候它会达到尽头，重新回到开始？(Page 5)

test1.c和test2.c在你所在的时区打印的信息分别是什么，Why?

test1.c

    #include <stdio.h>
    #include <time.h>
    int main() 
    {
        time_t biggest = 0x7FFFFFFF;

        printf("biggest = %s \n", ctime(&biggest));
        return 0;
    }

test2.c

    #include <stdio.h>
    #include <time.h>
    
    int main()
    {
        time_t biggest = 0x7FFFFFFFF;
        printf("biggest = %s \n",asctime(gmtime(&biggest)));
        return0;
    }

###2.赋值问题

为什么下面这段程序编译时，编译器会发出一条警告信息(自行补齐程序测试,Page 19):

`line 5; warning argument is incompatible with prototype`

    1 foo(const char **p){}
    2 
    3 main (int argc, char **argv)
    4 {
    5     foo(argv)
    6 }

###3.类型问题

test1和test2程序在你的机器上能否编译成功(Page 24)？

test1.c

    #include <stdio.h>
    
    int array[] = {23, 34, 12, 17, 204, 99, 16};
    #define TOTAL_ELEMENTS (sizeof(array/sizeof(array[0])))
    
    void main()
    {
        int d = -1, x;
        if(d <= TOTAL_ELEMENTS -2)
            x = array[d+1];
        return;
    }

test2.c

    #include <stdio.h>
    
    int array[] = {23, 34, 12, 17, 204, 99, 16};
    #define TOTAL_ELEMENTS (sizeof(array/sizeof(array[0])))
    
    void main()
    {
        int d = -1, x;
        if(d <= (int)TOTAL_ELEMENTS -2)
            x = array[d+1];
        return;
    }

Problem:按照ECP(Expert C Programming简写，下同)的解释，test2.c应该能否编译通过，但在我的机器上，用gcc -o test test2.c编译时，还是出现与test1.c同样的错误，gcc版本 gcc version 4.6.3 (Ubuntu/Linaro 4.6.3-1ubuntu5)等待解决中...


###4.第一次执行行为与以后执行行为不同示例

    /* Page 35 */
    #include <stdio.h>

    void generate_initializer(char * string)
    {
        static char separator = ' ';
        printf("%c %s \n", separator, string);
        separator = ',';
        return;
    }
    
    void main()
    {
        char string[] = "This is a test";
        generate_initializer(string);
        generate_initializer(string);
        generate_initializer(string);
        generate_initializer(string);
        return;
    }

###5.重载问题

`p = N * size * q;`，这里的乘号是一个还是两个,提示，接下去的语句是： `r = malloc(p)`

`apple = sizeof(int) *p`,这代表什么意思，是int的长度乘以p？或则是把未知类型的指针p强制转换为int,然后进行sizeof操作?

###6.C语言申明

A.申明从它的名字开始读取，然后按照优先级顺序依次读取

B.优先级从高到低依次是：

* 1.申明中被括号括起来的那部分
* 2.后缀操作符：括号()表示这是一个函数，而方括号[]表示这是一个数组。
* 3.前缀操作符：星号*表示"指向...的指针"

C.如果`const`和(或)`volatile`关键字的后面紧跟类型说明符(如int,long)等，那么它作用于类型说明符。在其他情况下，`const`和(或)`volatile`关键字作用于它左边紧邻的指针星号.

例(Page 64)：

`char * const *(*next)()`

`char *(* c[10])(int **p)`

编程挑战：编写一个程序，把C语言的申明翻译成通俗语言(Page 75)，分析流程参见（Page 65) C语言申明的神奇解码环

cdecl.c

    #include <stdio.h>
    #include <string.h>
    #include <ctype.h>
    #include <stdlib.h>
    #define MAXTOKENS 100
    #define MAXTOKENLEN 64
    
    enum type_tag{ IDENTIFIER, QUALIFIER, TYPE };
    
    struct token {
        char type;
        char string[MAXTOKENS];
    };
    
    int top = -1;
    struct token stack[MAXTOKENS];
    struct token this;
    
    #define pop stack[top--]
    #define push(s) stack[++top] = s
    
    /* 判断标识符的类型 */
    enum type_tag classify_string(void)
    {
        char *s = this.string;
        if(!strcmp(s, "const")){
            strcpy(s, "read-only");
            return QUALIFIER;
        }
        if(!strcmp(s, "volatile")) return QUALIFIER;
        if(!strcmp(s, "void")) return TYPE;
        if(!strcmp(s, "char")) return TYPE;
        if(!strcmp(s, "signed")) return TYPE;
        if(!strcmp(s, "unsigend")) return TYPE;
        if(!strcmp(s, "short")) return TYPE;
        if(!strcmp(s, "int")) return TYPE;
        if(!strcmp(s, "long")) return TYPE;
        if(!strcmp(s, "float")) return TYPE;
        if(!strcmp(s, "double")) return TYPE;
        if(!strcmp(s, "struct")) return TYPE;
        if(!strcmp(s, "union")) return TYPE;
        if(!strcmp(s, "enum")) return TYPE;
        return IDENTIFIER;
    }
    
    /* 读取下一个标记到"this" */
    void gettoken(void)
    {
        char *p = this.string;
    
        /* 略过空白字符 */
        while ((*p = getchar()) == ' ');
        if(isalnum(*p)){
            while(isalnum(*++p = getchar()));
            ungetc(*p, stdin);
            *p = '\0';
            this.type = classify_string();
            return;
        }
    
        if(*p == '*') {
            strcpy(this.string, "pointer to");
            this.type = '*';
            return;
        }
        this.string[1] = '\0';
        this.type = *p;
        return;
    }
    
    void read_to_first_identifier(){
        gettoken();
        while(this.type != IDENTIFIER){
            push(this);
            gettoken();
        }
    
        printf("%s is ",this.string);
        gettoken();
        return;
    }
    
    void deal_with_arrays(){
        while(this.type == '[') {
            printf("array");
            gettoken(); /* 数字或'[' */
            if(isdigit(this.string[0])){
                printf("0..%d ",atoi(this.string)-1);
                gettoken(); /* 读取']' */
            }
            gettoken(); /* 读取']'之后的再一个标记 */
            printf("of ");
        }
        return;
    }
    
    void deal_with_function_args(){
        while(this.type != ')'){
            gettoken();
        }
        gettoken();
        printf("function returning ");
        return;
    }
    
    void deal_with_pointers(){
        while(stack[top].type == '*' ){
            printf("%s ", pop.string );
        }
    }
    
    void deal_with_declarator(){
        /* 处理标识符之后可能存在的数组/函数 */
        switch(this.type){
            case '[':
                deal_with_arrays();
                break;
            case '(':
                deal_with_function_args();
                break;
        }
    
        deal_with_pointers();
    
        /* 处理在读入到标识符之前入到堆栈中的符号 */
        while(top >= 0) {
            if(stack[top].type == '('){
                pop;
                gettoken(); /* 读取')'之后的符号 */
                deal_with_declarator();
            }else {
                printf("%s ", pop.string);
            }
        }
        return;
    }
    
    int main()
    {
        /* 将标志压入堆栈中，直到遇见标识符 */
        read_to_first_identifier();
        deal_with_declarator();
        printf("\n");
        return 0;
    }

编译执行：

    $ gcc cdecl.c -o cdecl
    $ ./cdecl 
    char *(*c[10])(int **p)
    c is array0..9 of pointer to function returning pointer to char

###7.函数库链接的5个特殊秘密

* 动态库文件的扩展名是".so",而静态库文件的扩展名是".a"
* 例如，你通过-lthread选项，告诉编译链接到libthread.so
* 编译器期望在确定的目录找到库
* 观察头文件，确定所使用的函数库
* 与提取动态库的符号相比，静态库中的符号提取的方法限制更严

###Tips

* 无论在什么时候，如果遇见了这样一条语句`malloc(strlen(str))`;几乎可以断定它是错误的，而`malloc(strlen(str)+1)`才是正确的.(Page 28)
* `switch`的fall through 问题(Page 29)
* `const`关键字并不是真正表示常量(Page 31)
* C语言运算符优先级存在的问题(Page 38)
* C语言语言标准I/O库中的`gets()`问题(Page 42)
* typedef和define的区别(Page 68),typedef一些使用注意事项(Page 71)
* 数组和指针并不相同(Page 81)
* 静态链接和动态链接的问题(Page 93)
* 怎样在函数库中观察一个符号(Page 99)
* 始终将-l函数库选项放在编译命令行的最右边，Why?(Page 101)

###书本错误信息汇总

* (Page 23) `if(-1 < (unsigned char) 1` 应为 `if(-1 < (unsigned char))`
* (Page 76) Line 43 `INDENTIFIER` 应为 `IDENTIFIER`
* (Page 83) Line 83 `while(thie.type == '['] {` 应为 `while(thie.type == '[') {`
