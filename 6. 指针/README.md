---
typora-copy-images-to: images
---

### 指针知识体系搭建

指针是一种变量，在32位系统下占4字节，在64位系统下占8字节

指针和指针变量是两个完全不同的概念，指针是一个地址，而指针变量是存放地址（指针）的变量。

指针指向谁，就把谁的地址赋给指针

指针变量和它所指向的内存空间变量是两个不同的概念

不断的给指针赋值，相当于不停的改变指针的指向

理解指针的关键,是内存. 没有内存哪里的指针 

间接赋值是指针存在的最大意义

在C语言中，地址还是扩大变量或者函数作用域的有效手段

- 空指针NULL
- 野指针：就是指针指向一个不确定的地址空间，或者虽然指向一个确定的地址空间，但是引用空间的结果却是不可预知的
- 万能指针void *
- 函数指针：指向函数（入口地址）的指针
- 指针函数：返回值为指针的函数

#### 铁律1：指针是一种数据类型

（1）指针也是一种变量，占有内存空间，用来保存内存地址。

指针变量占有内存空间大小，32位系统大小为4个字节，64位系统大小为8个字节

（2）*p操作内存

- 在指针声明时，*号表示所声明的变量为指针
- 在指针使用时，*号表示 操作 指针所指向的内存空间中的值
- *p相当于通过地址(p变量的值)找到一块内存；然后操作内存
- *p放在等号的左边赋值（给内存赋值）
- *p放在等号的右边取值（从内存获取值）

（3）指针变量和它指向的内存块是两个不同的概念

- 含义1： 给p赋值p=0x1111; 只会改变指针变量值，不会改变所指的内容；p = p +1;p++
- 含义2： 给\*p赋值`*p='a';` 不会改变指针变量的值，只会改变所指的内存块的值  
- 含义3： =左边\*p 表示 给内存赋值， =右边\*p 表示取值 含义不同切记！
- 含义4： =左边`char *p`
- 含义5： 保证所指的内存块能修改 全局区中的常量区受系统保护，不能通过指针修改

（4）指针是一种数据类型，是指它指向的内存空间的数据类型

含义1：指针步长（p++），根据所指内存空间的数据类型来确定

p++ = (unsigned char )p+sizeof(a);

结论：指针的步长，根据所指内存空间类型来定。

注意：	建立指针指向谁，就把谁的地址赋值给指针。	

不断的给指针变量赋值，就是不断的改变指针变量（和所指向内存空间没有任何关系）。

#### 铁律2：间接赋值（*p）是指针存在的最大意义 

（1）两码事：指针变量和它指向的内存块变量
（2）条件反射：指针指向某个变量，就是把某个变量地址赋值给指针
（3）\*p间接赋值成立条件：3个条件 

- 2个变量（通常一个实参，一个形参）
- 建立关系，实参取地址赋给形参指针 
- \*p形参去间接修改实参的值 

```c
int  getMem3(char **myp1, int *mylen1,  char **myp2, int *mylen2)
{
	int		ret = 0;
	char	*tmp1, *tmp2;

	tmp1 = (char *)malloc(100);
	strcpy(tmp1, "1132233");

	//间接赋值 
	*mylen1 = strlen(tmp1);  //1级指针
	*myp1 = tmp1; //2级指针的间接赋值

	tmp2 = (char *)malloc(200);
	strcpy(tmp2, "aaaaavbdddddddd");

	*mylen2 = strlen(tmp2);  //1级指针
	*myp2 = tmp2; //2级指针的间接赋值

	return ret;
}

int  main35()
{
	int		ret = 0;
	char	*p1 = NULL;
	int		len1 = 0;
	char	*p2 = NULL;
	int		len2 = 0; 

	ret = getMem3(&p1, &len1, &p2, &len2);
	if (ret != 0)
	{
		printf("func getMem3() err:%d \n", ret);
		return ret;
	}
	printf("p1:%s \n", p1);
	printf("p2:%s \n", p2);
	if (p1 != NULL)
	{
		free(p1);
		p1 = NULL;
	}
	if (p2 != NULL)
	{
		free(p2);
		p2 = NULL;
	}

	printf("p1:%d \n", p1);
	system("pause");
	return ret;
}
```

