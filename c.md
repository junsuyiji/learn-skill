# 嵌入式C面经



## 杂项

### **1. C和C++的区别？**



### **2. C语言如何实现一个频繁使用短小函数，C++如何实现？**

C语言可以使用宏定义实现一个短小函数，如下面例子所示。但是宏定义语句不会进行检查，并且对书写格式有过分的讲究。比如MAX和括号之间不能有空格，每个参数都要放在括号里。尽管如此，仍然会因为没有参数类型检查造成错误。

```c
#define MAX(a，b) ((a)>(b)?(a):(b))

int main(){
    int a=1, b=0, c=0;
    c = MAX(a, b); //程序运行正常
    c = MAX(a, "Hello"); //未进行类型检查，将错误的比较int和字符串。
    c = MAX(a++, b);  //a被增值两次，结果：c=2, a=3
    c = MAX(a++, b+10);  //a增值一次，结果：c=10, a=2
}
```


C++使用内联函数来实现，内联函数被编译器自动的以函数的形式添加进代码，因此会正常的进行参数检查。并且内联函数可以提高函数的使用效率。但是要注意内联函数不允许使用循环语句和开关语句，通常情况下，内联函数都是1-5行的小函数。

```c
inline int MAX(int a, int b){
    return a>b?a:b;
}
```



### 3. 你觉得C语言有特点的地方是什么



### 4. 什么是嵌入式 C？ / 你对嵌入式 C 语言的理解是什么？

嵌入式 C 是 C 标准委员会指定的 C 编程语言的一组语言扩展。 它通常用于解决不同嵌入式系统的 C 扩展之间存在的共性问题。 嵌入式 C 编程通常需要对 C 语言进行非标准扩展，以支持增强的微处理器功能，例如定点算术、多个不同的内存库和基本 I/O 操作。

### 5. C语言和嵌入式C之间的主要区别是什么？

C语言是一种通用的高级编程语言，用于设计任何基于桌面的应用程序。 另一方面，嵌入式 C 是 C 语言的扩展，用于开发基于微控制器的应用程序。 在这里，嵌入式是指主机或微控制器，而不是编程语言。 让我们在下表中来看看 C语言和嵌入式 C 之间的主要区别。

### **6. C语言中各进制表示法**

%d  代表十进制

%o  代表八进制

%x  代表十六进制

%u  无符号十进制数

%e  以科学记数法表示

%#o 代表带前缀o的八进制

%#x 代表待前缀ox的十六进制

\0oo 八进制值（o表示一个八进制数字）

\xhh 十六进制值（h表示一个十六进制数字）



## 预编译

### 1. C语言中的声明和定义

- 函数和变量的声明不会分配内存，但定义会分配相应的内存空间。
- 函数和变量的声明可以有很多次，但定义只有一次。
- 函数的定义和声明方式都是默认extern的，即函数默认是全局的。可以采用static实现对函数的隐藏。
- 变量的声明和定义默认都是局部的，在当前编译单元或者文件中可用。

### **2. 头文件中双引号和尖括号的区别是什么？**

这两种的主要区别是编译器预处理阶段查找头文件路径的顺序不一样：

- 使用双引号时：**先搜索源文件所在的工作目录**->编译器设置的头文件查找途径->系统变量C_INCLUDE_PATH指定的头文件路径。
- 使用尖括号时：先搜索编译器设置的头文件查找路径->系统变量C_INCLUDE_PATH指定的头文件路径。

因此，在#include引用自己写的头文件时，只能用双引号，而引用标准库文件时两种方法都行。

### **3. 在.h的头文件中，#pragma的作用**

\#pragma是一种预处理指令，它的作用是设置编译器的状态或者指定编译器完成一些特定的动作。其格式一般为 #pragama parameter。

- \#pragma message：message参数用于在编译信息输出窗口输出相应的信息，用于源代码的信息控制。其格式为#pragma message("SDK 1.01")。
- \#pragma once：一般用于头文件中，用来保证头文件只被编译一次。
- \#pragma code_seg：用来设置程序中函数代码存放的代码段。
- \#pragma pack：用来控制内存对齐的方式。#pragma pack(n)编译器将按照n个字节进行对齐。`#pragma pack()`编译器将取消自定义字节对齐方式。

### **4. #pragma once和#ifndef的区别**

在C/C++中，为了避免了同一个头文件被包含(include)多次有两种宏实现方式：

