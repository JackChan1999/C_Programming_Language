x86的通用寄存器有eax、ebx、ecx、edx、edi、esi。这些寄存器在大多数指令中是可以任意选用的，比如movl指令可以把一个立即数传送到eax中，也可传送到ebx中。但也有一些指令规定只能用其中某些寄存器做某种用途，例如除法指令idivl要求被除数在eax寄存器中，edx寄存器必须是0，而除数可以在任意寄存器中，计算结果的商数保存在eax寄存器中，而原来的被除数被覆盖掉，余数保存在edx寄存器中。也就是说，通用寄存器对于某些指令而言不是通用的。

x86的特殊寄存器有ebp、esp、eip、eflags。eip是程序计数器，eflags保存着计算过程中产生的标志位，包括进位、溢出、零、负数四个标志位，在x86的文档中这几个标志位分别称为CF、OF、ZF、SF。ebp和esp用于维护函数调用的栈帧。

下面我们通过一个求一组数的最大值的汇编程序来体会：

```
#max.asm  
.section .data  
data_items:             #数据项，即数组元素  
.long3,67,34,222,45,75,54,34,44,33,22,11,66,0  
   
.section .text  
.globl _start  
_start:  
         movl$0, %edi                     # 把当前位置0存入%edi寄存器  
         movldata_items(,%edi,4), %eax     #从数据的第一个整数开始处理  
         movl%eax, %ebx             # 因为是数据的第一个数字，所以当前%eax的值是最大的  
   
start_loop:               # 循环开始  
         cmpl$0, %eax        # 检查是否已经把所有的数字遍历了  
         jeloop_exit  
         incl%edi                    # 处理下一个数字  
         movldata_items(,%edi,4), %eax  
         cmpl%ebx, %eax    # 比较数值  
         jlestart_loop # 如果新的数字不是最大的值，就跳转回循环的开始  
         movl%eax, %ebx    #如果是最大值，那么就把这个数字取代原来的最大值 move the value as the largest  
         jmpstart_loop        # 继续循环  
   
loop_exit:  
         movl$1, %eax  
         int$0x80  
```

汇编、链接、执行：

可以看到最大数为222。

这个程序在一组数中找到一个最大的数，并把它作为程序的退出状态。这组数在.data段给出：

```
data_items:  
.long3,67,34,222,45,75,54,34,44,33,22,11,66,0  
```

.long指示声明一组数，每个数占32位，相当于C语言中的数组。这个数组开头有一个标号data_items，汇编器会把数组的首地址作为data_items符号所代表的地址，data_items类似于c语言中的数组名。data_items这个标号没有用.globl声明，因为它只在这个汇编程序内部使用，链接器不需要知道这个名字的存在。除了.long之外，常用的数据声明还有：

.byte，也是声明一组数，每个数占8位

.ascii，例如.ascii "Helloworld"，声明了11个数，取值为相应字符的ASCII码。注意，和C语言不同，这样声明的字符串末尾是没有'\0'字符的，如果需要以'\0'结尾可以声明为.ascii "Hello world\0"。

data_items数组的最后一个数是0，我们在一个循环中依次比较每个数，碰到0的时候让循环终止。在这个循环中：
edi寄存器保存数组中的当前位置，每次比较完一个数就把edi的值加1，指向数组中的下一个数。

ebx寄存器保存到目前为止找到的最大值，如果发现有更大的数就更新ebx的值。

eax寄存器保存当前要比较的数，每次更新edi之后，就把下一个数读到eax中。

```
_start:  
movl $0, %edi  
```

初始化edi，指向数组的第0个元素。

```
movl data_items(,%edi,4), %eax  
```

这条指令把数组的第0个元素传送到eax寄存器中。data_items是数组的首地址，edi的值是数组的下标，4表示数组的每个元素占4字节，那么数组中第edi个元素的地址应该是data_items + edi * 4，从这个地址读数据，写成指令就是上面那样，这种地址的表示方式在后面还会详细解释。

```
movl%eax, %ebx  
```

