Container_of在Linux内核中是一个常用的宏，用于从包含在某个结构中的指针获得结构本身的指针，通俗地讲就是通过结构体变量中某个成员的首地址进而获得整个结构体变量的首地址。

container_of(ptr, type, member) 

- ptr:表示结构体中member的地址
- type:表示结构体类型
- member:表示结构体中的成员

通过ptr的地址可以返回结构体的首地址

Container_of的定义如下

```c
#define container_of(ptr, type, member) ({ \
    const typeof( ((type *)0)->member ) *__mptr = (ptr); \
    (type *)( (char *)__mptr - offsetof(type,member) );})  
```

其实它的语法很简单，只是一些指针的灵活应用，它分两步：

第一步，首先定义一个临时的数据类型（通过`typeof( ((type *)0)->member )`获得）与ptr相同的指针变量`__mptr`，然后用它来保存ptr的值。

第二步，用`(char *)__mptr`减去member在结构体中的偏移量，得到的值就是整个结构体变量的首地址（整个宏的返回值就是这个首地址）。

其中的语法难点就是如何得出成员相对结构体的偏移量？

通过例子说明，如清单1： 

```c
/* linux-2.6.38.8/include/linux/compiler-gcc4.h */  
#define __compiler_offsetof(a,b) __builtin_offsetof(a,b)  
  
/* linux-2.6.38.8/include/linux/stddef.h */  
#undef offsetof  
#ifdef __compiler_offsetof  
#define offsetof(TYPE,MEMBER) __compiler_offsetof(TYPE,MEMBER)  
#else  
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)  
#endif  
  
#include <stdio.h>  
  
struct test_struct {  
    int num;  
    char ch;  
    float fl;  
};  
  
int main(void)  
{  
    printf("offsetof(struct test_struct, num) = %d\n",   
            offsetof(struct test_struct, num));  
      
    printf("offsetof(struct test_struct,  ch) = %d\n",   
            offsetof(struct test_struct, ch));  
      
    printf("offsetof(struct test_struct,  fl) = %d\n",   
            offsetof(struct test_struct, fl));  
      
    return 0;  
}  
```

说明，`__builtin_offsetof(a,b)`是GCC的内置函数，可认为它的实现与`((size_t) &((TYPE *)0)->MEMBER)`这段代码是一致的。

例子输出结果： 

```
offsetof(struct test_struct, num) = 0  
offsetof(struct test_struct,  ch) = 4  
offsetof(struct test_struct,  fl) = 8  
```

其中代码难以理解的地方就是它灵活地运用了0地址。如果觉得`&( (struct test_struct *)0 )->ch`这样的代码不好理解，那么我们可以假设在0地址分配了一个结构体变量`struct test_struct a`，然后定义结构体指针变量p并指向`a（struct test_struct *p = &a）`，如此我们就可以通过`&p->ch`获得成员ch的地址。由于a的首地址为0x0，所以成员ch的首地址为0x4。

![](images/container_of.png)

最后通过强制类型转换（size_t）把一个地址值转换为一个整数。

分析完container_of的定义，接下来举两个例子来体会一下它的使用方法。

正确的例子，如清单2： 

```c
/* linux-2.6.38.8/include/linux/compiler-gcc4.h */  
#define __compiler_offsetof(a,b) __builtin_offsetof(a,b)  
  
/* linux-2.6.38.8/include/linux/stddef.h */  
#undef offsetof  
#ifdef __compiler_offsetof  
#define offsetof(TYPE,MEMBER) __compiler_offsetof(TYPE,MEMBER)  
#else  
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)  
#endif  
  
/* linux-2.6.38.8/include/linux/kernel.h * 
 * container_of - cast a member of a structure out to the containing structure 
 * @ptr: the pointer to the member. 
 * @type:   the type of the container struct this is embedded in. 
 * @member:    the name of the member within the struct. 
 * 
 */  
#define container_of(ptr, type, member) ({ \
    const typeof( ((type *)0)->member ) *__mptr = (ptr); \
    (type *)( (char *)__mptr - offsetof(type,member) );})  
  
#include <stdio.h>  
  
struct test_struct {  
    int num;  
    char ch;  
    float fl;  
};  
  
int main(void)  
{  
    struct test_struct init_test_struct = { 99, 'C', 59.12 };  
  
    char *char_ptr = &init_test_struct.ch;  
  
    struct test_struct *test_struct = container_of(char_ptr, struct test_struct, ch);  
      
    printf(" test_struct->num = %d\n test_struct->ch = %c\n test_struct->fl = %f\n",   
        test_struct->num, test_struct->ch, test_struct->fl);  
      
    return 0;  
}  
```

例子输出结果： 

```
test_struct->num = 99  
test_struct->ch = C  
test_struct->fl = 59.119999  
```

不适当的例子，如清单3： 
```c
/* linux-2.6.38.8/include/linux/compiler-gcc4.h */  
#define __compiler_offsetof(a,b) __builtin_offsetof(a,b)  
  
/* linux-2.6.38.8/include/linux/stddef.h */  
#undef offsetof  
#ifdef __compiler_offsetof  
#define offsetof(TYPE,MEMBER) __compiler_offsetof(TYPE,MEMBER)  
#else  
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)  
#endif  
  
/* linux-2.6.38.8/include/linux/kernel.h * 
 * container_of - cast a member of a structure out to the containing structure 
 * @ptr: the pointer to the member. 
 * @type:   the type of the container struct this is embedded in. 
 * @member:    the name of the member within the struct. 
 * 
 */  
#define container_of(ptr, type, member) ({ \
    const typeof( ((type *)0)->member ) *__mptr = (ptr); \
    (type *)( (char *)__mptr - offsetof(type,member) );})  
  
#include <stdio.h>  
  
struct test_struct {  
    int num;  
    char ch;  
    float fl;  
};  
  
int main(void)  
{  
    char real_ch = 'A';  
    char *char_ptr = &real_ch;  
  
    struct test_struct *test_struct = container_of(char_ptr, struct test_struct, ch);  
  
    printf(" char_ptr = %p  test_struct = %p\n\n", char_ptr, test_struct);  
  
    printf(" test_struct->num = %d\n test_struct->ch = %c\n test_struct->fl = %f\n",   
        test_struct->num, test_struct->ch, test_struct->fl);  
      
    return 0;  
}  
```
例子输出结果： 