第一种是#ifdef的方式，这种方式受C/C++语言标准支持，它可以保证同一个文件或者***\*内容完全相同的文件/代码片段\****被包含多次。但这种定义方式依赖于宏名字不能冲突，如果宏名冲突，会导致头文件存在缺报错找不到声明。#ifndef的示例如下:

```c
#ifndef __LED_H__
#define __LED_H__

//下面放函数和变量声明语句

#endif
```

第二种方式采用#pragma的方式，这种方式由编译器保证同一个文件(物理意义上的)不会被包含多次，也不会出现因为宏名冲撞引发的问题。使用示例：

```c
#pragma once

//下面放函数和变量声明语句
...
```

###  **5. 什么是预处理，何时要预处理？**

C预处理器不是编译器的组成部分，而是编译过程的一个单独步骤，主要指的是**程序执行前**#开头的指令的一些工作。主要包括：#include,#define, #ifdef, #ifndef, #if/#elif/#else/#endif, #error, #pragma. 简言之，C预处理器(C Preprocessor)只不过是一个文本替换的工具，其目的是指示编译器在实际编译之前完成所需的预处理。





## 编译问题

### **1. C语言编译后内存分为哪几部分？**

- **栈(stack)**: 由编译器进行管理，自动分配和释放，存放函数调用过程的各种参数，局部变量，返回值以及函数的返回地址。操作方式类似于数据中的栈。
- **堆(heap)**: 用于程序动态申请分配和释放空间。C语言中的malloc和free，C++中的new和delete均是在堆中进行的。正常情况下，程序员申请的空间在使用结束后应该释放，若程序员没有释放空间，则在程序结束后由系统自动回收。注意：这里的对并不是数据结构中的堆。
- **全局(静态)存储区**: 分为DATA和BSS段，DATA段(全局初始化区)存放初始化的全局变量和静态变量；BSS段(全局未初始化分区) 存放未初始化的全局变量和静态变量。程序运行结束时自动释放，其中在BSS段在程序执行之前会被系统自动清零，所以未初始化的全局变量和静态变量在程序执行之前已经为0.
- **文字常量区**：存放常量字符串，程序结束由系统释放。
- **程序代码区**：存放程序的二进制代码。

### **2. C语言的变量**

(1) C语言变量的基本类型有哪些？

五种基本类型：char, int, float, double, **void**. void是空类型，一般用于限制函数的返回和参数以及用来定义空指针。

(2) 局部变量和全局变量区别？
\- 生存周期不同：局部变量存在于模块中，生存期从模块开始到结束。
\- 存储位置不同：局部变量保存在栈空间内，栈地址是不固定的，由编译器自动分配和释放，全局变量保存在静态存储区的数据段中。

(3) 局部变量和全局变量是否可以重名?
可以，但是局部变量会屏蔽全局变量，如果在程序中使用因为重名被屏蔽的全局变量，需要在变量前加::。

```c
int a = 10;
int main()
{
    int a = 9;
    printf("%d\n", a); //输出局部变量
    printf("%d\n", ::a);  //输出全局变量
    return 0;
}
```



### **3. C语言的数据类型有哪些？**

- 基本类型：包括整数类型和浮点类型，char属于整数类型。
- 派生类型：包括指针类型，数组类型，结构类型，共用体类型和函数类型。
- 枚举类型：枚举数据类型的作用是让数据更加简洁，易读。枚举数据的定义格式为：enum 枚举名 {元素1，元素2，...}; 枚举类型中的每一个元素都有其代表的值。未指定情况下，第一个枚举元素的默认值为0，后面每一个元素的值为前一个元素加。示例如下：

```c
enum DAY{
    MON, TUE, WED=5, THU, FRI, SAT, SUN
};
enum DAY day = MON, day1 = THU;
printf("%d\n", day);  //day的输出是0
printf("%d\n", day1); //day1的输出是6
```

void类型：void类型指定没有可用类型的值。当函数不需要返回值时，必须使用void限定。例：void func(int a, char *b)；当函数不允许接受参数时，必须使用void限定。例：int func(void)；void用来定义指针时，该指针可以指向任意类型的数据。



## 语法问题

### 关键字

1. c语言中有多少个关键字？

32个

```c
auto int double long char float short signed unsigned struct union enum static switch case default break register const volatile typedef extern return void continue  do while for  else  if goto sizeof 
```

2. 什么是定义？什么是声明？它们有什么区别？

定义：编译器在内存中创建一个对象，给这个对象分配一块内存并且赋予一个名字

声明：告诉编译器这个变量名已经有人用了或者是我现在要提前预定这个名字，后来的别再用这个了