![](images/指针间接赋值.png)

#### 铁律3：理解指针必须和内存四区概念相结合

（1）主调函数 被调函数 

- 主调函数可把堆区、栈区、全局数据内存地址传给被调用函数
- 被调用函数只能返回堆区、全局数据

（2）内存分配方式

- 指针做函数参数，是有输入和输出特性的。
- 在主调用函数为指针分配内存，传给被调用函数，这种用法叫指针的输入in
- 运算结果可以当作指针参数传进来，不要轻易改变形参的值，为了不改变形参的值，用一个辅助指针接收形参的值

#### 铁律4：应用指针必须和函数调用相结合（指针做函数参数）

![1499783656269](images/1499783656269.png)

#### 铁律5：一级指针典型用法（指针做函数参数）

数组（int a[10]）作函数参数，会退化为指针

字符串作函数参数

- C语言的字符串，以0结尾的字符串
- 在C语言中没有字符串类型，通过字符数组来模拟字符串
- 字符串的内存分配，栈上、堆上、全局区

```c
void main()
{
	char buf[20]= "aaaa"; //定义并且初始化
	char buf2[] = "bbbb";
	char *p1 = "111111";
	char *p2 = malloc(100); 
	strcpy(p2, "3333");

	system("pause");
	return ;
}
```

![1499825183889](images/1499825183889.png)

```c
char *str_cnct( char *x, char* y )
{
	if( x == NULL )
	{
		return NULL;
	}
	char * str3 = (char *) malloc( 80 )
	char *z = str3; // 指针z指向数组str3
	while ( *z++ = *x++ );
	z--; // 去掉串尾结束标志
	while ( *z++ = *y++ );
	z = str3; // 将str3地址赋给指针变量z
	return(z);
}
void main()
{
	Char *p = str_cnct( "abcd", "ddeee" );
	If( p != NULL )
	{
		Free(p); 
      	p = NULL;
	} 
	return;
}
```

#### 铁律6：二级指针典型用法（指针做函数参数）

二级指针三种内存模型图

```c
void main()
{
	int i = 0;
	//指针数组
	char * p1[] = {"123", "456", "789"};
	//二维数组
	char p2[3][4]  = {"123", "456", "789"};
	//手工二维内存
	char **p3 = (char **)malloc(3 * sizeof(char *)); //int array[3];
	for (i=0; i<3; i++)
	{
		p3[i] = (char *)malloc(10*sizeof(char)); //char buf[10]
		sprintf(p3[i], "%d%d%d", i, i, i);
	}
}
```

![](images/内存模型.png)

```c
int sort(char *p[], int count, char **p,int *ncount);
int sort(char *p[], int count, char (*p)[30],int *ncount);
int sort(char (*p)[30], int ncount,  char **p, int *ncount);

//把第一种内存模型第二种内存模型结果copy到第三种内存模型中，并排序，打印
char ** sort(char **p1, int num1, char(*p)[30], int num2, int *num3 );
```

