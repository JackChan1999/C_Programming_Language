Makefile有很多灵活的写法，可以写得更简洁，同时减少出错的可能。本节我们来看看这样一个例子还有哪些改进的余地。

 一个目标依赖的所有条件不一定非得写在一条规则中，也可以拆开写，例如：
```
main.o: main.h stack.h maze.h  
   
main.o: main.c  
         gcc-c main.c  
```
就相当于：
```
main.o: main.c main.h stack.h maze.h  
         gcc-c main.c  
```

如果一个目标拆开写多条规则，其中只有一条规则允许有命令列表，其它规则应该没有命令列表，否则make会报警告并且采用最后一条规则的命令列表。

这样我们的例子可以改写成：
```
main: main.o stack.o maze.o  
         gccmain.o stack.o maze.o -o main  
   
main.o: main.h stack.h maze.h  
stack.o: stack.h main.h  
maze.o: maze.h main.h  
   
main.o: main.c  
         gcc-c main.c  
   
stack.o: stack.c  
         gcc-c stack.c  
   
maze.o: maze.c  
         gcc-c maze.c  
   
clean:  
         -rmmain *.o  
   
.PHONY: clean  
```
这不是比原来更繁琐了吗？现在可以把提出来的三条规则删去，写成：
```
main: main.o stack.o maze.o  
         gccmain.o stack.o maze.o -o main  
   
main.o: main.h stack.h maze.h  
stack.o: stack.h main.h  
maze.o: maze.h main.h  
   
clean:  
         -rmmain *.o  
   
.PHONY: clean  
```
这就比原来简单多了。可是现在main.o、stack.o和maze.o这三个目标连编译命令都没有了，怎么编译的呢？试试看：

```
$ make  
cc   -c -o main.o main.c  
cc   -c -o stack.o stack.c  
cc   -c -o maze.o maze.c  
gcc main.o stack.o maze.o -o main  
```
现在解释一下前三条编译命令是怎么来。如果一个目标在Makefile中的所有规则都没有命令列表，make会尝试在内建的隐含规则（Implicit Rule）数据库中查找适用的规则。make的隐含规则数据库可以用make -p命令打印，打印出来的格式也是Makefile的格式，包括很多变量和规则，其中和我们这个例子有关的隐含规则有：

```
# default  
OUTPUT_OPTION = -o $@  
   
# default  
CC = cc  
   
# default  
COMPILE.c = $(CC) $(CFLAGS) $(CPPFLAGS)$(TARGET_ARCH) -c  
   
%.o: %.c  
# commands to execute (built-in):  
       $(COMPILE.c) $(OUTPUT_OPTION) $<  
```

\#号在Makefile中表示单行注释，就像C语言的//注释一样。CC是一个Makefile变量，用CC = cc定义和赋值，用$(CC)取它的值，其值应该是cc。Makefile变量像C的宏定义一样，代表一串字符，在取值的地方展开。cc是一个符号链接，通常指向gcc，在有些UNIX系统上可能指向另外一种C编译器。

CFLAGS这个变量没有定义，`$(CFLAGS)`展开是空，CPPFLAGS和TARGET_ARCH也是如此。这样`$(COMPILE.c)`展开应该是cc 空 空 空 -c，去掉“空”得到cc -c，注意中间留下4个空格，所以%.o:%.c规则的命令`$(COMPILE.c) $(OUTPUT_OPTION) $<`展开之后是`cc -c -o $@$<`，和上面的编译命令已经很接近了。

`$@`和`$<`是两个特殊的变量，`$@`的取值为规则中的目标，`$<`的取值为规则中的第一个条件。%.o: %.c是一种特殊的规则，称为模式规则（Pattern Rule）。现在回顾一下整个过程，在我们的Makefile中以main.o为目标的规则都没有命令列表，所以make会查找隐含规则，发现隐含规则中有这样一条模式规则适用，main.o符合%.o的模式，现在%就代表main（称为main.o这个名字的Stem），再替换到%.c中就是main.c。所以这条模式规则相当于：

```
main.o: main.c  
         cc    -c -o main.o main.c  
```
随后，在处理stack.o目标时又用到这条模式规则，这时又相当于：
```
stack.o: stack.c  
         cc    -c -o stack.o stack.c  
```
maze.o也同样处理。这三条规则可以由make的隐含规则推导出来，所以不必写在Makefile中。

先前我们写Makefile都是以目标为中心，一个目标依赖于若干条件，现在换个角度，以条件为中心，Makefile还可以这么写：
```
main: main.o stack.o maze.o  
         gccmain.o stack.o maze.o -o main  
   
main.o stack.o maze.o: main.h  
main.o maze.o: maze.h  
main.o stack.o: stack.h  
   
clean:  
         -rmmain *.o  
   
.PHONY: clean  
```
我们知道，写规则的目的是让make建立依赖关系图，不管怎么写，只要把所有的依赖关系都描述清楚了就行。对于多目标的规则，make会拆成几条单目标的规则来处理，例如
```
target1 target2: prerequisite1prerequisite2  
         command$< -o $@  
```
这样一条规则相当于：
```
target1: prerequisite1 prerequisite2  
         commandprerequisite1 -o target1  
   
target2: prerequisite1 prerequisite2  
         commandprerequisite1 -o target2  
```
注意两条规则的命令列表是一样的，但`$@`的取值不同。