区别：定义创建了对象并且分配了内存，但是声明没有分配内存



3. `static`的作用

修饰变量、修饰函数

修饰变量分为静态全局变量和静态局部变量，但是它们都存储在内存的静态区

静态全局变量：作用域仅限于变量被定义的文件中，在定义之处前面的代码也没有办法使用它

静态局部变量：仅仅只能在被定义的函数里面使用，别的函数不能用，但是由于存储在静态区，所以函数结束不会被销毁，函数下次使用依然可以使用这个值

```c
#include <stdio.h>

static int j;

void fun1() {
  static int i = 0;
  i++;
}

void fun2() {
  j = 0;
  j++;
}

int main() {
  for (int k = 0; k < 10; k++) {
    fun1();
    fun2();
  }
  printf("i is %d, j is %d \n", i, j);

  return 0;
}
```

修饰函数：静态函数不代表函数的存储方式，而是函数的作用域仅仅限于本文件

4. `struct`和`class`区别

仅仅有一个小区别：`struct`默认情况下属性是`public`,`class`默认情况下`private`

5. 什么是大小端模式？

大端模式：字数据的高字节存在低地址中，字数据的低字节则是存放在高地址中

小端模式：字数据的高字节存在高地址中，字数据的低字节则是存放在低地址中

6. 枚举和宏有什么区别

- 宏常量是在预编译阶段进行简单的替换，但是枚举则是在编译的过程中确定其值
- 一般在编译器里面可以调试枚举变量，但是不能够调试宏常量
- 枚举可以一次定义很多的枚举常量，但是宏一次仅仅能够定义一个



### 符号

1. 双引号和单引号的区别

双引号包围起来的是字符串常量，单引号包围起来的是字符常量，字符在内存中是以ASCII码存储的，所以可以和整形常量或者是数字来进行运算



### 预处理

1. 预处理是c语言的一部分吗



2. 包含#号的部分是预处理吗



3. 什么是内存对齐

对于没有对齐的内存，处理器需要两次访问，对于对齐的内存，处理器仅仅需要以此内存访问就可以

```c
#include <stdio.h>

struct test {
  int a;
  char b;
  int c;
};

int main() {
  struct test mytest;
  
  mytest.a = 1;
  mytest.b = '2';
  mytest.c = 3;

  printf("%d \n", sizeof(mytest));

  return 0;
}
//如果直接相加应该是4+1+4，但是却是12
```

**内存对齐的规则**

第一个变量在与结构体偏移量为0的地址处；其他成员变量要对齐到对齐数的整数倍处；结构体总大小为最大对齐数的整数倍,复杂类型(如结构)的默认对齐方式是它最长的成员的对齐方式,这样在成员是复杂类型时,可以最小化长度

ps：对齐数=编译器默认的对齐数与该成员大小的较小值，每个成员都有一个对齐数

为什么要进行内存对齐？

在32位的情况下，寄存器只能从能整除以4的地址开始读取数据，

内存对齐多少字节是可以手动指定的

#pragram pack(n)

为什么只能从特定的地址读取数据？

32位的情况下，地址总线位数不够多，只能同时访问32个相对地址相同的数据位，只能从被4整除的地址开始访问接下来的四个字节



### 指针和数组

1. 什么是指针

一个基本数据类型和*构成了一个指针变量，这个指针变量的大小是确定的，在32位的系统下，不管什么样的指针类型，大小都是4字节

*号则是取出该指针变量中存储的地址



2. 什么是数组



3. 指针和数组有什么样的关系

















### **1. 定义常量的方法，区别？**

C语言定义常量有两种方式：

- **使用`#define`宏定义**：`#define LENGTH = 10`
- **使用`const`关键字**：`const int LENGTH = 10 `或 `int const LENGTH = 10`

两者区别：

- 宏定义是字符替换，没有数据类型的区别，也不会进行类型检查，可能会产生边际效应等错误；const是常量的声明，有类型区别，需要在编译阶段进行类型检查。
- 宏定义是“编译时”的一个概念，在预处理阶段展开，不能宏定义进行调试，声明周期结束于编译时期；const是一个“运行时”的概念，在程序运行的时候使用。
- 宏定义是直接替换，不会分配内存，存储于程序的代码段中；const需要进行内存分配，存储于程序的数据段中。



###  **2. const关键字的用法有哪些？**

- **修饰变量，数组**：被修饰的变量和数组只具有只读属性，不可被更改(也就是变成了常量数组)。例子：