```c
#define  _CRT_SECURE_NO_WARNINGS 
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

int sort(char **myp1 /*in*/, int num1, char (*myp2)[30], int num2, char ***myp3, int *num3)
{
	int i = 0, j = 0, k= 0;
	int  tmplen = 0;
	char **p3 = NULL;
	char *tmpP = NULL;
	p3 = (char **)malloc( (num1 + num2) * sizeof(char *)  ); //里面装的是指针
	if (p3 == NULL) 
	{
		return -1;
	}
	//根据长度分配内存空间
	for (i=0; i<num1; i++)
	{
		tmplen= strlen(myp1[i]) + 1;
		p3[i] = (char *)malloc( tmplen * sizeof(char)) ;
		if (p3[i] == NULL)
		{
			return -2;
		}
		strcpy(p3[i], myp1[i]);
	}

	for (j=0; j<num2; j++, i++)
	{
		tmplen = strlen(myp2[j]) + 1;
		p3[i] = (char *)malloc (tmplen * sizeof(char));
		if (p3[i] == NULL)
		{
			return -3;
		}
		strcpy(p3[i], myp2[j]);
	}

	tmplen = num1 + num2;
	//*num3 = num1 + num2;

	//排序
	for (i=0; i<tmplen; i++)
	{
		for (j=i+1; j<tmplen; j++)
		{
			if ( strcmp(p3[i], p3[j]) > 0 )
			{
				tmpP = p3[i];
				p3[i] = p3[j];
				p3[j] = tmpP;
			}
		}
	}

	//间接赋值
	*num3 = tmplen;
	*myp3 = p3;
	return 0;
}

void sortFree01(char **p, int len)
{
	int i = 0;
	if (p == NULL)
	{
		return ;
	}

	for (i=0; i<len ; i++)
	{
		free(p[i]);
	}
	free(p);
}

void sortFree02(char ***myp, int len) //把二级指针指向二维内存释放掉,,同时间接的修改了实参的值
{
	int i = 0;
	char **p = NULL;
	if (myp == NULL)
	{
		return ;
	}

	p  = *myp; //还原成二级指针
	if (p == NULL)
	{
		return ;
	}

	for (i=0; i<len ; i++)
	{
		free(p[i]);
	}
	free(p);
	//myp 是实参的地址
	*myp = NULL; //间接赋值是指针存在的最大意义
}

int  main()
{
	int ret = 0;
	char *p1[] = {"aaaa", "cccc", "bbbb"};
	char buf2[10][30] = {"1111", "3333", "2222"};
	char **p3 = NULL;
	int len1, len2, len3, i = 0;

	len1 = sizeof(p1)/sizeof(*p1);
	len2 = 3;

	ret = sort(p1, len1, buf2, len2, &p3, &len3);
	if (ret != 0)
	{
		printf("func sort() err:%d \n", ret);
		return ret;
	}

	for (i=0; i<len3; i++)
	{
		printf("%s\n", p3[i]);
	}

	system("pause");
	return ret;
}
```

#### 铁律7： 三级指针输出典型用法

### 关于指针的经典语录

- 指针也是一种数据类型，指针的数据类型是指它所指向内存空间的数据类型
- 间接赋值\*p是指针存在的最大意义 
- 理解指针必须和内存四区概念相结合 
- 应用指针必须和函数调用相结合（指针做函数参数）。指针是子弹，函数是枪管；子弹只有沿着枪管发射才能显示它的威力；指针的学习重点不言而喻了吧。接口的封装和设计、模块的划分、解决实际应用问题；它是你的工具。
- 指针指向谁就把谁的地址赋给指针 
- 指针指向谁就把谁的地址赋给指针，用它对付链表轻松加愉快
- 链表入门的关键是分清楚链表操作和辅助指针变量之间的逻辑关系
- C/C++语言有它自己的学习特点；若java语言的学习特点是学习、应用、上项目；那么C/C++语言的学习特点是：学习、理解、应用、上项目。多了一个步骤吧。
- 学好指针才学会了C语言的半壁江山，另外半壁江山在哪里呢？
- 理解指针关键在内存，没有内存哪来的内存首地址，没有内存首地址，哪来的指针啊。

### 变量空间的首字节地址，作为整个空间的地址

实际上，内存中的每一个字节空间都有一个地址，如果是内核有32根地址线，地址以二进制表示，其最大可寻址范围是

0000000 0000000 0000000 0000000 ~ 11111111 11111111 11111111 11111111

地址的十六进制表示

![1499868834530](images/1499868834530.png)

0xffffff：16<sup>8</sup> = 2<sup>32</sup> = 4G

既然每个字节对应的地址都是32位的，那么所有存放地址的指针变量大小也应该是32位的，即4个字节。64位系统同理。

当我们说一个地址的时候，指的就是一个字节的地址，比如 int a 的空间大小有4个字节，每个字节都有一个地址，但只有首字节地址才能作为整个a空间的地址。&a代表的就是第一个字节的地址。拿到了空间的首地址后，同时 int 类型又明确了空间大小是4个字节，所以从首地址字节顺延3个字节的空间，一共4个字节作为整个变量的空间。

