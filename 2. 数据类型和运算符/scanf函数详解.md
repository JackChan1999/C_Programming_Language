### scanf()函数详解

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>

//01.scanf();函数扫描输入事项:
//  格式必须一一匹配:非格式控制符的可见字符必须一一匹配输入
int main01(void)
{
    int num = 0;

    printf("%p \n", &num);
    scanf("num=%d", &num);//根据地址对变量进行赋值初始化
    printf("num=%d \n", num);

    system("pause");
    return 1;
}

//02.星号(*)用于scanf();和printf();当中的不同特点:
//  用于scanf();当中可以跳过该格式控制符所对应的数据挖掘
//  用于printf();当中可以控制总输出宽度以及以及保留小数位数(精度)
int main02(void)
{
    int a, b = 0, c;

    scanf("a=%d,b=%*d,c=%d", &a, &c);
    a = a + 1;
    //b = b + 10;
    printf("a=%d,b=%d,c=%d readall", a, b, c);

    system("pause");
    return 1;
}

//03.关于scanf("%d%d%d");形式的整数录入特点分析
//  (1).不可见字符间隔:空格,Tab键,回车
//  (2).解析特点:
//      1).scanf();字符串缓冲区-->格式字符串映射进去
//      2).格式控制符进行数据挖掘
//          进行数据类型匹配挖掘
//      3).不可见字符用于发出挖掘指令
int main03(void)
{
    int a, b, c;

    scanf("%d%d%d", &a, &b, &c);
    printf("a = %d, b = %d, c = %d readall \n", a, b, c);

    system("pause");
    return 1;
}

int main04(void)
{
    int num;
    char str[100] = { 0 };

    scanf("%d %s", &num, str);//中间的不可见字符:可以采用空格+Tab键+回车发出挖掘指令(从scanf();的控制字符串缓冲区当中挖掘)
    printf("num = %d, str = %s", num, str);

    system("pause");
    return 1;
}
```

### scanf()函数拓展

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>

#define 海华中意的身高 172
#define 海华中意的年龄 30

//01.数据挖掘含义:
//  1.数据扫描函数从指定字符串当中挖掘有用信息到指针列表当中所指向变量位置
//  2.然后对该指针所指向的变量数据进行各种操作
//02.数据扫描函数使用注意事项:
//  格式控制符所对应的数据左右
//      无论有多少个空格
//      无论有多少个Tab键
//      无论有多少个回车
//  都只当做一个空格进行数据扫描处理
int main01(void)
{
    char str[500] = "131610 陈小姐 女    40 165 高中  未婚  朝阳  双子座 在校学生    普通话 北京  面议元/天   有过几次    学生伴游    清纯漂亮 自信可爱的我独在北京上学.在这浪漫的季节偶尔来到这里寻找我的有缘人.愿我们的相聚能留下人生中最美好的回忆!  10:00—23:00     2264938779@qq.com       226493870779";
    int tall;
    int age;

    sscanf(str, "%*d %*s %*s %d %d", &age, &tall);//数据挖掘
    //三元运算符-->三元表达式-->当中只有一个分号,那就是结尾的那个用于语句结束的分号";"
    (age >= 海华中意的年龄) && (tall <= 海华中意的身高) ? printf("海华买单 \n") : printf("换下一个 \n");

    system("pause");
    return 1;
}

//01.如果一个字符串的前后含有逗号","都会被字符串格式控制符识别为字符串组成元素:
//  (1).空格形式的不可见字符不会被识别为组成元素
//  (2).数字形式的课件字符不会被识别为组成元素
int main02(void)
{
    //%s，
    char str[500] = "420111197203024016,13600477059,nixiaodong@vip.sina.com";
    //字符串不会将逗号当做一个结束

    long long selfid;
    long long mobile;
    char email[100] = { 0 };
    //,|%|#不能作为字符串结尾符,需要进行数据扫描挖掘的提前替换

    sscanf(str, "%lld,%lld,%s", &selfid, &mobile, email);//从字符串当中进行数据挖掘操作
    printf("%lld,%lld,%s \n", selfid, mobile, email);

    system("pause");
    return 1;
}

int main03(void)
{
    char str[500] = "刘海华,420111197203024016,13600477059,niniaodong@vip.sina.com";

    //将障碍字符串数据扫描的可见字符串组成元素进行替换,以待后期的数据扫描挖掘
    for (int i = 0; i < 500; i++)
    {
        if (str[i] == ',')
        {
            str[i] = ' ';
        }
    }

    char name[100] = { 0 };
    long long selfid;
    long long mobile;
    char email[100] = { 0 };
    sscanf(str, "%s %lld %lld %s", name, &selfid, &mobile, email);
    printf("%s %lld %lld %s \n", name, selfid, mobile, email);

    system("pause");
    return 1;
}

int main04(void)
{
    char str[150] = "530981645----13707700754aini";
    long long QQ;
    char pass[100] = { 0 };

    sscanf(str, "%lld----%s", &QQ, pass);
    printf("QQ = %lld, pass = %s \n", QQ, pass);

    system("pause");
    return 1;
}
```

