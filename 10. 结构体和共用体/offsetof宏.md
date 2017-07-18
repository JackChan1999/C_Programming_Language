---
typora-copy-images-to: images
---

### offsetof 宏

计算结构体中某元素相对于结构体首字节地址的偏移量

offsetof宏返回的是MEMBER成员在内存中的实际地址。又因为整个结构体的起始地址是0，因此MEMBER成员的实际地址在数值上就等于MEMBER成员相对于结构体首地址的偏移量

巧妙之处在于将地址0强制转换为type类型的指针，从而定位到member在结构体中偏移位置。编译器认为0是一个有效的地址，从而认为0是type指针的起始地址。

```c
#include<stdio.h>
#define offsetof(TYPE, MEMBER) (size_t)&(((TYPE*)0)->MEMBER)
// TYPE 是结构体类型，MEMBER是结构体中的一个元素的元素名
// 这个宏返回的是MEMBER元素相对于整个结构体变量的首地址的偏移量，类型是 int
// & ((TYPE *)0)->MEMBER 等价于 & (((TYPE *)0)->MEMBER) - & (((TYPE *)0)
```
![1500258263590](images/1500258263590.png)
```c
#include <stdio.h>  
#define offsetof(TYPE,MEMBER) ((int) &((TYPE *)0)->MEMBER)  
#pragma pack(4)  
struct ptr  
{  
    char a ;   
    short b ;   
    int  c ;   
    double d ;  
};  
#pragma pack()  
int main(void)  
{  
    struct ptr Pt ;  
    printf("ptr:%d\n",sizeof(struct ptr));//16  
      
    //相对地址偏移量   
    int offset = offsetof(struct ptr,a);  //0  
    printf("offset:%d\n",offset);  
    int offset1 = offsetof(struct ptr,b); //2  
    printf("offset1:%d\n",offset1);  
    int offset2 = offsetof(struct ptr,c); //4  
    printf("offset2:%d\n",offset2);  
    int offset3 = offsetof(struct ptr,d); //8  
    printf("offset3:%d\n",offset3);  
    return 0 ;  
}
```

测试程序

```c
#include <stdio.h>
#include <stdlib.h>

#define NAME_STR_LEN  32

#define offsetof(type, member) (size_t)&(((type*)0)->member)

#define container_of(ptr, type, member) ({ \
        const typeof( ((type *)0)->member ) *__mptr = (ptr); \
        (type *)( (char *)__mptr - offsetof(type,member) );})

typedef struct student_info
{
    int  id;
    char name[NAME_STR_LEN];
    int  age;
}student_info;


int main()
{
    size_t off_set = 0;
    off_set = offsetof(student_info, id);
    printf("id offset: %u\n",off_set);
    off_set = offsetof(student_info, name);
    printf("name offset: %u\n",off_set);
    off_set = offsetof(student_info, age);
    printf("age offset: %u\n",off_set);
    student_info *stu = (student_info *)malloc(sizeof(student_info));
    stu->age = 10;
    student_info *ptr = container_of(&(stu->age), student_info, age);
    printf("age:%d\n", ptr->age);
    printf("stu address:%p\n", stu);
    printf("ptr address:%p\n", ptr);
    return 0;
}
```

测试结果

![](http://images.cnitblog.com/blog/305504/201312/12235044-c15ba5efb23a4bee8bccd4442d7e6086.png)

对这个宏的讲解我们大致可以分为以下4步进行讲解：

1. ( (TYPE *)0 ) 0地址强制 "转换" 为 TYPE结构类型的指针；
2. ((TYPE *)0)->MEMBER 访问TYPE结构中的MEMBER数据成员；
3. &( ( (TYPE *)0 )->MEMBER) 取出TYPE结构中的数据成员MEMBER的地址；
4. (size_t)(&(((TYPE*)0)->MEMBER)) 结果转换为size_t类型。

offsetof的巧妙之处在于将0地址强制转换为TYPE结构类型的指针，TYPE结构以内存空间首地址0作为起始地址，则成员地址自然为偏移地址。

```c
#include<stdio.h>  
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)4)->MEMBER)  
struct test_struct {  
    int num;  
    char ch;  
    float f1;  
};  
int main(void)  
{  
    printf("offsetof (struct test_struct,num)=%d\n",offsetof(struct test_struct,num)-4);  
    printf("offsetof (struct test_struct,ch)=%d\n",offsetof(struct test_struct,ch)-4);  
    printf("offsetof (struct test_struct,f1)=%d\n",offsetof(struct test_struct,f1)-4);  
    return 0;  
}  
```

运行结果为：

```
offsetof (struct test_struct,num)=0  
offsetof (struct test_struct,ch)=4  
offsetof (struct test_struct,f1)=8 
```

代码中没有使用0，而是使用的4，所以在最终计算出的结果部分减去了一个4才是偏移地址，当然实际使用中我们都是用的是0