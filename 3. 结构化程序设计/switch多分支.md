### switch多分支

```c
#define _CRT_SECURE_NO_WARNINGS

#include <stdio.h>
#include <stdlib.h>

//01.if-else if-else内容总结:
//  1.用于多分支判断情况:
//      多个入口,单个出口
//  2.用于区间范围判断
//      判断情况模糊
//  3.作为区间判断的时候需要
//      注意区间范围必须有序
//  4.没有任何返回值
int main01(void)
{                 
    int num = 9;
    //if-else if-else-->很容易混淆,不匹配情况
    //switch-->不容易混淆,绝对匹配情况
    if (num == 1)
    {
        if (1 > 0) {}else {}
    }
    else if (num == 2)
    {
        if (2 > 0) {}else {}
    }
    else if (num == 3)
    {
    }
    else if (num == 4)
    {
    }
    else if (num == 5)
    {
    }
    else if (num == 6)
    {
    }
    else
    {
        //处理上述之外的情况
    }

    system("pause");
    return 1;
}

//02.switch内容总结:
//  1.用于多分支判断情况:
//      多个入口,单个出口
//  2.用于等值判断
//      待判断变量必须和常量
//      等值才能匹配成功
//  3.特殊情况分析:
//      (1).每种情形完成之后需要添加break语句
//          如果没有break语句,会发生穿透现象
//      (2).穿透现象可以用于离散区间模拟
//          类似于区间形式的判断(前提有序)
//      (3).无论default语句放在哪个位置
//          它都是最后才进行判断的,只有排除了
//          所有情况之后才会到default语句
//      (4).switch语句只有一个出口
//      (5).只要没有break关键字,但是存在default
//          语句,那么最后执行的一定是default语句
//          如果最后执行的default语句后面没有break
//          而且该default语句执行之后会发生穿透现象
//          直到遇到break才会终止switch语句
//  4.switch语句只支持整数类型的等值判断
//      不支持其它类型的等值判断
//  5.case关键字后面的常量匹配值不能是表达式
//      只能是整型常量或者本质为整型常量的字符型数据
int main02(void)
{
    int num = 1;
    scanf("%d", &num);

    char fl = 'A';
    enum week { X, Y, Z };
    enum week week1 = X;

    int data = 2;
    switch (num)
    {//要处理的变量 error C2050:switch 表达式不是整型
    //case 10://不可以出现相同的case后置常量
    case 10:
        printf("一个士兵! \n");
        break;
    case 1:
        printf("一个班12个人! \n");
        break;
    case 2:
        printf("一个排36个人! \n");
    case 3:
        printf("一个连108个人! \n");
        break;
    case 4:
        printf("一个营324个人! \n");
        break;
    case 5:
        printf("一个团972个人! \n");
        break;
    default:
        printf("我只能处理到团级编制! \n");
        break;
    }

    system("pause");
    return 1;
}
```