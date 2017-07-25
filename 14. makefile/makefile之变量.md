我们详细看看Makefile中关于变量的语法规则。先看一个简单的例子：
```
foo = $(bar)  
bar = Huh?  
   
all:  
    @echo$(foo)  
```

我们执行make将会打出Huh?。当make读到foo = \$(bar)时，确定foo的值是\$(bar)，但并不立即展开\$(bar)，然后读到bar = Huh?，确定bar的值是Huh?，然后在执行规则all:的命令列表时才需要展开\$(foo)，得到\$(bar)，再展开\$(bar)，得到Huh?。因此，虽然bar的定义写在foo之后，\$(foo)展开还是能够取到$(bar)的值。

这种特性有好处也有坏处。好处是我们可以把变量的值推迟到后面定义，例如：

```
main.o: main.c  
    $(CC)$(CFLAGS) $(CPPFLAGS) -c $<  
   
CC = gcc  
CFLAGS = -O -g  
CPPFLAGS = -Iinclude  
```

编译命令可以展开成gcc -O -g -Iinclude -cmain.c。通常把CFLAGS定义成一些编译选项，例如-O、-g等，而把CPPFLAGS定义成一些预处理选项，例如-D、-I等。用=号定义变量的延迟展开特性也有坏处，就是有可能写出无穷递归的定义，例如CFLAGS = $(CFLAGS) -O，或者：
```
A = $(B)  
B = $(A)  
```

当然，make有能力检测出这样的错误而不会陷入死循环。有时候我们希望make在遇到变量定义时立即展开，可以用:=运算符，例如：
```
x := foo  
y := $(x) bar  
   
all:  
    @echo"-$(y)-"  
```

当make读到y :=\$(x) bar定义时，立即把$(x)展开，使变量y的取值是foo bar，如果把这两行颠倒过来：
```
y := $(x) bar  
x := foo  
```
那么当make读到y :=\$(x) bar时，x还没有定义，展开为空值，所以y的取值是 bar，注意bar前面有个空格。一个变量的定义从=后面的第一个非空白字符开始（从\$(x)的$开始），包括后面的所有字符，直到注释或换行之前结束。如果要定义一个变量的值是一个空格，可以这样：
```
nullstring :=  
space := $(nullstring) # end ofthe line  
```

nullstring的值为空，space的值是一个空格，后面写个注释是为了增加可读性，如果不写注释就换行，则很难看出$(nullstring)后面有个空格。

还有一个比较有用的赋值运算符是?=，例如foo ?= \$(bar)的意思是：如果foo没有定义过，那么?=相当于=，定义foo的值是$(bar)，但不立即展开；如果先前已经定义了foo，则什么也不做，不会给foo重新赋值。

+=运算符可以给变量追加值，例如：

```
objects = main.o  
objects += $(foo)  
foo = foo.o bar.o  
```

object是用=定义的，+=仍然保持=的特性，objects的值是main.o$(foo)（注意$(foo)前面自动添一个空格），但不立即展开，等到后面需要展开$(objects)时会展开成main.o foo.o bar.o

再比如：

```
objects := main.o  
objects += $(foo)  
foo = foo.o bar.o  
```

object是用:=定义的，+=保持:=的特性，objects的值是main.o$(foo)，立即展开得到main.o （这时foo还没定义），注意main.o后面的空格仍保留。

如果变量还没有定义过就直接用+=赋值，那么+=相当于=

上一节我们用到了特殊变量$@和$<，这两个变量的特点是不需要给它们赋值，在不同的上下文中它们自动取不同的值。常用的特殊变量有：

- $@，表示规则中的目标
- $<，表示规则中的第一个条件
- $?，表示规则中所有比目标新的条件，组成一个列表，以空格分隔
- $^，表示规则中的所有条件，组成一个列表，以空格分隔

例如前面写过的这条规则：
```
main: main.o stack.o maze.o  
    gccmain.o stack.o maze.o -o main  
```

可以改写成：
```
main: main.o stack.o maze.o  
    gcc$^ -o $@  
```

这样即使以后又往条件里添加了新的目标文件，编译命令也不需要修改，减少了出错的可能。

$?变量也很有用，有时候希望只对更新过的条件进行操作，例如有一个库文件libsome.a依赖于几个目标文件：

```bash
libsome.a: foo.o bar.o lose.owin.o  
    arr libsome.a $?  
    ranliblibsome.a  
```

