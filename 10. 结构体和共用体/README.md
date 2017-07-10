---
typora-copy-images-to: images
---

### 结构体变量的内存分配

结构体变量占据的内存大小是按照字节对齐的机制来分配的

### 字节对齐

结构体字节对齐存储的原因是符合内存硬件的特性，访问速度性能高，但是牺牲了内存空间

```c
struct {
	char a;
	double b;
	int c;
	short d;
} s;
```

- 地址偏移量是成员变量的基本数据类型的整数倍

![1499570642569](images/1499570642569.png)

- 结构体变量的总大小是结构体最宽基本类型成员大小的整数倍

![1499570995879](images/1499570995879.png)

```c
#include<stdio.h>
#include<stddef.h>

/* Offset of member MEMBER in a struct of type TYPE. */
// #define offsetof(TYPE, MEMBER) __builtin_offsetof (TYPE, MEMBER)
// #define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)

typedef struct Test{
	char a;
	double b;
	int c;
	short d;
} Test;

int main(){
	
	printf("%d \n",offsetof(Test,a)); // 0
	printf("%d \n",offsetof(Test,b)); // 8
	printf("%d \n",offsetof(Test,c)); // 16
	printf("%d \n",offsetof(Test,d)); // 20
	
	return 0;
}
```

### 结构体指针

```c
struct Student s；
struct Student *p = &s;
printf("%s\n", p->name);
```

### union

允许多个成员使用同一块内存

### typedef —— 给数据类型取别名 

```c
typedef struct Student{
  	int age;
  	char name[10];
} STU;
STU student;
```
### 结构体的浅拷贝和深拷贝

```c
typedef struct Teacher
{
	char name[64];
	int age ;
	char *pname2;
}Teacher;

//编译器的=号操作,只会把指针变量的值,从from  copy 到 to, 
//但不会把指针变量所指的 内存空间 给copy过去//浅copy

//结构体中套一个 1级指针 或 2级指针 
//如果想执行深copy,再显式的分配内存
void copyTeacher(Teacher *to, Teacher *from)
{
	*to = *from;
	to->pname2 = (char *)malloc(100);
	strcpy(to->pname2, from->pname2);
	//memcpy(to, from , sizeof(Teacher));
}
void main51()
{
	Teacher t1;
	Teacher t2;

	strcpy(t1.name, "name1");
	t1.pname2 = (char *)malloc(100);
	strcpy(t1.pname2, "ssss");

	//t1 copy t2
	copyTeacher(&t2, &t1);

	if (t1.pname2 != NULL)
	{
		free(t1.pname2);
		t1.pname2 = NULL;
	}

	if (t2.pname2 != NULL)
	{
		free(t2.pname2);
		t2.pname2 = NULL;
	}

	system("pause");
	return ;
}
```