### scanf()函数加强

```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>

//01.连续整数的截断技巧:
//  1.%d控制整数数据扫描挖掘
//  2.中间插入数字(%Nd)表示按照多少个数字组成进行阶段挖掘
int main01(void)
{
    int a, b, c, d, e;

    scanf("%3d%3d%3d%3d%3d", &a, &b, &c, &d, &e);//3指定整数的挖掘长度
    printf("a = %d, b= %d, c = %d, d = %d, e = %d \n", a, b, c, d, e);

    system("pause");
    return 1;
}

//02.scanf();函数使用细节说明:
//  1.星号(*)表示跳过该格式控制符所对应的数据扫描
//  2.整数从某个字符串数据开始连续扫描多少个数据
int main02(void)
{
    char str1[100] = { 0 };
    char str2[100] = { 0 };

    scanf("%*3s%5s", str1, str2);//固定长度截取,*忽略
    printf("%s, %s \n", str1, str2);

    system("pause");
    return 1;
}

//03.实数的扫描不可以指定精度,打印可以指定精度
//  扫描的精度指定无效
int main03(void)
{
    float fl = 0;

    scanf("%7f", &fl);//实数不可以指定精度
    printf("fl = %lf \n", fl);

    system("pause");
    return 1;
}

//04.数据扫描函数是用于将数据扫描的指针所指向的变量当中
int main04(void)
{
    int num = 0;
    scanf("%d", &num);//不写地址符,会将整数的值当做地址进行处理

    system("pause");
    return 1;
}

//05.数据扫描函数要求格式一一对应才行
int main05(void)
{
    int num = 0;

    scanf("#num=%d", &num);//精确匹配,不可以遗漏
    printf("%d", num);

    system("pause");
    return 1;
}

//06.即使是不可见字符也会被当做字符的有效输入:
//  (1).空格-->Tab键-->回车
//  (2).有效输入就能有效输出
int main06(void)
{
    char ch1, ch2, ch3;
    scanf("%c%c%c", &ch1, &ch2, &ch3);
    printf("[%c][%c][%c] \n", ch1, ch2, ch3);//回车会被当做一个有效输入

    system("pause");
    return 1;
}

//07.不同格式控制符所对应的数据扫描结束点不同:
//  字符串的输入格式控制符以:
//      必须以不可见字符空格,Tab键,回车进行结束
//  数字的输入格式控制符:
//      除了数字以外的任何字符
int main07(void)
{
    int num1;
    int num2;
    int num3;
    char str[100];

    scanf("%s%d%d%d", str, &num1, &num2, &num3);
    printf("%s, %d, %d, %d \n", str, num1, num2, num3);//字符串必须以空格或者回车结束

    system("pause");
    return 1;
}

int main08(void)
{
    int num1;
    int num2;
    int num3;

    //1.在scanf();函数当中,%%代表一个实际的%
    //2.注意%d%%必须得挨着才行,否则会数据扫描失败
    //  1).数据扫描和数据匹配是两码事儿
    //  2).%%不可以挖掘到整数,所以必须连在一起
    scanf("%d%%%d%d", &num1, &num2, &num3);
    printf("%d,%d,%d \n", num1, num2, num3);

    system("pause");
    return 1;
}

//08.scanf();数据扫描函数使用技巧:
//  在进行使用格式控制符进行字符串数据扫描的时候
//  如果在扫描字符串格式控制符的末尾添加一个%n
//  就意味着可以统计挖掘到的字符串当中的字符个数
int main09(void)
{
    int num1;
    int num2;
    int num3;
    char str[100];
    int n;

    scanf("%s%n%d%d%d", str, &n, &num1, &num2, &num3);//统计字符数量
    printf("%s,%d,%d,%d \n", str, num1, num2, num3);//字符串必须空格或者回车以及Tab键以作结尾
    printf("n = %d \n", n);

    system("pause");
    return 1;
}

//09.数据扫描函数与正则表达式的结合常常用于对字符串
//  的处理
int main10(void)
{
    char str[100];

    //scanf("%s", str);
    //scanf("%[^\n]", str);//只有\n才会被当做为数据扫描挖掘的结尾
    //scanf("[A-Z]%n", str);//只有\n当做结束
    //printf("%s \n", str);

    system("pause");
    return 1;
}
```