```c
const int i = 1;
const int array = {1, 2, 3}。
```

- **修饰指针**：const修饰指针的时候有三种情况：
    第一种情况const用来修饰*p，(如下代码)，const和int可以互换位置。此时const修饰的是*p，*p代表指针变量所指向内存单元的值，即*p的值不可被更改，但p所代表的地址可以被更改。

```c
int i = 1 ，j = 2;
const int *p = &i; // 或者是 int const *p = &i
*p = 3;  // 此行报错
p = &j; // 此行运行正常
```

第二种情况const用来修饰p，此时p所存放的内存单元地址不可变，但所指向内存单元的内容可变。

```c
int i = 1, j = 2;
int* const p = &i;
*p = 3;  //此行运行正常
p = &j  // 此行报错
```

第三种情况是*p和p均被const修饰，则p存放的内存单元的地址和*p存放的内存单元的内容均不可变。

```c
int i = 1, j = 2;
int* const p = &i;
*p = 3;  //此行报错
p = &j  // 此行也报错
```

- **修饰函数的入口参数**：防止传入的参数代表的内容在函数体内被改变，但**仅对指针和引用有意义**。因为如果是按值传递，传给参数的仅仅是实参的副本，即使在函数体内改变了形参，实参也不会得到影响。

```c
void fun(const char * src, char * des){  // 保护源字符串不被修改，若修改src则编译出错。
	strcpy(des,src);
}
```





### **3. volatile关键字的作用是什么？**

volatile单词是易变的，不稳定得意思。**volatile关键词修饰的变量表示可以被某些编译器未知的因素修改，比如操作系统，硬件或者其他线程等。因此编辑器不会对该变量的代码段进行优化，每次用到该变量时，都会从内存段重新读取。** 总结来说volatile可以告诉编译器，被修饰的变量随时可能发生变化，因此每次需要重新读取，来保证对特殊地址的稳定访问。

```c
volatile BOOL bStop = False;

// 线程1，用来对bStop标志位进行操作。
while(!bstop) {...}
bStop = False;

//线程2，等待线程1终止，如果bStop不使用volatile声明，则while循环无法退出。
bStop = True;
while(bStop);
```


volatile的应用场景：

- 中断服务程序中修改的供其他程序检测的变量需要加volatile
- 多任务环境下各任务间共享的标志也应该加volatile，如多线程中的共享标志位。
- 存储器映射的硬件寄存器也要加volatile声明，因为每次对它的读写都可能由不同的意义。



### **4. static关键字的作用**

C语言中static关键字的常用于三种情况：修饰局部变量，修饰全局变量，修饰函数。

- static用于局部变量修饰符：被static修饰的局部变量只能在函数体内被调用。并且**静态局部变量的值不会因为函数调用的结束而清除**。当函数再次调用时，该变量的值是上次调用结束后的值。静态局部变量被存储在**静态存储区**(局部变量存放在栈中)。并且静态局部变量会被自动初始化为0。
- static用于全局变量修饰符：static修饰的全局变量成为静态全局变量。该变量只能在被当前文件的所有函数访问，不可以被其他文件内的函数访问。
- static用于函数修饰符：被static修饰的函数成为静态函数。同样静态函数只能在当前文件中被调用，不能被其他文件调用。



### **5. extern 关键字的作用**

在C语言中，修饰符extern用在变量或者函数的声明前，用来说明此函数/变量是在别处定义，但是要再此处引用。注意**extern不能用来修饰局部变量**。具体应用示例：

在a.c文件中

```c
unsigned char key;
void func(void){...}
```

在main.c文件中

```c
#include "a.h"

extern unsigned char key;
extern void func(void);
int main(){
    if(key == "a")
        func();
    return 0;
}
```



### **6. i++和++i区别，哪个速度更快？**

++/--属于C/C++中的自增运算符，表示操作数增/减一。一般自增运算符有两种写法：i++ / ++i。
区别：

1. 用在独立的语句中：i++和++i没有区别。
2. 用在赋值表达式中：y=i++表示先给y赋值，i自身再加一；y=++i表示先i自身加一再赋值给y。

**速度上：++i的速度比较快，**详细的流程如下：

```c
// i++等效于
int j = i;
i = i + 1;
return j

//++i相当于
i = i + 1;
return i;
```



### **7. new和malloc的区别？**