ebx的初始值也是数组的第0个元素。下面我们进入一个循环，在循环的开头用标号start_loop表示，循环的末尾之后用标号loop_exit表示。

```
start_loop:  
cmpl $0, %eax  
je loop_exit  
```

比较eax的值是不是0，如果是0就说明到达数组末尾了，就要跳出循环。cmpl指令将两个操作数相减，但计算结果并不保存，只是根据计算结果改变eflags寄存器中的标志位。如果两个操作数相等，则计算结果为0，eflags中的ZF位置1。je是一个条件跳转指令，它检查eflags中的ZF位，ZF位为1则发生跳转，ZF位为0则不跳转，继续执行下一条指令。可见条件跳转指令和比较指令是配合使用的，前者改变标志位，后者根据标志位做判断，如果参与比较的两数相等则跳转，je的e就表示equal。

```
incl%edi  
movldata_items(,%edi,4), %eax  
```

将edi的值加1，把数组中的下一个数传送到eax寄存器中。

```
cmpl%ebx, %eax  
jlestart_loop  
```

把当前数组元素eax和目前为止找到的最大值ebx做比较，如果前者小于等于后者，则最大值没有变，跳转到循环开头比较下一个数，否则继续执行下一条指令。jle也是一个条件跳转指令，le表示less than or equal。

```
movl%eax, %ebx  
jmpstart_loop  
```

更新了最大值ebx然后跳转到循环开头比较下一个数。jmp是一个无条件跳转指令，什么条件也不判断，直接跳转。loop_exit标号后面的指令用_exit系统调用退出程序。

通过这个例子我们了解到，访问内存时在指令中可以用多种方式表示内存地址，比如可以用数组基地址、元素长度和下标三个量来表示，增加了寻址的灵活性。下面介绍x86常用的几种寻址方式（Addressing Mode）。内存寻址在指令中可以表示成如下的通用格式：

```
ADDRESS_OR_OFFSET(%BASE_OR_OFFSET,%INDEX,MULTIPLIER)
```

它所表示的地址可以这样计算出来： 

```
FINAL ADDRESS = ADDRESS_OR_OFFSET +BASE_OR_OFFSET + MULTIPLIER * INDEX  
```

其中ADDRESS_OR_OFFSET和MULTIPLIER必须是常数，BASE_OR_OFFSET和INDEX必须是寄存器。在有些寻址方式中会省略这4项中的某些项，相当于这些项是0。

## 常见的寻址方式：
1、直接寻址（Direct Addressing Mode）。只使用ADDRESS_OR_OFFSET寻址，例如movl ADDRESS, %eax把ADDRESS地址处的32位数传送到eax寄存器。

2、变址寻址（Indexed Addressing Mode） 。上一节的movldata_items(,%edi,4), %eax就属于这种寻址方式，用于访问数组元素比较方便。

3、间接寻址（Indirect Addressing Mode）。只使用BASE_OR_OFFSET寻址，例如movl(%eax), %ebx，把eax寄存器的值看作地址，把这个地址处的32位数传送到ebx寄存器。注意和movl %eax, %ebx区分开。

4、基址寻址（Base Pointer Addressing Mode）。只使用ADDRESS_OR_OFFSET和BASE_OR_OFFSET寻址，例如movl4(%eax), %ebx，用于访问结构体成员比较方便，例如一个结构体的基地址保存在eax寄存器中，其中一个成员在结构体内的偏移量是4字节，要把这个成员读上来就可以用这条指令。

5、立即数寻址（Immediate Mode）。就是指令中有一个操作数是立即数，例如movl $12, %eax中的$12，这其实跟寻址没什么关系，但也算作一种寻址方式。

6、寄存器寻址（Register Addressing Mode）。就是指令中有一个操作数是寄存器，例如movl$12, %eax中的%eax，这跟内存寻址没什么关系，但也算作一种寻址方式。在汇编程序中寄存器用助记符来表示，在机器指令中则要用几个Bit表示寄存器的编号，这几个Bit也可以看作寄存器的地址，但是和内存地址不在一个地址空间。