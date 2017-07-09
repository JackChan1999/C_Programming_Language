---
typora-copy-images-to: images
---

指针是一种变量，在32位系统下占4字节，在64位系统下占8字节

指针和指针变量是两个完全不同的概念，指针是一个地址，而指针变量是存放地址（指针）的变量。

指针指向谁，就把谁的地址赋给指针

指针变量和它所指向的内存空间变量是两个不同的概念

不断的给指针赋值，相当于不停的改变指针的指向

理解指针的关键,是内存. 没有内存哪里的指针 

间接赋值是指针存在的最大意义

- 空指针NULL
- 野指针
- 万能指针void *
- 函数指针：指向函数（入口地址）的指针
- 指针函数：返回值为指针的函数

### 万能指针

C语言规定只有相同类型的指针才可以相互赋值

- void*指针作为左值用于“接收”任意类型的指针
- void*指针作为右值赋值给其它指针时需要强制类型转换

```c
int *p1 = NULL;
char *p2 = (char *)malloc(sizoeof(char)*20); 
```
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

### 数组指针作为函数参数

将数组作为函数参数的本质就是将指向数组首元素的指针作为函数参数。数组指针作为函数参数时，由于无法获取数组的长度，所以根据需求传入数组的长度。

### 指针函数

```
int * add(int x, int y);
```

### 函数指针

函数的入口地址就是函数指针

```
int (*p)(int, int);
int add(int x, int y);
p = add;
(*p)(3,5);
```

### 函数指针作为函数参数

```
void func(int (*p)(int, int), int b, int n);
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

### 内存申请

- malloc() 未初始化
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

```
const int *p; // 常量指针，指针所指向变量的值不能被改变
int * const p; // 指针常量，一个常量，即指针存放的地址不能被改变
const int * const; // 指向常量的常指针，指针指向的地址不能被改变，且所指向地址中的值也不能被改变
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