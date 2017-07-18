在书写代码与阅读代码的时候，经常会看到这一句代码： `typedef void *HANDLE` ，它是何方神圣呢？如何理解呢？

不理解它的时候，感觉它很神奇，知道它以后，它就是个typedef的定义，只不过是`void*`类型罢了，也就是HANDLE等价于`void *`，我们可以叫它披着句柄皮的指针(PS:指针和句柄是有区别的，在这说句废话)；

对于`void*` 神通广大，因为它作为函数参数or函数返回值，能够接受任何类型的指针；也就是来者不拒，是指针就接受(so open ,Aha)。但应用的时候，应该注意，`void *` 必须指向具体的类型；例如：
```c
void * HappyPt;
int* pi=static_cast<int*>HappyPt;; //注意喽，在这里具体到了int*哦！
```
具体用法如下所示：

1.还记得它吗？
```c
void *  __cdecl memset(_Out_opt_bytecapcount_(_Size) void * _Dst, _In_ int _Val, _In_ size_t _Size);
```
这是memset这个函数的原型，在这里你可以毫不留情的把`void*` 换成HANDLE，memset就是为了将传递进来的地址开始的n个字节的值全部赋值成某个特定的值，所以没必要知道是什么类型的指针，就用void类型指针代替了。用作参数的时候，我们一般不关心传递进来的地址类型；

用作返回值的时候，一般是返回一个地址，至于这个地址你要做什么用那是你决定的，最常见的就是malloc了，它返回void类型指针作为分配好的内存区的首地址，至于是什么类型，就需要用户自己来指定了。

示例
```c
typedef void *HANDLE;
HANDLE m_exitThreadEvent; 
m_exitThreadEvent = CreateEvent(NULL,FALSE,FALSE,NULL);

HANDLE CreateEvent(
  LPSECURITY_ATTRIBUTES lpEventAttributes, 
  BOOL bManualReset, 
  BOOL bInitialState, 
  LPTSTR lpName 
);
```
## void

### void的作用

- 对函数参数的限定：当不需要传入参数时，即 `function (void);`
- 对函数返回值的限定：当函数没有返回值时，即 `void function(void);`

### void指针的作用

（1）void指针可以指向任意的数据类型，即任意类型的指针可以赋值给void指针

```c
int *a;
void *p;
p=a;
```
如果void指针赋值给其他类型，则需要强制转换；`a=（int *）p;`

（2）在ANSI C标准中不允许对void指针进行算术运算，因为没有特定的数据类型，即在内存中不知道移动多少个字节；而在GNU标准中，认为void指针和char指针等同。

### 应用

（1）void指针一般用于应用的底层，比如malloc函数的返回类型是void指针，需要再强制转换；
（2）文件句柄HANDLE也是void指针类型，这也是句柄和指针的区别；
（3）内存操作函数的原型也需要void指针限定传入参数：

```c
void * memcpy (void *dest, const void *src, size_t len);
void * memset (void *buffer, int c, size_t num );
```
（4）面向对象函数中底层对基类的抽象。