### void * 万能指针

void * 无类型指针，也叫万能指针，可以指向任何类型的数据，在32位系统下，占4个字节。

这类指针指向一块内存，却没有告诉应用程序按什么类型去解读这块内存，所以无类型指针不能直接进行数据的存取操作，必须先转换成其它类型的指针才能将内存解读出来。

void * 类型的指针指向的内存是尚未确定类型的，因此我们后续可以使用强制类型转换，强行将其转为各种类型。这就是void类型的最终归宿——被强制类型转换成一个具体的类型。

C语言规定只有相同类型的指针才可以相互赋值

- void * 指针作为左值用于“接收”任意类型的指针
- void * 指针作为右值赋值给其它指针时需要强制类型转换

```c
int *p1 = NULL;
char *p2 = (char *)malloc(sizoeof(char)*20); 
```
void *指针可以指向任意变量的内存空间

```c
void *p = NULL;
int a = 10; 
p = (void *)&a; //指向变量时，最好转换为void *
//使用指针变量指向的内存时，转换为int * 
*( (int *)p ) = 11; 
printf("a = %d\n", a);
```

![1500178806133](images/1500178806133.png)

### 野指针产生的原因

指针变量和它所指向的内存空间变量是两个不同的概念

释放了指针所指的内存空间  但是指针变量本身没有重置成NULL

避免方法： 

1）定义指针的时候 初始化成NULL

2）释放指针所指向的内存空间后，把指针重置成NULL

```c
void main()
{
	char  *p1 = NULL;
	p1 = (char *)malloc(100);
	if (p1 == NULL)
	{
		return ;
	}
	strcpy(p1, "11112222");

	printf("p1:%s \n", p1);

	if (p1 != NULL)
	{
		free(p1);
		p1 = NULL;
	}

	if (p1 != NULL)
	{
		free(p1);
	}

	printf("hello...\n");
	system("pause");
	return ;
}
```

### NULL到底是什么

本质上是一个宏定义，在C语言中NULL本质是0，但是这个0不是当一个数字来解析，而是当一个内存地址来解析的，这个0其实就是0x00000000，代表内存的0地址。

(void *)0这个整体表达式表示一个指针。这个指针变量本身占四个字节，地址指向哪里取决于指针变量本身，这个指针变量的值是0，也就是说这个指针变量指向0地址（实际是0地址开始的一段内存）

```c
#ifdef __cplusplus
#define NULL 0
#else
#define NULL ((void *)0)
#endif
```

```c
int *p = NULL; // p的值是(void *)0的值，实际就是0，意思是指针p指向内存的0地址处
```

![1499701976789](images/1499701976789.png)

### 数组与指针

```c
int buf[10] = {0}; 
```

- buf：数组名，表示数组首元素的地址（&buf[0]），是一个常量，不可变（为了保证释放内存的安全性）。
- buf[0]：数组的首元素
- &buf[0]：数组首元素的地址，等价于buf
- &buf：表示数组首地址（整个数组的地址），是一个地址常量，只能作为右值
- buf+1 步长是4字节，&buf+1 步长是4*10字节

### 数组指针作为函数参数

将数组作为函数参数的本质就是将指向数组首元素的指针作为函数参数。数组指针作为函数参数时，由于无法获取数组的长度，所以根据需求传入数组的长度。

### 指针函数

```
int * add(int x, int y);
```

### 函数指针

函数指针用于指向函数的入口地址

函数名的实质其实就是函数这段代码的首地址

函数名数组名的最大区别就是：函数名做右值时加不加&的效果和意义都是一样的，但是数组名做右值时加不加&的效果和意义是不一样的。

```c
int (*p)(int, int);
int add(int x, int y);
p = add;
(*p)(3,5);
```

函数指针作为函数参数

```
void func(int (*p)(int, int), int b, int n);
```

#### 函数指针基础语法

