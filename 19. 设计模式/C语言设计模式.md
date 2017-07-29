## C语言设计模式

关于软件设计方面的书很多，比如《重构》，比如《设计模式》。至于软件开发方式，那就更多了，什么极限编程、精益方法、敏捷方法。随着时间的推移，很多的方法又会被重新提出来。

其实，就我个人看来，不管什么方法都离不开人。一个人写不出二叉树，你怎么让他写？敏捷吗？你写一行，我写一行。还是迭代？写三行，删掉两行，再写三行。项目的成功是偶然的，但是项目的失败却有很多原因，管理混乱、需求混乱、设计低劣、代码质量差、测试不到位等等。就软件企业而言，没有比优秀的文化和出色的企业人才更重要的了。

从软件设计层面来说，一般来说主要包括三个方面：

（1）软件的设计受众，是小孩子、老人、女性，还是专业人士等等；
（2）软件的基本设计原则，以人为本、模块分离、层次清晰、简约至上、适用为先、抽象基本业务等等；
（3）软件编写模式，比如装饰模式、责任链、单件模式等等。

从某种意义上说，设计思想构成了软件的主题。软件原则是我们在开发中的必须遵循的准绳。软件编写模式是开发过程中的重要经验总结。灵活运用设计模式，一方面利于我们编写高质量的代码，另一方面也方便我们对代码进行维护。毕竟对于广大的软件开发者来说，软件的维护时间要比软件编写的时间要多得多。编写过程中，难免要有新的需求，要和别的模块打交道，要对已有的代码进行复用，那么这时候设计模式就派上了用场。我们讨论的主题其实就是设计模式。

讲到设计模式，人们首先想到的语言就是c#或者是java，最不济也是c++，一般来说没有人会考虑到c语言。其实，我认为设计模式就是一种基本思想，过度美化或者神化其实没有必要。其实阅读过linux kernel的朋友都知道，linux虽然自身支持很多的文件系统，但是linux自身很好地把这些系统的基本操作都抽象出来了，成为了基本的虚拟文件系统。

举个例子来说，现在让你写一个音乐播放器，但是要支持的文件格式很多，什么ogg，wav，mp3啊，统统要支持。这时候，你会怎么编写呢？如果用C++语言，你可能会这么写。

```c
class music_file  
{  
    HANDLE hFile;  
  
public:  
    void music_file() {}  
    virtual ~music_file() {}  
    virtual void read_file() {}  
    virtual void play() {}  
    virtual void stop() {}  
    virtual void back() {}  
    virtual void front() {}  
    virtual void up() {}  
    virtual void down() {}      
};  
```
其实，你想想看，如果用C语言能够完成相同的抽象操作，那不是效果一样的吗？
```c
typedef struct _music_file  
{  
    HANDLE hFile;  
    void (*read_file)(struct _music_file* pMusicFile);  
    void (*play)(struct _music_file* pMusicFile);  
    void (*stop)(struct _music_file* pMusicFile);  
    void (*back)(struct _music_file* pMusicFile);  
    void (*front)(struct _music_file* pMusicFile);  
    void (*down)(struct _music_file* pMusicFile);  
    void (*up)(struct _music_file* pMusicFile);             
}music_file;  
```