1. **属性**：new/delete是C++关键字，需要编译器的支持，malloc/free是库函数需要头文件的支持。
2. **参数**：使用new操作符申请内存分配时无需指定内存块的大小，编译器会根据类型的信息自行计算。然而malloc则需要显式的指出所需内存的尺寸。
3. **返回类型的安全性**：new操作符内存分配成功时，返回的是对象类型的指针、类型严格与对象匹配，无需进行类型转换，因此**new是符合类型安全性的操作符**。malloc内存分配成功返回的是 void *类型的指针，需要通过强制的类型转换将指针转换成所需要的类型。
4. **分配失败的返回值**：new内存分配失败的时候会跑出mac_alloc异常，malloc分配内存失败的时候会返回Null。
5. **自定义类型**：new会先调用operator new函数，申请足够的内存(通常底层使用malloc实现)。然后调用类型的构造函数初始化的成员变量，最后返回自定义类型的指针。delete会先调用析构函数，然后调用operator delete函数释放内存(通常底层采用free实现)。
6. **申请的内存所在位置**：new操作符从自由存储区(free store)上为对象动态的分配内存空间，然而malloc函数从堆上分配内存。自由存储区是基于C++的new操作符的抽象概念，范式通过new操作符进行内存申请，该内存即为自由存储区。而堆是操作系统中的术语，是操作系统所维护的一种特殊内存，用于程序内存的动态分配。C语言使用malloc从堆上分配内存，使用free释放已分配的相应内存。自由存储区不等于堆。
7. **重载**：C++允许重载new/delete操作符。布局new的就不需要为对象分配内存，而是指定了一个地址作为内存的起始区域，new在这段内存上为对象调用构造函数完成初始化过程，并返回此地址。然后malloc不允许重载。



### **8. 为什么要用指针？**



### **9. int 和 unsigsned int 有什么区别**



### **10. memcopy函数的实现**



### **11. if圆括号内可以任意表达式？**



### 12. const和宏定义的区别

编译器处理不同
宏定义是一个“编译时”概念，在预处理阶段展开（在编译时把所有用到宏定义值的地方用宏定义常量替换），不能对宏定义进行调试，生命周期结束于编译时期；
const常量是一个“运行时”概念，在程序运行使用，类似于一个只读行数据

存储方式不同
宏定义是直接替换，不会分配内存，存储与程序的代码段中；
const常量需要进行内存分配

类型和安全检查不同
宏定义是字符替换，没有数据类型的区别，同时这种替换没有类型安全检查，可能产生边际效应等错误；
const常量是常量的声明，有类型区别，需要在编译阶段进行类型检查



### 13. static函数与普通函数

static函数在内存中只有一份，普通函数在每个被调用中维持一份拷贝
问：A.c 和B.c两个c文件中使用了两个相同名字的static变量,编译的时候会不会有问题?这两个static变量会保存到哪里（栈还是堆或者其他的）?
答：static的全局变量，表明这个变量仅在本模块中有意义，不会影响其他模块。
他们都放在数据区，但是编译器对他们的命名是不同的。
如果要使变量在其他模块也有意义的话，需要使用extern关键字。

### 14. 不能做switch()的参数类型

不支持float，double，string



### 15. sizeof与strlen有以下区别

sizeof是一个操作符，而strlen是库函数。
sizeof的参数可以是数据的类型，也可以是变量，而strlen只能以结尾为’\0’的字符串作参数。
编译器在编译时就计算出了sizeof的结果，而strlen必须在运行时才能计算出来。
sizeof计算数据类型占内存的大小，strlen计算字符串实际长度

### 16. strcpy和strncpy的缺陷

1.存在潜在越界问题
当dest的长度 < src的长度的时候，由于无法根据指针判定其所指指针的长度，故数组内存边界不可知的。因此会导致内存越界，尤其是当数组是分配在栈空间的，其越界会进入你的程序代码区，将使你的程序出现非常隐晦的异常。

2.字符串结束标志服’\0’丢失
当dest所指对象的数组长度==count的时候，调用strncpy使得dest字符结束符’\0’丢失。

3.效率较低
当count > src所指对象的长度的时候，会继续填充’\0’知道count的长度为止。

4.不能处理内存覆盖问题
不能处理dest和src内存重叠的情况。



### 17. C语言中的分段错误错误是什么？ 分段错误错误的一些常见原因是什么？

分段错误(Segmentation fault)是一种运行时错误，在程序运行时可能由于某些原因而发生。
以下是 C 中的分段错误错误的一些常见原因列表：