函数指针用于指向一个函数，函数名是函数体的入口地址

```C++
#define  _CRT_SECURE_NO_WARNINGS 
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

//数组指针 语法 梳理
//定义一个数组类型   
//int a[10];

//定义一个指针数组类型 
//定义一个指向数组类型的指针，数组类的指针

void main01()
{
	int a[10]; //a代表的是数组首元素的地址  &a代表整个数组的地址  a+1 4  &a+1步长 40
	{
		//定义一个数组类型
		typedef int (myTypeArray)[10];
		myTypeArray myArray;
		myArray[0] = 10;
		printf("%d \n", myArray[0]);
	}

	{
		//定义一个数组指针类型 
		typedef int (*PTypeArray)[10];  //int *p 

		PTypeArray myPArray; //sizeof(int) *10
		myPArray = &a;
		//int b = 10;
		//int *p = NULL;
		//p = &b;
		(*myPArray)[0] = 20;

		printf("a[0]: %d \n", a[0]);
	}

	{
		//定义一个指向数组类型的指针，数组类的指针
		int (*MyPointer)[10]; //变量 告诉C编译器 给我分配内存
		MyPointer = &a;
		(*MyPointer)[0] = 40;
		printf("a[0]: %d \n", a[0]);
	}
	
	system("pause");
	return ;
}

//函数指针语法梳理
//1 如何定义一个函数类型
//2 如何定义一个函数指针类型
//3 如何定义一个 函数指针  (指向一个函数的入口地址)

int add(int a, int b)
{
	printf("func add ....\n");
	return a +b;
}

void main02()
{
	add(1, 2); //直接调用调用，函数名就是函数的入口地址 

	//定义一个函数类型
	{
		typedef int (MyFuncType)(int a, int b); //定义了一个类型
		MyFuncType *myPointerFunc = NULL; //定义了一个指针, 指向某一种类的函数

		myPointerFunc  = &add;  //细节
		myPointerFunc(3, 4); //间接调用

		myPointerFunc  = add;  //细节，C 过程 兼容历史版本的原因
		myPointerFunc(3, 4); //间接调用
	}

	//定义一个函数指针类型
	{
		typedef int (*MyPointerFuncType)(int a, int b); //int * a = NULL;
		MyPointerFuncType myPonterFunc; //定义一个指针
		myPonterFunc = add;
		myPonterFunc(5, 6);
	}

	//函数指针 
	{
		int (*MyPonterFunc)(int a, int b); //定义了一个变量
		MyPonterFunc = add;
		MyPonterFunc(7, 8);
	}

	system("pause");
}
```

#### 函数指针做函数参数

当函数指针 做为函数的参数，传递给一个被调用函数，被调用函数就可以通过这个指针调用外部的函数，这就形成了回调

```C++
#define  _CRT_SECURE_NO_WARNINGS 
#include <stdlib.h>
#include <string.h>
#include <stdio.h>

int myadd(int a, int b)  //子任务的实现者
{
	printf("func add() do...\n");
	return a + b;
}
int myadd2(int a, int b)  //子任务的实现者
{
	printf("func add2() do...\n");
	return a + b;
}
int myadd3(int a, int b)  //子任务的实现者
{
	printf("func add3() do...\n");
	return a + b;
}
int myadd4(int a, int b)  //子任务的实现者
{
	printf("func add4() do...\n");
	return a + b;
}

//定义了一个类型 
typedef int (*MyTypeFuncAdd)(int a, int b);

//函数指针做函数参数
int MainOp(MyTypeFuncAdd myFuncAdd)
{
	int c = myFuncAdd(5, 6);
	return c;
}

// int (*MyPointerFuncAdd)(int a, int b)
int MainOp2(int (*MyPointerFuncAdd)(int a, int b) )
{
	int c = MyPointerFuncAdd(5, 6); //间接调用
	return c;
}

//间接调用 
//任务的调用和任务的编写可以分开
void main()
{
	/*
	MyTypeFuncAdd  myFuncAdd = NULL;
	myadd(1, 2); //直接调用

	myFuncAdd = myadd; 
	myFuncAdd(3, 4); //间接调用 

	MainOp2(myadd);
	MainOp(myadd);
	*/

	//在mainop框架 没有发生任何变化的情况下
	MainOp(myadd2);
	MainOp(myadd3);
	MainOp(myadd4);

	system("pause");
	return ;
}
```
回调函数：函数的调用和函数的实现 有效的分离

