### while循环

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>

int main01(void)
{
    while (0)//0和非0
    {
        malloc(1024 * 1024 * 10);
    }

    system("pause");
    return 1;
}

int main02(void)
{
    int i = 0;
    while (i < 500)
    {
        system("notepad");
        i++;
    }

    system("pause");
    return 1;
}

int main03(void)
{
    int i = 0;
    int j = 0;

    //0 
    //1
    //2
    //3
    //4-->5次
    //while (i++ < 5)//++ <
    //{
    //  //i++;
    //  system("notepad");
    //}

    //i++ < 5 <==> ++j <6-->依然5次
    while (i++ < 5 && ++j < 6)
    {
        system("notepad");
    }

    system("pause");
    return 1;
}

//01.如何识别一条语句:
//  1.空语句(";")
//  2.只要以分号(";")结尾就算一条语句
//  3.语句块儿
//  4.任意一个结构:
//      顺序-->循环-->分支
int main04(void)
{
    while (1)
    {//while()之后不要添加空语句(;),如果是块儿语句,则整个块儿语句被当做一个语句,否则就是最近的一条语句(例如:空语句;)
        system("notepad");
    }

    system("pause");
    return 1;
}

//02.如何判断一个循环结构到底执行执行多少次?
//  判断多少对映射关系成立就行了
int main05(void)
{
    int i = 1;
    int res = 0;

    while (i <= 100)
    {//100组映射关系成立-->执行100次
        res += i;
        i++;
    }
    printf("%d \n", res);

    system("pause");
    return 1;
}

int main06(void)
{
    int n = 0;
    scanf("%d", &n);

    int i = 0;
    int res = 1;
    while (i < n)//循环条件
    {
        res *= 2;//循环执行体
        i++;
    }
    printf("%d \n", res);

    system("pause");
    return 1;
}

void shellOpenQQ()
{
    ShellExecuteA(0, "open", "D:\\ProgramFiles\\Tencent\\QQ\\Bin\\QQScLauncher.exe", 0, 0, 1);
}

int main01(void)
{
    shellOpenQQ();
    Sleep(3000);

    HWND win = FindWindowA("TXGuiFoundation", "QQ");
    if (win != NULL)//找到
    {
        //控制窗体显示和隐藏
        /*printf("窗体显示和隐藏! \n");
        while (1)
        {
            ShowWindow(win, SW_SHOW);
            Sleep(30);
            ShowWindow(win, SW_HIDE);
            Sleep(30);
        }*/

        //控制窗体从左向右移动
        printf("窗体从左往右移动! \n");
        //while (1)
        //{
        //  int i = 0;
        //  while (i < 1000)
        //  {
        //      SetWindowPos(win, NULL, i, 0, 500, 400, 1);
        //      //Sleep(30);
        //      i++;
        //      //i=0;i-->1000(从左往右进行移动)
        //      //500,400-->指定窗体的宽高
        //  }
        //}

        //控制窗体从上往下移动
        printf("窗体从上往下移动! \n");
        /*while (1)
        {
            int i = 0;
            while (i < 500)
            {
                SetWindowPos(win, NULL, 1000, i, 500, 400, 1);
                Sleep(30);
                i++;
            }
        }*/

        //控制窗体从右往左
        printf("控制窗体从右往左! \n");
        /*while (1)
        {
            int i = 1000;
            while (i > 0)
            {
                SetWindowPos(win, NULL, i, 500, 500, 400, 1);
                Sleep(30);
                i--;
            }
        }*/

        //控制窗体从下往上
        printf("控制窗体从下往上! \n");
        int i = 500;
        while (i > 0)
        {
            SetWindowPos(win, NULL, 0, i, 500, 400, 1);
            Sleep(30);
            i--;
        }
    }

    system("pause");
    return 1;
}
```

### do-while循环

```c
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>

int main01(void)
{
    do
    {
        system("notepad");
    } while (-1);//do-while至少执行循环体一次,非0将会永远循环执行下去

    system("pause");
    return 1;
}

