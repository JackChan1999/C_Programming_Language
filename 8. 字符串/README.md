---
typora-copy-images-to: images
---

### 转义字符及其作用

![1499575197489](images/1499575197489.png)

![1499574320749](images/1499574320749.png)

### 字符数组

```c
char c[] = {'h','e','l','l','o'};
```

### 字符串

```c
char char_aray[] = {"hello"};
char char_aray[6] = {'h','e','l','l','o','\0'};
```

### 获取字符串长度

```c
sizeof("abc");
strlen("abc");
```

### 字符串与指针

```c
char* chr = "hello";
```

### 字符数组与字符指针

```c
char str[6] = "hello";
char* p = "hello";
```

![1499529425079](images/1499529425079.png)

### 字符串的输入输出

- gets() 读入用户输入的字符串
- puts()
- printf()
- scanf()
- sprintf()
- atoi()
- itoa()

### 常用printf()格式字符

![1499575313349](images/1499575313349.png)

### 常用scanf()输入字符串的终止符

![1499575414445](images/1499575414445.png)

### 字符串作函数参数