- 解引用指针的用法：如果使用的指针可能没有有效的地址/内存位置来指向，它可能会产生分段错误错误。
- 如果尝试访问只读内存区域，程序可能会返回分段错误错误。
- 当尝试释放已释放的内存(使用指针)时，也可能发生分段错误错误。
- 分段错误错误的发生是C中产生堆栈溢出错误的原因。



### 18. C语言中的堆栈溢出错误是什么？

在嵌入式 C 中，ISR 代表中断服务程序。 这些是存储在特定内存地址并在发生某种类型的中断时调用的过程。



### 19. 嵌入式 C 中 ISR 的完整形式是什么？ 是否可以传递任何参数并从 ISR 返回值？

在嵌入式 C 中，ISR 代表中断服务程序。 这些是存储在特定内存地址并在发生某种类型的中断时调用的过程。

不可以。不能传递任何参数并从 ISR 返回值。 ISR 不返回任何内容，并且不允许传递任何参数。 当硬件或软件事件发生时调用 ISR，而代码不会调用它。 这就是为什么不向 ISR 传递参数的原因。 由于代码不调用 ISR，因此没有调用代码来读取 ISR 的返回值。 这就是 ISR 不返回任何值的原因。



### **20. C语言中的静态变量是什么？ 为什么在 C 中使用静态变量？**

静态变量具有保留其值的特性，即使它们超出了它们的范围。 这意味着只能初始化一次静态变量。 它的范围是本地的，但它一直存在到程序结束。 编译器将变量保留到程序结束。可以在函数内部或外部定义静态变量。 静态变量的默认值为零。 静态变量在程序执行之前一直有效。

C语言中静态变量的语法：

```c
static datatype variable_name = value;
```

静态变量通常用于计数。 例如，函数 `openBakAccount()`会在银行开立新账户时调用。 然后，要统计开户总数，可以在函数中声明一个静态变量，并在每次函数调用时增加它。



### **21. 如何在一个源文件中使用在另外一个源文件中定义的变量？**

可以使用`extern`关键字来声明一个允许访问另一个文件中的变量的变量。 这就是如何在另一个源文件中定义的源文件中使用变量。



### 22. 如何通过对指针地址的一些意外修改来保护字符指针？

可以使用常量字符指针(`const char*`)通过意外修改指针地址来保护字符指针。 它可以防止对字符串中的指针地址进行不必要的修改。



### **23. 三种基本的数据模型**

按照数据结构类型的不同，将数据模型划分为层次模型、网状模型和关系模型。



### **24. 结构与联合有和区别？**

(1). 结构和联合都是由多个不同的数据类型成员组成, 但在任何同一时刻, 联合中只存放了一个被选中的成员（所有成员共用一块地址空间）, 而结构的所有成员都存在（不同成员的存放地址不同）。 
(2). 对于联合的不同成员赋值, 将会对其它成员重写, 原来成员的值就不存在了, 而对于结构的不同成员赋值是互不影响的

### **25. 简述数组与指针的区别？**

数组要么在静态存储区被创建（如全局数组），要么在栈上被创建。指针可以随时指向任意类型的内存块。
    (1)修改内容上的差别
      char a[] = “hello”;
      a[0] = ‘X’;
      char *p = “world”; // 注意p 指向常量字符串
      p[0] = ‘X’; // 编译器不能发现该错误，运行时错误
   (2) 用运算符sizeof 可以计算出数组的容量（字节数）。sizeof(p),p 为指针得到的是一个 指针变量的字节数，而不是p 所指的内存容量。C++/C 语言没有办法知道指针所指的内存容量，除非在申请内存时记住它。注意当数组作为函数的参数进行传递时，该数组自动退化为同类型的指针。
     char a[] = "hello world";
     char *p = a;
     cout<< sizeof(a) << endl; // 12 字节
     cout<< sizeof(p) << endl; // 4 字节
     计算数组和指针的内存容量
     void Func(char a[100])
     {
      cout<< sizeof(a) << endl; // 4 字节而不是100 字节
     }



### **26. Typedef**

Typedef 在C语言中频繁用以声明一个已经存在的数据类型的同义字。也可以用预处理器做类似的事。例如，思考一下下面的例子：
\#define dPS struct s *
typedef struct s * tPS;
  以上两种情况的意图都是要定义dPS 和 tPS 作为一个指向结构s指针。哪种方法更好呢？（如果有的话）为什么？
  这是一个非常微妙的问题，任何人答对这个问题（正当的原因）是应当被恭喜的。答案是：typedef更好。思考下面的例子：