//01.通过映射对数判断执行次数的方式:
//  执行-->4
//  执行-->3
//  执行-->2
//  执行-->1
//  执行-->0-->不再执行
int main02(void)
{
    int i = 5;

    do
    {//相当于直接将5映射的哪一次给执行了5-->1:执行5次
        system("notepad");
        i--;
    } while (i);//5次

    system("pause");
    return 1;
}

//02.通过映射对数判断执行次数的方式:
//  执行-->5
//  执行-->4
//  执行-->3
//  执行-->2
//  执行-->1
//  执行-->0-->不再执行
//  注意:后面的整数是根据while();后面的整个表达式结果进行判断的
//03.循环结构转换特点:
//  1.while循环结构一定能够转换成为do-while循环结构;
//  2.do-while循环结构却不一定能够转换成为while循环结构
//  注意:while<--->do-while循环结构之间的转换规律
int main03(void)
{
    int i = 5;

    do
    {
        printf("notepad \n");
    } while (i--);//6次-->整个表达式的映射多加了一个0映射

    system("pause");
    return 1;
}

int main04(void)
{
    do
    {
        printf("notepad \n");
        malloc(1024 * 1024 * 500);
    } while (0);

    system("pause");
    return 1;
}

int main05(void)
{
    int i = 5;
    int j = 4;

    //执行规律特点分析:
    //  0.确定逻辑与的前后判断关系
    //  1.针对j的规律分析:
    //      执行-->4
    //      执行-->3
    //      执行-->2
    //      执行-->1
    //      执行-->0-->映射对数5-->执行5次
    //  2.针对i的规律分析:
    //      执行-->4
    //      执行-->3
    //      执行-->2
    //      执行-->1
    //      执行-->0-->映射对数5-->执行5次
    do
    {
        printf("notepad \n");
        printf("i = %d, j = %d \n", i, j);
    } while (j-- && --i);//与,有一个为0就终止循环结构

    system("pause");
    return 1;
}

int main06(void)
{
    char ch;

    do
    {
        ch = getchar();
        if (ch >= 'A' && ch <= 'Z')
        {
            ch = ch + 32;//大写转小写
        }
        getchar();//缓冲回车
        putchar(ch);
        putchar('\n');
    } while (ch != '\t');//等于tab退出do-while循环结构

    system("pause");
    return 1;
}

//03.Window操作API与do-while函数的结合使用
int main07(void)
{
    //异步启动一个记事本
    system("start notepad");
    Sleep(2000);
    //获取窗体编号
    HWND win = FindWindowA("Notepad", "无标题 - 记事本");
    if (win != NULL)
    {
        system("echo find notepad! \n");
        int i = 0;
        do
        {
            //设置窗体信息:
            //win00000000-->窗体句柄号
            //i---------------->窗体x坐标
            //i * 768 /1366--->窗体y坐标
            //400------------->窗体宽度
            //400------------->窗体高度
            SetWindowPos(win, NULL, i, i * 768 / 1366, 400, 400, 1);//设置坐标,尺寸
            i++;
        } while (i < 1000);
    }

    //窗体描述信息
    //  窗体左上角的坐标表示(IpRect.left, IpRect.top);
    //  窗体右下角的坐标表示(IpRect.right, IpRect.bottom);
    RECT IpRect;
    GetWindowRect(win, &IpRect);
    printf("%d, %d, %d, %d \n", IpRect.top, IpRect.bottom, IpRect.left, IpRect.right);

    if (win != NULL)
    {
        printf("find! \n");
        //SWP_SHOWWINDOW-->表示显示窗口
        SetWindowPos(win, NULL, 0, 0, 300, 300, SWP_SHOWWINDOW);//设置坐标尺寸
        //SetWindowPos();
        int i = 400;
        Sleep(1000);
        do
        {
            SetWindowPos(win, NULL, 0, 0, i, i, SWP_SHOWWINDOW);
            //MoveWindow();
            printf("%d \n", i);
            Sleep(100);
            i += 20;
        } while (i <= 700);
    }
    else
    {
        printf("not find! \n");
    }

    system("pause");
    return 1;
}
```