这样，只有更新过的目标文件才需要重新打包到libsome.a中，没更新过的目标文件原本已经在libsome.a中了，不必重新打包。

在上一节我们看到make的隐含规则数据库中用到了很多变量，有些变量没有定义（例如CFLAGS），有些变量定义了缺省值（例如CC），我们写Makefile时可以重新定义这些变量的值，也可以在缺省值的基础上追加。以下列举一些常用的变量，请读者体会其中的规律。

| 变量            | 含义                                       |
| ------------- | ---------------------------------------- |
| AR            | 静态库打包命令的名字，缺省值是ar                        |
| ARFLAGS       | 静态库打包命令的选项，缺省值是rv                        |
| AS            | 汇编器的名字，缺省值是as                            |
| ASFLAGS       | 汇编器的选项，没有定义                              |
| CC            | C编译器的名字，缺省值是cc                           |
| CFLAGS        | C编译器的选项，没有定义                             |
| CXX           | C++编译器的名字，缺省值是g++                        |
| CXXFLAGS      | C++编译器的选项，没有定义                           |
| CPP           | C预处理器的名字，缺省值是$(CC) -E                    |
| CPPFLAGS      | C预处理器的选项，没有定义                            |
| LD            | 链接器的名字，缺省值是ld                            |
| LDFLAGS       | 链接器的选项，没有定义                              |
| TARGET_ARCH   | 和目标平台相关的命令行选项，没有定义                       |
| OUTPUT_OPTION | 输出的命令行选项，缺省值是-o $@                       |
| LINK.o        | 把.o文件链接在一起的命令行，缺省值是\$(CC) \$(LDFLAGS) \$(TARGET_ARCH) |
| LINK.c        | 把.c文件链接在一起的命令行，缺省值是\$(CC) \$(CFLAGS) \$(CPPFLAGS) \$(LDFLAGS) $(TARGET_ARCH) |
| LINK.cc       | 把.cc文件（C++源文件）链接在一起的命令行，缺省值是\$(CXX) \$(CXXFLAGS) \$(CPPFLAGS) \$(LDFLAGS) $(TARGET_ARCH) |
| COMPILE.c     | 编译.c文件的命令行，缺省值是\$(CC) \$(CFLAGS) \$(CPPFLAGS) $(TARGET_ARCH) -c |
| COMPILE.cc    | 编译.cc文件的命令行，缺省值是\$(CXX) \$(CXXFLAGS) \$(CPPFLAGS) $(TARGET_ARCH) -c |
| RM            | 删除命令的名字，缺省值是rm -f                        |

常用的make命令行选项：

-n选项只打印要执行的命令，而不会真的执行命令，这个选项有助于我们检查Makefile写得是否正确，由于Makefile不是顺序执行的，用这个选项可以先看看命令的执行顺序，确认无误了再真正执行命令。

-C选项可以切换到另一个目录执行那个目录下的Makefile，比如先退到上一级目录再执行我们的Makefile（假设我们的源代码都放在testmake目录下）：

```bash
$ cd ..  
$ make -C testmake  
make: Entering directory`/home/djkings/testmake'  
cc    -c -o main.o main.c  
cc    -c -o stack.o stack.c  
cc    -c -o maze.o maze.c  
gcc main.o stack.o maze.o -o main  
make: Leaving directory`/home/djkings/testmake'  
```

一些规模较大的项目会把不同的模块或子系统的源代码放在不同的子目录中，然后在每个子目录下都写一个该目录的Makefile，然后在一个总的Makefile中用make -C命令执行每个子目录下的Makefile。例如Linux内核源代码根目录下有Makefile，子目录fs、net等也有各自的Makefile，二级子目录fs/ramfs、net/ipv4等也有各自的Makefile。

在make命令行也可以用=或:=定义变量，如果这次编译我想加调试选项-g，但我不想每次编译都加-g选项，可以在命令行定义CFLAGS变量，而不必修改Makefile编译完了再改回来：

```bash
$ make CFLAGS=-g  
cc -g   -c -o main.o main.c  
cc -g   -c -o stack.o stack.c  
cc -g   -c -o maze.o maze.c  
gcc main.o stack.o maze.o -o main  
```

如果在Makefile中也定义了CFLAGS变量，则命令行的值覆盖Makefile中的值。