回调函数的本质：提前做了一个协议的约定（把函数的参数、函数返回值提前约定）

请思考：C编译器通过那个具体的语法，实现解耦合的？

C++编译器通过多态的机制(提前布局vptr指针和虚函数表,找虚函数入口地址来实现)

### 函数指针实战2——结构体内嵌函数指针实现分层

cal.h

```c
#ifndef __CAL_H__
#define __CAL_H__

typedef int (*pFunc)(int, int);

struct cal_t{
	int a;
	int b;
	pFunc p;
};

int calculator(const struct cal_t *p);

#endif
```

framework.c

```c
#include "cal.h"

int calculator(const struct cal_t *p){
	p->p(p->a, p->b);
}
```

cal.c

```c
#include "cal.h"
#include<stdio.h>

int add(int a, int b){
	return a + b;
}

int sub(int a, int b){
	return a - b;
}

int multiply(int a, int b){
	return a * b;
}

int divide(int a, int b){
	return a / b;
}

int main(void){
	
	int ret = 0;
	
	struct cal_t myCal;
	
	myCal.a = 12;	
	myCal.b = 4;
	myCal.p = divide;
	
	ret = calculator(&myCal);
	
	printf("ret = %d.\n",ret);
	
	return 0;
}
```

### 指针数组

一个元素数据类型为指针的数组

```
int *p[5];
```

### 带参数的main()函数

```
int main(int argc, char *argv[]);
```

- argc 表示在命令行中输入的参数个数
- argv 参数是字符串指针数组，其各元素值为命令行中各字符串的首地址

例如：`main.exe arg1 arg2 arg3` ，argc为4，argv指针数组中元素指向的字符串分别是：“main.exe”、 “arg1”、“arg2 ”、“arg3”

### 分析数组指针与指针数组的表达式

```c
int (*p)[10]; // 数组指针
int *p[10]; // 指针数组
```

规律：第一步找核心，第二步找结合（根据运算符优先级）

如果核心和星号\*结合，表示核心是指针；如果核心和中括号[]结合，表示核心是数组；如果核心和小括号()结合，表示核心是函数

### 用typedef定义数据类型

- 给类型起别名
- 简化类型，让程序更易理解和书写
- 创建平台无关类型，方便移植。

```c
typedef char Line[80]; // Line就是别名，代表80个元素的字符数组
typedef int (*fun_ptr)(int, int); // 函数指针
typedef struct Student{
  	char name[10];
  	int age;
} Student;
Lint t;
fun_ptr p;
Student stu;
```

### 回调函数

### 内存申请

- malloc() 未初始化，返回的是 void * 类型的指针（地址），该地址是本次申请的内存空间的首字节地址。
- calloc() 经过初始化的内存空间
- realloc() 可以实现内存分配和内存释放的功能
- 内存泄露

内存回收

- free() 

### 内存操作

- memset() 用于对指定的内存空间进行初始化操作
- memcopy()
- memmove() 可以正确处理内存重叠问题
- memcmp() 比较两个内存空间中数据的大小

![1499528179884](images/1499528179884.png)

### 指针与const

C语言中的const是个冒牌货，变量的值可以通过指针修改

```c
const int *p; // 常量指针，指针所指向变量的值不能被改变
int * const p; // 指针常量，一个常量，即指针存放的地址不能被改变
const int * const; // 指向常量的常指针，指针指向的地址不能被改变，且所指向地址中的值也不能被改变
// 通过指针可以间接修改const修饰的变量
const int a = 10;
int *p = &a;
*p = 20; 
```

### 指针的输入输出特性

- 指针做输出：被调用函数分配内存
- 指针做输入：主调用函数 分配内存

