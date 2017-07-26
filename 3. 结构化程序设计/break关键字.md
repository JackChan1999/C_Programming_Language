### break关键字

```c
///break.c
#include <stdio.h>
#include <stdlib.h>

//01.不安全函数和安全函数:
//  不安全函数会在安全函数的名称后面添加一个"_s"以作为安全函数的标识
//02.case语句特点:
//  1.swtich当中如果没有使用break关键字,那么switch会贯穿执行
//  2.case语句块儿当中虽然没有使用块儿语句,但是仍然可以编写多条语句
int main01(void)
{
    int num;

    scanf_s("%d", &num);//安全的scanf_();函数使用
    switch (num)
    {
    case 1://case就像一个开关一样,有一个符合条件,就由开关处从上往下进行执行case后面的语句块儿,一直执行到break关键字为止
        //如果没有break,就全部进行执行
        system("tasklist");//进程列表
        //printf("test \n");//case语句块儿后的语句块儿可以写多条语句
        break;
    case 2:
        system("calc");//计算器
        break;
    case 3:
        system("mspaint");//画图板
        break;
    case 4:
        system("记事本");
        break;
    }

    system("pause");
}

//03.break关键字使用总结:
//  1.使用环境:
//      只能用于switch多分支结构和循环结构
//  2.使用特点:
//      用于switch多分支结构:
//          表明终止switch多分支结构(包含break关键字的最内部switch结构)
//      用于循环结构
//          表明终止循环结构(包含break关键字的最内部循环结构)
int main02(void)
{
    for (int i = 0; ; i++)
    {//for循环第二个语句为空,照样表示的是死循环,除非第二个语句为0,否则都是死循环
        if (900 == i)
        {
            break;//退出循环,break一旦执行,表明退出循环结构执行体,也就是说循环结构执行体后面的语句都将得不到执行
        }
        printf("%d \n", i);//打印数据
    }

    system("pause");
}
```

```c
///break习题.c
#include <stdio.h>
#include <stdlib.h>

//01.求解二元一次方程的两个解
//  二元一次方程
//      316=13*x+11*y
//      316=13*i+11*j
//  穷举法-->枚举法-->暴力破解法
//      (316-13*i) % 11 == 0;//没有余数-->说明整除成功,得到实际的解
int main03(void)
{
    for (int i = 0;; i++)
    {
        printf("%d \n", i);
        if ((316 - 13 * i) % 11 == 0)//判定能否完成整除
        {//没有余数,说明整除成功-->有结果
            printf("316 = %d*13 + %d*11 \n", i, (316 - 13 * i) / 11);//整除成功之后的结果就是相应的解
            break;
        }
    }

    system("pause");
}
```

```c
///google.c
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>//ShellExecuteA

//打开Google浏览器
void openGoogle()
{
    ShellExecuteA(0, "open", "C:\\Users\\ZHEN\\AppData\\Local\\Google\\Chrome\\Application\\chrome.exe", 0, 0, 1);//默认窗口打开
}

//控制Google浏览器
void moveGoogle()
{
    //寻找一个窗口,获取以后,将窗口编号赋值给变量win
    HWND win = FindWindowA("Chrome_WidgetWin_1", "打开新的标签页 - Google Chrome");
    if (win == NULL)
    {
        printf("谷歌玩儿失踪! \n");
        return;
    }

    //0,10,20,30,40,50
    //0,1,2,3,4,5
    for (int i = 0;; i = i + 10)
    {
        if (i > 1500)
        {
            break;
        }
        SetWindowPos(win, NULL, i, 0, 200, 200, 0);//设置窗口位置,大小
        if (i / 10 % 2 == 1)
        {
            ShowWindow(win, SW_HIDE);//隐藏
        }
        else
        {
            ShowWindow(win, SW_SHOW);//显示
        }
        Sleep(30);
    }

    int x = 1600;
    while (1)
    {
        if (x == 0)
        {
            break;//跳出循环
        }
        SetWindowPos(win, NULL, x, 0, 200, 200, 0);//设置窗口坐标,尺寸
        Sleep(50);
        x = x - 10;
    }
    SetWindowPos(win, NULL, 0, 0, 100, 100, 0);//初始位置
    //do-while实现放大和缩小

    int y = 100;//y长度从100~768,宽度从100*16/9~1366
    do
    {
        if (y == 768)
        {
            break;//跳出死循环
        }
        SetWindowPos(win, NULL, 0, 0, y * 16 / 9, y, 0);//放大,对应于屏幕的分辨率
        Sleep(50);
        y += 10;
    } while (1);

    y = 768;
    do
    {
        if (y == 100)
        {
            break;//跳出死循环
        }
        SetWindowPos(win, NULL, 0, 0, y * 16 / 9, y, 0);//通过设置长度,宽度实现缩小
        Sleep(50);
        y -= 10;
    } while (1);

    system("pause");
}

//关闭Google浏览器
void closeGoogle()
{
    system("taskkill /f /im chrome.exe");
}

int main04(void)
{
    closeGoogle();//关闭以前的Google浏览器
    Sleep(2000);
    openGoogle();//打开最新的Google浏览器
    Sleep(5000);
    moveGoogle();

    system("pause");
}
```