### if单分支

```c
#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>
#include <stdlib.h>

int main01(void)
{
    if (-2 + 2)//只分辨0与非0的情况
    {
        system("msconfig");
    }

}

int main02(void)
{
    if (0)
        system("msconfig");
    //if不加括号的情况下,默认的控制范围为,其后的一条语句
    //一条语句:一个分号或者一个代码块儿
    system("notepad");

    system("pause");
}

int main03(void)
{
    int a, b, c;

    scanf("%d%d%d", &a, &b, &c);
    if (a < b)//如果a<b,则互换数据实体,保证a是a和b中的数据实体较大变量
    {
        //int temp = a;
        //a = b;
        //b = temp;
        a = a ^ b;
        b = a ^ b;
        a = a ^ b;
    }

    if (a < c)
    {
        a = a ^ b;
        b = a ^ b;
        a = a ^ b;
    }

    /*if (b < c)
    {
        b = b ^ c;
        c = b ^ c;
        b = b ^ c;
    }*/
    //简写形式:
    if (b < c) b = b ^c, c = b ^ c, b = b ^c;

    //限定顺序:a>b>c
    printf("a = %d, b = %d, c = %d \n", a, b, c);

    system("pause");
}
```
### if双分支

```c
#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>
#include <stdlib.h>

int main01(void)
{
    if (-1)//这里只需要判定最终结果是0或者非0就行了
    {
        system("calc");
    }
    else
    {
        system("notepad");
    }

    system("pause");
}

int main02(void)
{
    //单条语句,{}块儿语句标识符可以省略
    //最近的分号";"作为语句的结束
    if (1 - 1)
        system("calc");
    else
        system("notepad");

    system("pause");
    return 1;
}

int main03(void)
{
    int a, b;
    int abs, bbs;

    scanf("%d,%d", &a, &b);
    if (a > 0)
    {
        abs = a;
    }
    else
    {
        //abs = -a;
        abs = a * (-1);
    }

    /*if (b > 0)
    {
        bbs = b;
    }
    else
    {
        bbs = b * (-1);
    }*/
    bbs = ((b > 0) ? b : (b * (-1)));
    printf("%d \n", abs > bbs ? abs : bbs);

    system("pause");
    return 1;
}

#define 编程能力 80
#define 周瑞富的编程能力 81

int main04(void)
{
    if (周瑞富的编程能力 > 编程能力)
    {
        printf("欢迎加入流氓集团! \n");
    }
    else
    {
        printf("回家等电话! \n");
    }

    system("pause");
    return 1;
}
```
### if多分支

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>

int main01(void)
{
    int num;

    scanf("%d", &num);
    if (0 == num)
    {
        MessageBoxA(0, "", "", 0);
        system("shutdown -r -t 5");//重启命令
    }
    else if (1 == num)
    {
        MessageBoxA(0, "", "", 0);
        while (1)
        {
            system("你不爱上刘振全世界就会毁灭 >> C:\\1.exe");//导出并追加的命令
        }
    }
    else
    {
        MessageBoxA(0, "", "", 0);
        while (1)
        {
            malloc(10 * 1024 * 1024);//内存分配函数
        }
    }

    system("pause");
}

#define 企业要求编程能力 80
#define 刘振全的编程能力 39

//01.if多分支语句的特点:
//  详情请看后解
int main02(void)
{
    if (刘振全的编程能力 > 80)
    {
        printf("高薪! \n");
    }
    else if (刘振全的编程能力 < 80 && 刘振全的编程能力 > 60)
    {
        printf("低薪! \n");
    }
    else
    {
        printf("回家等电话! \n");
    }

    system("pause");
}

//02.如何判断同时判断:
//  数字,小写字母,大写字母
int main03(void)
{
    while (1)
    {
        char ch = getchar();
        getchar();//吸收回车符
        if (ch >= '0' && ch <= '9')
        {
            printf("数字! \n");
        }
        else if (ch >= 'a' && ch <= 'z')
        {
            printf("小写字母1 \n");
        }
        else if (ch >= 'A' && ch <= 'Z')
        {
            printf("大写字母! \n");
        }
        else
        {
            printf("其它字符! \n");
        }
    }

    system("pause");
}
```