```c
int  getMem41(char **myp1/*out*/ , int *mylen1 /*out*/,  char **myp2 /*out*/, int *mylen2 /*out*/)
{
	int		ret = 0;
	char	*tmp1, *tmp2;

	tmp1 = (char *)malloc(100);
	strcpy(tmp1, "1132233");

	//间接赋值 
	*mylen1 = strlen(tmp1);  //1级指针
	*myp1 = tmp1; //2级指针的间接赋值

	tmp2 = (char *)malloc(200);
	strcpy(tmp2, "aaaaavbdddddddd");

	*mylen2 = strlen(tmp2);  //1级指针
	*myp2 = tmp2; //2级指针的间接赋值

	return ret;
}

char *  getMem42(int num)
{
	int		ret = 0;
	char	*tmp1;

	tmp1 = (char *)malloc(num);
	strcpy(tmp1, "1132233");

	return tmp1;
}

int  main44()
{
	int		ret = 0;
	char	*p1 = NULL;
	int		len1 = 0;
	char	*p2 = NULL;
	int		len2 = 0; 

	ret = getMem41(&p1, &len1, &p2, &len2);
	if (ret != 0)
	{
		printf("func getMem41() err:%d \n", ret);
		return ret;
	}
  
	printf("p1:%s \n", p1);
	printf("p2:%s \n", p2);
  
	if (p1 != NULL)
	{
		free(p1);
		p1 = NULL;
	}
	if (p2 != NULL)
	{
		free(p2);
		p2 = NULL;
	}

	p1 = getMem42(100);
	printf("p1:%s \n", p1);
	if (p1 != NULL)
	{
		free(p1);
		p1 = NULL;
	}

	printf("p1:%d \n", p1);
	system("pause");
	return ret;
}
```

```c
//求文件中的两段话的长度
int getMem(char **myp1, int *mylen1, char **myp2, int *mylen2)
{
	char *tmp1 = NULL;
	char *tmp2 = NULL;
	tmp1 = (char *)malloc(100);
	if (tmp1 == NULL)
	{
		return -1;
	}
	strcpy(tmp1, "abcdefg");
	*mylen1 = strlen(tmp1);
	*myp1 = tmp1; //间接修改实参p1的值

	tmp2 = (char *)malloc(100);
	if (tmp2 == NULL)
	{
		return -2;
	}
	strcpy(tmp2, "11122233333");
	*mylen2 = strlen(tmp2);
	*myp2 = tmp2; //间接修改实参p1的值
	return 0;
}

int getMem_Free(char **myp1)
{
	/*
	if (myp1 == NULL)
	{
		return ;
	}
	free(*myp1);  //释放完指针变量所指的内存空间
	*myp1 = NULL;  //把实参修改成nULL
	*/
	char *tmp = NULL;
	if (myp1 == NULL)
	{
		return -1;
	}
	tmp = *myp1;
	free(tmp);  //释放完指针变量所指的内存空间
	*myp1 = NULL;  //把实参修改成nULL
	return 0;
}

void main11()
{
	char  *p1 = NULL;
	int len1 = 0;

	char *p2 = NULL;
	int len2 = 0;

	int ret = 0;

	ret  = getMem(&p1, &len1, &p2, &len2 );

	printf("p1: %s \n", p1);
	printf("p2: %s \n", p2);

	getMem_Free(&p1);
	getMem_Free(&p2);  

	system("pause");
	return ;
}

int getMem_Free0(char *myp1)
{
	if (myp1 == NULL)
	{
		return -1;
	}
	free(myp1);  //释放完指针变量所指的内存空间
	myp1 = NULL;
	return 0;
}

void main14()
{
	char  *p1 = NULL;
	int len1 = 0;

	char *p2 = NULL;
	int len2 = 0;

	int ret = 0;

	ret  = getMem(&p1, &len1, &p2, &len2 );

	printf("p1: %s \n", p1);
	printf("p2: %s \n", p2);

	if (p1 != NULL)
	{
		free(p1);
		p1 = NULL;
	}
	if (p2 != NULL)
	{
		free(p2);
		p2 = NULL;
	}
	//在被调用函数中把p1所指向的内存给释放掉 ，但是实参p1不能被修改成NULLL，有野指针现象
	getMem_Free0(p1);  
	getMem_Free0(p2);  

	system("pause");
	return ;
}
```