dPS p1,p2; //当变量的数目不止一个时define会出错
tPS p3,p4;
第一个扩展为
struct s * p1, p2;
  上面的代码定义p1为一个指向结构的指，p2为一个实际的结构，这也许不是你想要的。第二个例子正确地定义了p3 和p4 两个指针。

### **27. A.c 和B.c两个c文件中使用了两个相同名字的static变量,编译的时候会不会有问题?这两个static变量会保存到哪里（栈还是堆或者其他的）?**

static的全局变量，表明这个变量仅在本模块中有意义，不会影响其他模块。

他们都放在数据区，但是编译器对他们的命名是不同的。

如果要使变量在其他模块也有意义的话，需要使用extern关键字。





## C语言面试

### 关键字

**C语言有多少个关键字？**

32个

**分别是哪些？**

1. auto 声明自动变量
2. int    声明整形变量
3. double 声明双精度变量
4. long  声明长整型变量
5. char   声明字符型变量
6. float   声明浮点数变量
7. short   声明短整型变量
8. signed   声明有符号类型的变量
9. unsigned  声明无符号类型的变量
10. struct   声明结构体变量
11. union  声明联合体变量
12. enum  声明枚举类型变量
13. static  声明静态类型变量
14. switch  用于开关语句
15. case  用于开关语句分支
16. default  用于开关语句其他分支
17. break  跳出当前循环
18. register  声明寄存器变量
19. const   声明只读变量
20. volatile  变量在程序执行的过程中可以被隐含的改变
21. typedef   给数据类型起别名
22. extern  声明变量在其他文件中声明
23. return   子程序返回语句
24. void    声明函数无返回值或者无参数，声明空类型指针
25. continue  结束当前循环，开始下一轮循环
26. do   循环语句的循环体
27. while 循环语句的循环条件
28. if  条件语句
29. else 条件语句的否定分支
30. for  一种循环语句
31. goto  无条件跳转语句
32. sizeof  计算对象所占用内存的空间大小



**什么是定义？什么是声明？两者有什么区别？**

定义创建一个对象，给对象分配一块空间并且起一个名字

声明告诉编译器这个名字已经有人用了，但是没有开辟内存

区别在于定义分配了空间，但是声明没有分配空间



**register 一定能让变量存储在cpu的寄存器中吗？**

这个关键字只是建议编译器这样做，并不保证一定能够将变量放到寄存器中



**register大小有限制吗？**

register变量需要小于或者等于整形的变量，因为需要一个寄存器能够放下，这个系统的位数有关



**register能够取地址吗？为什么？**

因为register不一定能够存放在内存之中，所以不能对register进行取地址操作



**static修饰的变量存放在哪里？**

static声明的变量分为静态变量和全局变量，它们都存放在内存中的静态区

**static的作用？**

修饰变量：

- 修饰静态全局变量，使得该变量仅仅能够在该文件使用，不仅如此，在静态变量定义之前，即使是本文件也无法使用
- 修饰局部变量，仅仅能够在这个函数里面使用，但是函数运行结束不会被销毁，而是存储在内存区，下次调用该函数依然可以使用该函数

<img src="/home/caiji/.config/Typora/typora-user-images/image-20231010204947266.png" alt="image-20231010204947266" style="zoom:50%;" />

```c
#include <stdio.h>

static int i;

void fun1() {
  
  i = 0;
  i++;
}

void fun2() {
  
  static j = 0;
  j++;

}


int main() {
    
  printf("%d, %d \n", i, j);

  return 0;
}

```

修饰函数

让这个函数仅仅能够在本文件中使用



**sizeof是函数吗？**

sizeof不是函数是一个关键字

**sizeof一些常见的计算**

- `sizeof (int *) p` int * 表示指向整形数据的指针类型，所以这个是求解变量p的大小
- `int a[100]; sizeof(a),sizeof[100] ``sizeof(a)`将返回整个数组`a`的大小，即`100 * sizeof(int)`，即`100 * 4`，等于400字节。 ;`sizeof(a[100])`将返回数组`a`中的第101个元素`a[100]`的大小，即`sizeof(int)`，即4字节。

**unsigned计算题？**

```c
#include <stdio.h>

int main() {
  
  char a[1000];
  int i;
  
  for (int i = 0; i < 1000; i++) {
    a[i] = -1-i;
  }

  printf("%ld\n", strlen(a));

  return 0;
}

```

为什么a的长度是255？

首先strlen函数遇见字符\0的时候就会停止读取，认为本字符串结束了

0的读取方式：以数字的方式读取就是0，以字符的方式读取就是’\0’，以某些其他方式读取就是null

