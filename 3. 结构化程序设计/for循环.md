### for循环

```c
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>

//01.for循环的死循环结构:
//  空语句:for(;;);
//  含语句:for(;-1;);中间只要!0就行
int main01()
{
    for (;;)//中间的语句起到判断的作用,为空为真,不为空的情况下非0即真,0则假
    {
        MessageBoxA(0, "china", "hello", 0);
    }

    for (;;);
    for (; -1;);

    system("pause");
}

//02.还是通过映射对数判断在循环的执行次数
//  先判断,在执行
//03.某种执行结构的末尾空语句和块儿语句特点:
//  所有结构后面都可以出现空语句和块儿语句
//  无论是顺序->分支-->循环结构都是一样的
//  只要是空语句也就当做一条语句进行处理
//  只要是块儿语句也当做一条语句进行处理
//  所有的空语句;都可以通过块儿语句进行替换
//  所有的块儿语句都可以通过空语句进行替换
//  前提条件是一一对应(一个块儿语句对应于一条空语句)
int main02(void)
{
    //for的作用范围,要么是最近的分号,要么是块儿语句
    for (int i = 0; i < 5; i++)//i作为初始化条件,i < 5作为循环判断条件,i++作为趋于循环终止的语句
    {
        system("calc");
    }

    system("pause");
}

//04.局部变量作用域分析:
//  循环变量的作用域类似于函数形参这个局部变量一样
//  只在其后面的首个块儿语句当中使用才会有效果
int main02(void)
{
    int res = 0;

    for (int i = 1; i <= 100; i++)
    {//循环变量不容易被外部给修改
        res += i;
    }
    printf("res = %d \n", res);

    int i = 1;
    while (i <= 100)
    {
        res += i;
    }
    //i = 190;

    system("pause");
}

//05.fox循环特殊点分析:
//  for循环的循环初始化条件,循环判断条件,循环趋向于结束的条件
//  都只会支持一条语句,但是却可以通过逗号运算符或者其他运算符支持多个逻辑执行
//  一个逗号表达式最终只会当做一条语句进行处理
int main03(void)
{
    for (int i = 0; i < 100; i += 3) {}

    for (int i = 0, j = 0; i < 100 && j < 100; i += 3, j += 4) {}//for循环通过,逗号运算符支持符合语句

    system("pause");
}

//06.通过for循环实现逻辑: 
//  1*2 + 3*4 + 5*6 + ... + 99*100
//  (2n-1)*(2n)-->n从1到50-->由一个数据生成两个数据
int main04(void)
{
    int res = 0;

    for (int i = 1; i <= 50; i++)
    {
        res += i*(i + 1);//C语言当中的乘号不能像数学中的一样进行省略
    }
    printf("res = %d \n", res);

    system("pause");
}

int main05(void)
{
    int res = 0;

    for (int i = 2; i <= 100; i += 2)
    {
        res = (i - 1)*i;//推理公式
    }
    printf("res = %d \n", res);

    system("pause");
}

int main06(void)
{
    //让窗口从右上角网左下角移动
    HWND win = FindWindowA("gdkwindowToplevel", "Cocos");
    if (win == NULL)
    {
        printf("Cocos玩儿失踪! \n");
    }
    else
    {
        for (int i = 0; i < 1000; i++)
        {
            SetWindowPos(win, NULL, 1366 - i, i * 768 / 1366, 400, 400, 1);
        }
    }

    system("pause");
}
```