### 玩转多级指针

```c
char **getMem51(int num)
{
	int i = 0;
	char **p2 = NULL;
	p2 = (char **)malloc(sizeof(char *) * num);
	if (p2 == NULL)
	{
		return NULL;
	}

	for (i=0; i<num; i++)
	{
		p2[i] = (char *)malloc(sizeof(char)  * 100  ); //char buf[100];
		sprintf(p2[i], "%d%d%d", i+1, i+1, i+1);
	}
	return p2;
}

int getMem52(char ***p3 , int num)
{
	int i = 0;
	char **tmp = NULL;

	if (p3 == NULL)
	{
		return -1;
	}
	
	tmp = (char **)malloc(sizeof(char *) * num);
	if (tmp == NULL)
	{
		return NULL;
	}

	for (i=0; i<num; i++)
	{
		tmp[i] = (char *)malloc(sizeof(char)  * 100  ); //char buf[100];
		sprintf(tmp[i], "%d%d%d", i+1, i+1, i+1);
	}
	*p3 = tmp; 
	return 0;
}

void getMem52_Free(char ***p3 , int num)
{
	int i = 0;
	char **tmp = NULL;

	if (p3 == NULL)
	{
		return ;
	}
	tmp = *p3; 

	for (i=0; i<num; i++)
	{
		free(tmp[i]);
	}
	free(tmp);
	 
	*p3 = NULL; //把实参赋值成null
}

void main66()
{
	int i = 0, j = 0;
	char **p2 = NULL;
	int num = 5;
	char *tmp = NULL;
	char tmpbuf[100];
	//p2 = getMem51(num);

	getMem52(&p2, num);

	for (i=0; i<num; i++)
	{
		printf("%s \n", p2[i]);
	}

	getMem52_Free(&p2, num);
	printf("hello...\n");
	system("pause");
	return ;
}
```

### 两个辅助指针变量挖字符串

```c
//分清楚赋值指针变量和操作逻辑之间的关系
int splitString(const char *buf1, char c, char buf2[10][30], int *count)
{
	//strcpy(buf2[0], "aaaaa");
	//strcpy(buf2[1], "bbbbbb");
	char *p=NULL, *pTmp = NULL;
	int	tmpcount = 0;

	//1 p和ptmp初始化
	p = buf1;
	pTmp = buf1;

	do 
	{
		//2 检索符合条件的位置 p后移  形成差值 挖字符串
		p = strchr(p, c);
		if (p != NULL)
		{
			if (p-pTmp > 0)
			{
				strncpy(buf2[tmpcount], pTmp,  p-pTmp);
				buf2[tmpcount][p-pTmp]  = '\0';  //把第一行数据变成 C风格字符串
				tmpcount ++;
				//3重新 让p和ptmp达到下一次检索的条件
				pTmp = p = p + 1;
			}
		}
		else
		{
			break;
		}
	} while (*p!='\0');
	
	*count = tmpcount;
	return 0;
}

void main()
{
	int ret = 0, i = 0;
	char *p1 = "abcdef,acccd,eeee,aaaa,e3eeeee,sssss,";
	char cTem= ',';
	int nCount;

	char myArray[10][30];

	ret = splitString(p1, cTem, myArray, &nCount);
	if (ret != 0)
	{
		printf("fucn splitString() err: %d \n", ret);
		return ret;
	}

	for (i=0; i<nCount; i++ )
	{
		printf("%s \n", myArray[i]);
	}
	printf("hello...\n");
	system("pause");
	return ;
}

//作业 用第三种内存模型求解问题
/*
int spitString2(const char *buf1, char c, char ***pp, int *count)
{
	
}

char ** spitString3(const char *buf1, char c, int *count)
{

}
*/
```