```
char_ptr = 0xbfb72d7f  test_struct = 0xbfb72d7b  
  
test_struct->num = -1511000897  
test_struct->ch = A  
test_struct->fl = 0.000000
```

注意，由于这里并没有一个具体的结构体变量，所以成员num和fl的值是不确定的。

### container_of 宏

```c
// typeof() 获取变量的数据类型
#define container_of(ptr, type, member) ({ 
const typeof( ((type *)0)->member ) *__mptr = (ptr);
(type *)( (char *)__mptr - offsetof(type,member));})

// 意思是声明一个与member同一个类型的指针常量 *__mptr,并初始化为ptr
const typeof( ((type *)0->member ) *__mptr = (ptr); 
// 意思是__mptr的地址减去member在该struct中的偏移量得到的地址, 再转换成type型指针. 
// 该指针就是member的入口地址了
(type *)( (char *)__mptr - offsetof(type,member) ); 
```

```c
#include
struct student{
	char name[20]; 
	char sex;
}stu={"zhangsan",'m'};

int main(){
	struct student *stu_ptr;    //存储container_of宏的返回值
	int offset;    //存储offsetof宏的返回值
	//下面三行代码等同于 container_of（＆stu.sex,struct student, sex ）参数带入的情形

	const typeof(((struct student*)0)->sex) *_mptr = &stu.sex;
	//首先定义一个 _mptr指针， 类型为struct student结构体中sex成员的类型
	//typeof 为获取（（（struct student *）0）->sex)的类型，此处此类型为char
	//（（struct student＊）0）在offsetof处讲解

	offset = (int)(&((struct student *)0)->sex);
	stu_ptr = (struct student *)((char*)_mptr - offset);
  
	printf("offsetof stu.sex = %d\n",offset); 
	printf("stu_ptr->name:%s\tstu_ptr->sex:%c\n", stu_ptr->name, stu_ptr->sex);
	return 0;
}
```

它的作用显而易见，那就是根据一个结构体变量中的一个域成员变量的指针来获取指向整个结构体变量的指针。比如，有一个结构体变量，其定义如下

```c
struct demo_struct {
    type1 member1;
    type2 member2;
    type3 member3;
    type4 member4;
};
struct demo_struct demo;
```

同时，在另一个地方，获得了变量demo中的某一个域成员变量的指针，比如：

```c
type3 *memp = get_member_pointer_from_somewhere();
```

此时，如果需要获取指向整个结构体变量的指针，而不仅仅只是其某一个域成员变量的指针，我们就可以这么做：

`struct demo_struct *demop = container_of(memp, struct demo_struct, member3);`

首先，我们将`container_of(memp, struct demo_struct, type3)`根据宏的定义进行展开如下：

```c
struct demo_struct *demop = ({
const typeof( ((struct demo_struct *)0)->member3 ) *__mptr = (memp);
(struct demo_struct *)( (char *)__mptr - offsetof(struct demo_struct, member3) );})
```
其中，typeof是GNU C对标准C的扩展，它的作用是根据变量获取变量的类型。因此，上述代码中的第2行的作用是首先使用typeof获取结构体域变量member3的类型为 type3，然后定义了一个type3指针类型的临时变量`__mptr`，并将实际结构体变量中的域变量的指针memp的值赋给临时变量`__mptr`。

假设结构体变量demo在实际内存中的位置如下图所示：
```
demo
+-------------+ 0xA000
|   member1              |
+-------------+ 0xA004
|   member2             |
|                                |
+-------------+ 0xA010
|   member3             |
|                                |
+-------------+ 0xA018
|   member4             |
+-------------+
```
则，在执行了上述代码的第2行之后__mptr的值即为0xA010。

再看上述代码的第3行，其中需要说明的是offsetof，它定义在include/linux/stddef.h中，其定义如下：

```
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)
```
同样，我们将上述的offsetof调用展开，即为：

```c
(struct demo_struct *)( (char *)__mptr - ((size_t) &((struct demo_struct *)0)->member3) );
```
可见，offsetof的实现原理就是取结构体中的域成员相对于地址0的偏移地址，也就是域成员变量相对于结构体变量首地址的偏移。

因此，`offsetof(struct demo_struct, member3)`调用返回的值就是member3相对于demo变量的偏移。结合上述给出的变量地址分布图可知，`offsetof(struct demo_struct, member3)`将返回0x10。

于是，由上述分析可知，此时，`__mptr==0xA010，offsetof(struct demo_struct, member3)==0x10`。
因此，`(char *)__mptr - ((size_t) &((struct demo_struct *)0)->member3) == 0xA010 - 0x10 == 0xA000`，也就是结构体变量demo的首地址（如上图所示）。

由此，`container_of`实现了根据一个结构体变量中的一个域成员变量的指针来获取指向整个结构体变量的指针的功能。