### goto调戏apple

```c
#include <stdio.h>
#include <stdlib.h>
#include <Windows.h>

void openApple()
{
    ShellExecuteA(0, "open", "\"C:\\Program Files (x86)\\iTunes\\iTunes.exe\"", 0, 0, 1);//启动iTunes
}

void moveApple()
{
    //创建一个窗口编号变量,寻找iTunes的窗体
    HWND win = FindWindowA("iTunes", "iTunes");//类名,标题
    if (NULL == win)
    {
        printf("apple iTunes正在玩儿失踪! \n");
    }

    int i = 0;
A: if (i < 1600)//等价于循环结构趋于终止的条件
    {
        SetWindowPos(win, NULL, i, i * 768 / 1366, 700, 400, 0);//设置窗体坐标以及尺寸
        Sleep(10);//便于我们观察,不要太快
        i++;
        goto A;//跳转到A
    }

   SetWindowPos(win, NULL, 0, 0, 700, 400, 0);//恢复原始位置
B:Sleep(100);//休眠100毫秒
   ShowWindow(win, SW_HIDE);//隐藏
   Sleep(100);
   ShowWindow(win, SW_SHOW);//显示
   goto B;
}

void closeApple()
{
    system("taskkill /f /im iTunes.exe");
}

int main01(void)
{
    closeApple();
    Sleep(2000);

    openApple();
    Sleep(5000);

    moveApple();

    system("pause");
}
```