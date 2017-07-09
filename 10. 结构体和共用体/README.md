---
typora-copy-images-to: images
---

### 结构体变量的内存分配

结构体变量占据的内存大小是按照字节对齐的机制来分配的

### 字节对齐

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