对于单精度浮点数（32位），+0和-0的表示形式如下：

- +0: 0 00000000 00000000000000000000000
- -0: 1 00000000 00000000000000000000000

```c
unsigned i ;
for (i=9;i>=0;i--)
{
printf("%u\n",i);
}
```

这段代码会导致一个无限循环。原因是循环条件中的`i>=0`永远为真，因为`i`是一个无符号整数类型（`unsigned`），它的值不会小于0。

当`i`的值为0时，循环会继续执行，然后`i--`操作会使`i`的值变为最大的无符号整数（在32位系统上是4294967295），然后循环条件`i>=0`仍然为真，导致循环继续执行。

在每次循环中，`printf`语句会打印出`i`的值，但由于`i`的值一直为非负数，循环将永远无法结束，导致无限循环。

要解决这个问题，可以将循环条件改为`i>0`，这样循环会在`i`的值为0时停止。或者可以将`i`的类型改为有符号整数类型（如`int`），这样循环条件`i>=0`就能正常工作。



```c
int i = -20;
unsigned j = 10;
i+j 的值为多少？为什么？
```

在这个表达式中，`i`是一个有符号整数，值为-20，`j`是一个无符号整数，值为10。**当有符号整数和无符号整数进行运算时，有符号整数会被隐式地转换为无符号整数。**



**float能够和0.0进行比较吗？**

不能，有精度限制



**case 关键字后面的值有什么要求吗？**

case 后面只能是整型或字符型的常量或常量表达式



**字符型数据在内存里是怎么存的？**

字符型数据在内存中是以ASCII码的形式存储的。每个字符都对应一个唯一的ASCII码值，它在内存中被表示为一个字节（8位）的数据。



**const变量为什么在定义的时候必须初始化？**

通过在定义时强制要求初始化，可以确保常量在使用之前已经具有确定的值。这样可以避免在后续的代码中出现意外的错误或不确定性

**case 语句后面是否可以是 const 修饰的只读变量呢？**

case语句后面必须是一个常量表达式，即在编译时就能确定的值。

编译器通常不为普通 const 只读变量分配存储空间，而是将它们保存在符号表中，这使得它成为一个编译期间的值，没有了存储与读内存的操作，使得它的效率也很高。

```c
#define M 3
//宏常量
const int N=5; //此时并未将 N 放入内存中
......
int i=N;  //此时为 N 分配内存，以后不再分配！
int I=M;   //预编译期间进行宏替换，分配内存
int j=N;   //没有内存分配
int J=M;   //再进行宏替换，又一次分配内存！
```

const 定义的只读变量在程序运行过程中只有一份拷贝（因为它是全局的只读变量，存放在静态区），而#define 定义的宏常量在内存中有若干个拷贝。#define 宏是在预编译阶段进行替换，而 const 修饰的只读变量是在编译的时候确定其值。
#define 宏没有类型，而 const 修饰的只读变量具有特定的类型



**空结构体有多大？**

一般情况下，一个字节



**union的大小是怎么确定的？**

在 union 中所有的数据成员共用一个空间，同一时间只能储存其中一个数据成员，所有的数据成员具有相同的起始地址



**枚举和宏的区别？**

- 枚举是编译的时候确定的，宏是预编译的时候进行替换的
- 可以调试枚举常量但是不能够调试宏常量
- 枚举一次可以定义很多的常量，但是宏一次仅仅能够定义一个

当我们定义一个枚举类型时，编译器会根据枚举值的数量和范围来选择合适的数据类型



**typedef 与#define 的区别**



### 符号

**反斜杠的作用？**

- 用作接续符
- 用作转义字符开始标识



**单引号和双引号的区别**

双引号的是字符串常量，单引号的是字符变量，字符在内存中是以ASCII码的形式存储的，所以可以和整形变量或者常量直接运算

字符串常量以/0结尾



**常见的符号优先级**

| .的优先级大于*           | *p.f                | *(p.f)                |
| ------------------------ | ------------------- | --------------------- |
| []的优先级大于*          | int *a[]            | int *(a[])            |
| 函数的()高于*            | int *fun()          | int * (func())        |
| == 和!=高于位操作        | (val & mask != 0)   | val & (mask != 0)     |
| == 和!=高于赋值符        | c = getchar() !=EOF | c = (getchar() !=EOF) |
| 算术运算符高于位移运算符 | msb << 4 + lsb      | msb << (4 + lsb)      |
|                          |                     |                       |





