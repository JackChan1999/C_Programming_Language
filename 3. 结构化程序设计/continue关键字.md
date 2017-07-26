### continue关键字

```c
///continue.c
#include <stdio.h>
#include <stdlib.h>

//01.continue关键字使用总结:
//  1.使用场景:
//      conti`这里写代码片`nue关键字只能用于循环语句当中
//  2.使用特点:
//      用于结束包含continue关键字的最内层循环结构
//      的执行体当中的本次剩余语句
int main01(void)
{
    for (int i = 100; i < 201; i++)
    {
        if (i % 3 == 0)
        {//不会打印能够整除3的数字
            //break;//结束循环结构
            continue;//结束本次循环,执行下一次循环,在关键字continue之后的本次循环语句就不被执行了
        }
        printf("%d \n", i);
    }

    system("pause");
}
```

```c
///调戏迅雷.c
#define _CRT_SECURE_NO_WARNINGS//关闭安全检查
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>

//能够被3整除的秒,我们就进行隐藏,否则我们进行显示
//打开迅雷
void openThunder()
{
    ShellExecuteA(0, "open", "D:\\ProgramFiles\\ThunderNetwork\\Thunder\\Program\\Thunder.exe", 0, 0, 1);
}

void showThunder(int i)
{//i为非0的时候隐藏,i为0的时候显示
    //寻找描述迅雷的窗体
    HWND win = FindWindowA("XLUEFrameHostWnd", "迅雷");
    if (i)
    {
        ShowWindow(win, SW_HIDE);//隐藏
    }
    else
    {
        ShowWindow(win, SW_SHOW);//显示
    }
}

void closeThunder()
{
    system("taskkill /f /im Thunder.exe");
}

int main02(void)
{
    closeThunder();
    Sleep(2000);

    openThunder();
    Sleep(3000);

    HWND win = FindWindowA("XLUEFrameHostWnd", "迅雷7");

    //计数器的使用:
    //  计数器构建:
    //      int i = 0;
    //  计数器使用:
    //      1, 3, 5, 7, 9, 11:隐藏
    //      2, 4, 6, 8, 10, 0 :显示
    int i = 0;
    while (1)
    {
        char str[20];//字符数组,作为字符串的缓冲区模拟,最大长度为20
        sprintf(str, "title 第%3d秒 \n", i);
        //跟printf();函数一样,sprintf();函数也是用于打印字符串的:
        //  printf();函数用于将字符串打印到屏幕
        //  sprintf();函数用于将字符串打印到字符串
        system(str);//当前命令行中执行的命令,标题就是当前命令行窗口的标题
        Sleep(1000);

        if (i % 3 == 0)
        {
            ShowWindow(win, SW_HIDE);//隐藏
            continue;//提前结束本次循环的的余后循环执行体-->节省时间,提升效率
        }
        ShowWindow(win, SW_SHOW);//显示

        i++;//计数器自增-->事情完成之后才增加,没有完成就不用增加(完成为1,未完成不为1)
    }

    system("pause");
}
```