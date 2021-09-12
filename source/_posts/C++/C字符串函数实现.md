---
title: C字符串函数实现
top: false
mathjax: true
categories:
- C++
---

-----









-----



### strcpy函数

1. **原型**：`char * strcpy(char * destin, const char * source)`
2. **作用**：把source指向的字符串拷贝到destin指向的字符串中
3. **代码**：

```c
char * my_strcpy(char * destin, const char * source)
{
    assert(destin != NULL && source != NULL); 
    char *tmp = destin;

    do {
        *destin++ = *source++
    }while(*destin && *source);

    return tmp;
}

int main()
{
    char destination[100] = {1};
    const char *source = "abcdefgh";

    char *destion = my_strcpy(destination, source);
    printf("%s\n", destion);

    return 0;
}
```



### strncpy函数

1. **原型**：`char * strncpy(char * str1, char * str2, int count)`
2. **作用**：把str2指向的前count个字符拷贝到str1指向的字符串中
3. **代码**：

```c
#include <stdio.h>
#include <string.h>
#include <assert.h>

char * my_strncpy(char * str1, char * str2, int count)
{
    assert(str1 != NULL);
    while(count && (*str1++ = *str2++))
    {
        count--;  //长度随着复制的进行而减少
    }
    while(count--)
    {
        *str1++ = '\0';  //如果str2的长度小于count那就用NULL填充str1的剩余
    }
    return str1;
}

int main()
{
    char destination[10] = {0};
    char *source = "abcdefgh";
    int count = 5;

    my_strncpy(destination, source, count);
    printf("%s\n", destination);
    return 0;
}
```

### strcmp函数

1. **原型**：`int strcmp(const char * str1, const char * str2)`
2. **作用**：比较str1和str2，str1 > str2返回1，str1 == str2返回0
3. **代码**：

```c
int my_strcmp(const char * str1, const char * str2)
{
    /***比较str1和str2，str1 > str2返回大于0的数，str1 == str2返回0，str1 < str2返回小于0的数***/
    while( ! ((*str1 != *str2) && str1 != '\0'))
    {
        //如果str1和str2的字符相等且字符str1没有到末尾则进入循环
        str1++;  //指针str1向后加一位
        str2++;  //指针str2向后加一位
    }
    return (*str1 - *str2);  //返回跳出循环的str1和str2当前的字符的差值比较的结果(0或1或-1)
}

int main()
{
    char *buf1 = "aaaa",
         *buf2 = "aaaab",
         *buf3 = "aaaac";
    int ptr = 0;  //定义变量用来存放my_strcmp返回的值

    ptr = my_strcmp(buf2, buf1);
    if (ptr > 0)
        printf("string 2 is bigger than string 1\n");
    else
        printf("string 2 is smaller than string 1\n");

    ptr = my_strcmp(buf2, buf3);
    if (ptr > 0)
        printf("string 2 is bigger than string 3\n");
    else
        printf("string 2 is smaller than string 3\n");

    return 0;
}
```

### strncmp函数

1. **原型**：`int strncmp(const char * str1, const char * str2, int count)`
2. **作用**：比较str1和str2的前n个字符
3. **代码**：

```c
int my_strncmp(const char * str1, const char * str2, int count)
{
    /***比较str1和str2的前n个字符***/
    if(!count)
        return 0;
    while(--count && *str1 && *str1 == *str2)
    {
        str1++;
        str2++;
    }
    return (*str1 - *str2);
}

int main()
{
    char *str1 = "China is a nation!";
    char *str2 = "French is a nation!";
    int count = 5,
        ptr = 0;

    ptr = my_strncmp(str1, str2, count);
    if(ptr != 0)
        printf("str1 is not equal to str2!\n");
    return 0;
}
```

### stricmp函数

1. **原型**：`int my_stricmp(const char *str1, const char *str2)`
2. **作用**：不区分大小写的比较str1和str2
3. **代码**：

```c
int my_stricmp(const char *str1, const char *str2)
{
    /***不区分大小写的比较str1和str2***/
    char ch1 = '0',
         ch2 = '0';

    do
    {
        if((ch1 = (unsigned char)(*(str1++))) >= 'A' && ch1 <= 'Z')
            ch1 += 0x20;
        if((ch2 = (unsigned char)(*(str2++))) >= 'A' && ch2 <= 'Z')
            ch2 += 0x20;
    }while(ch1 && (ch1 == ch2));  // 判断是否相等且str1不为'\0'，是则进入循环
    return (ch1 - ch2);  //返回两个数的大小
}

int main()
{
    char *str1= "ammana";
    char *str2 = "bibi";
    char *str3 = "AMMANA";
    int ptr = 0,
        ptr1 = 0;

    ptr = my_stricmp(str1, str2);
    if(ptr > 0)
        printf("str1 bigger than str2!\n");
    else
        printf("str1 smaller than str2!\n");

    ptr1 = my_stricmp(str1, str3);
    if(ptr1 > 0)
        printf("str1 bigger than str3!\n");
    else if(ptr1 < 0)
        printf("str1 smaller than str3!\n");
    else
        printf("str1 equal to str3!\n");

    return 0;
}
```

### strlen函数

1. **原型**：`unsigned int strlen(const char * str)`
2. **作用**：计算str的长度并返回
3. **代码**：

```c
unsigned int my_strlen(const char * str)
{
    /***计算str的长度并返回***/
    unsigned length = 0;  //定义无符号变量length统计str的长度
    while(*str != '\0')  //判断str指向的字符是否为'\0'
    {
        length++;  //长度加1
        str++;  //指向的地址向后加1位
    }
    return length;
}

int main()
{
    char * str = "abcdefgh";
    int len = 0;

    len = my_strlen(str);

    printf("%d\n", len);
    return 0;
}
```

### strcat函数

1. **原型**：`char * strcat(char* destin, const char *source)`
2. **作用**：连接两个字符串，将source连接到destin
3. **代码**：

```c
char * my_strcat(char* destin, const char *source)
{
    /***连接两个字符串，将source连接到destin***/
    char * temp = destin;  //定义temp指针变量存放destin的地址

    while(*temp)  //判断temp字符串是否读到了'\0'
    {
        temp++;  //将temp指针读到字符串末尾
    }

    while(*temp++ = *source++)  //把source字符串从temp末尾开始复制给temp
        {};

    return temp;  //返回链接好的字符串的指针
}

int main()
{
    /***对函数Strcat的调用***/
    char destination[25];  //定义一个大小为25的数组
    char *space = " ",
         *String = "C++!",
         *Hello = "Hello";

    strcpy(destination, Hello);  //进行拷贝
    my_strcat(destination, space);
    my_strcat(destination, String);

    printf("%s\n", destination);  //打印最终结果
    return 0;
}
```

### strchr函数

1. **原型**：`char * strchr(char * str, const char c)`
2. **作用**：查找str中c首次出现的位置，并返回位置或者NULL
3. **代码**：

```c
char * my_strchr(char * str, const char c)
{
    /***查找str中c首次出现的位置，并返回位置或者NULL***/
    while(*str != '\0' && *str != c)  //判断str是否为'\0'且等于c
    {
        str++;
    }
    //判断如果str等于c则返回与c匹配的str否则返回NULL
    return (*str == c ? str : NULL);
}

int main()
{
    char * string = "abcdefgh";
    char c = 'd',
         *ptr = NULL,  //存放my_strchr的返回值

    ptr = my_strchr(string, c);
    if(ptr)
        printf("%c\n", c);
    else
        printf("Not Found!\n");

    return 0;
}
```

### strrchr函数

1. **原型**：`char * strrchr(char * str, const char c)`
2. **作用**：查找str中c最后一次出现的位置，并返回位置或者NULL
3. **代码**：

```c
char * my_strrchr(char * str, const char c)
{
    /***查找str中c最后一次出现的位置，并返回位置或者NULL***/
    char * end = str + strlen(str);  //定义指针end存放str的末尾地址

    while(*str != *end && *end != c)  //判断end没有到头部且没找到和c匹配的字符
    {
        end--;  //向前进行移动
    }

    if(*end == *str && *end != c)  //如果没找到则返回NULL
        return NULL;

    return end;
}

int main()
{
    char * string = "abcdefgh";
    char c = 'd',
         *ptr = NULL,  //存放my_strchr的返回值

    ptr = my_strrchr(string, c);
    if(ptr)
        printf("%c\n", c);
    else
        printf("Not Found!\n");

    return 0;
}
```

### strrev函数

1. **原型**：`char * strrev(char * str)`
2. **作用**：翻转字符串并返回字符串指针
3. **代码**：

```c
char * my_strrev(char * str)
{
    /***翻转字符串并返回字符串指针***/
    assert(str != NULL);
    char *end = str;
    char *head = str;
    char temp = '0';

    while(*end++)
    {};

    end--;  /* 与end++抵消 */
    end--;  /* 回跳过结束符'\0' */

    /* 当head和end未重合时，交换它们所指向的字符 */
    while(head < end)
    {
        temp = *head;
        *head++ = *end;  /* head向尾部移动 */
        *end-- = temp;  /* end向头部移动 */
    }
    return str;
}

#if 0
/*第二种实现方式*/
char * my_strrev(char * str)
{
    /***翻转字符串并返回字符串指针***/
    assert(str != NULL);
    char *end = strlen(str) + str -1;
    char temp = '0';

    while(str < end)
    {
        temp = *str;
        *str = *end;
        *end = temp;
        str++;
        end--;
    }
    return str;
}
#endif


int main()
{
    /***只能逆置字符数组，而不能逆置字符串指针指向的字符串，
        因为字符串指针指向的是字符串常量，常量不能被修改***/
    char str[] = "Hello World";  //定义str数组

    printf("Before reversal: %s\n", str);
    my_strrev(str);
    printf("After reversal:  %s\n", str);
    return 0;
}
```

### strdup函数

1. **原型**：`char * strdup(const char * str)`
2. **作用**：拷贝字符串到新申请的内存中返回内存指针，否则返回NULL
3. **代码**：

```c
#include <assert.h>

char * my_strdup(const char * str)
{
    /***拷贝字符串到新申请的内存中返回内存指针，否则返回NULL***/
    char * temp = (char*)malloc(strlen(str) + 1);  //给temp申请内存
    assert(str != NULL && temp != NULL);  //进行检查是否为NULL
    strcpy(temp, str);  //进行拷贝
    return temp;  //返回的内存在堆中需要手动释放内存
}

int main()
{
    char *str = NULL,
         *string = "abcde";

    str = my_strdup(string);
    printf("%s\n", str);
    free(str);  //释放内存

    return 0;
}
```

### strstr函数

1. **原型**：`char * strstr(const char * str1, char * str2)`
2. **作用**：查找str2在str1中出现的位置，找到返回位置，否则返回NULL
3. **代码**：

```c
char * my_strstr(const char * str1, char * str2)
{
    /***查找str2在str1中出现的位置，找到返回位置，否则返回NULL***/
    assert(str1 != NULL & str2 != NULL);  //检查str1和str2
    int len1 = strlen(str1);  //定len1来获取str1的长度
    int len2 = strlen(str2);  //定len2来获取str2的长度

    while(len1 >= len2)  //必须str1的长度大于str2的长度
    {
        len1--;  //str1的长度每一次减去一个
        if(!strncmp(str1, str2, len2))  //进行比较str2和str1的前len2个字符
        {
            return str2;  //如果匹配返回str2
        }
        str1++;  //str1每一次都要向后走一步
    }
    return NULL;
}

int main()
{
    char *str1 = "China is a nation!",
         *str2 = "nation",
         *ptr = NULL;

    ptr = my_strstr(str1, str2);
    printf("The string is: %s\n", ptr);
    return 0;
}
```

### strpbrk函数

1. **原型**：`char *strpbrk(const char *str1, const char *str2)`
2. **作用**：从str1的第一个字符向后检索，直到’\0’，如果当前字符存在于str2中，那么返回当前字符的地址，并停止检索.
3. **代码**：

```c
#include <assert.h>

char *my_strpbrk(const char *str1, const char *str2)
{
    /***strpbrk()从str1的第一个字符向后检索，直到'\0'，如果当前字符存在于str2中，
        那么返回当前字符的地址，并停止检索***/
    assert(str1 != NULL && str2 != NULL);
    const char *str3 = str2;

    while(*str1)  //判断石头人字符串str1是否结束
    {
        for(str3 = str2; *str3; ++str3)  //str2进行循环与str1的自费比较
        {
            if(*str1 == *str3)  //如果相等则进行str1的下一个判断
                break;
        }
        if(*str3)  //如果str3结束则结束比较
            break;
        str1++;
    }

    if(*str3 == '\0')  //如果str1到末尾则返回NULL
        str1 = NULL;
    return (char*)str1;
}

int main()
{
    char s1[] = "http://see.xidian.edu.cn/cpp/u/xitong/";
    char s2[] = "see";
    char *p = my_strpbrk(s1, s2);
    if(p)
    {
        printf("The result is: %s\n",p);
    }
    else
    {
        printf("Sorry!\n");
    }
    return 0;
}
```

### strspn函数

1. **原型**：`int my_strspn(const char *str1, const char *str2)`
2. **作用**：从参数str1字符串的开头计算连续的字符,而这些字符都完全是str2所指字符串中的字符。
3. **代码**：

```c
#include <assert.h>

/***strspn()函数检索区分大小写***/
int my_strspn(const char *str1, const char *str2)
{
    /***从参数str1字符串的开头计算连续的字符,而这些字符都完全是str2所指字符串中的字符。
    简单的说,若strspn()返回的数值为n,则代表字符串str1开头连续有n个字符都是属于字符串str2内的字符***/
    assert(str1 != NULL && str2 != NULL);
    const char *tmp = str1;  //临时变量存储str1的地址
    const char *str3;

    while(*tmp)
    {
        for(str3 = str2; *str3; ++str3)
        {
            if(*str3 == *tmp)  //判断str1是否存在str2的字符
                break;
        }
        if(*str3 == '\0')  //如果str2结束则跳出循环str1++
            break;
        tmp++;
    }

    return tmp - str1;  //返回差值，即str1共有几个连续的字符是str2中存在的
}

int main()
{
    char s1[] = "hello world!";
    char s2[] = "i am lihua";

    int p = my_strspn(s1, s2);

    printf("The result is:%d\n", p);
    return 0;
}
```

### strtok函数

1. **原型**：`char *my_strtok(char *sou, char *delim)`
2. **作用**：将字符串分割成一个个片段
3. **代码**：

```c
/***因strtok函数内部使用了静态指针，因此它不是线程安全的***/
static char *olds;  //定义全局变量来进行定位
char *my_strtok(char *sou, char *delim)
{
    /***strtok函数用来将字符串分割成一个个片段，
    在参数sou的字符串中发现到参数delim的分割字符时则会将该字符改为\0 字符***/
    //参数sou指向欲分割的字符串，参数delim为分割字符串
    //在第一次调用时，strtok()必需给予参数sou字符串，往后的调用则将参数sou设置成NULL
    //每次调用成功则返回下一个分割后的字符串指针

    char *token = NULL;
    if(sou == NULL)  //如果sou为空则将上一次的位置给sou
    {
        sou = olds;
    }

    /*将指针移到第一个非delim的位置*/
    sou += strspn(sou, delim);
    if(*sou == '\0')  //如果是结束符，则将结束符保存并退出函数
    {
        olds = sou;
        return NULL;
    }

    /*获取delim的字符在字符串sou中第一次出现的位置*/
    token = sou;
    sou = strpbrk(token, delim);

    if(sou == NULL)
    {
        olds = __rawmemchr (token, '\0');  //参考http://dev.wikl.net/89401.html
    }
    else
    {
        *sou = '\0';  //将分隔符的位置用'\0'替换
        olds = sou + 1;  //将olds指向下一次需要操作的位置
    }
    return token;
}

int main()
{
    //strtok处理的是函数的局部状态信息，所以不能同时解析两个字符串
    char sou[100] = " Micael_SX is so good";
    char *delim = " ";
    char *token;
    token = my_strtok(sou, delim);
    while(token != NULL)
    {
        printf("%s\n", token);
        token = my_strtok(NULL, delim);
    }
    return 0;
}
```

### strsep函数

1. **原型**：`char *my_strsep(char **stringp, const char *delim)`
2. **作用**：将字符串分割成一个个片段
3. **代码**：

```c
/***********************************************************
a：如果*stringp为NULL，则该函数不进行任何操作，直接返回NULL；
b：strsep每次都是从*stringp指向的位置开始搜索，搜索到任一分割字符之后，将其置为’\0’，
   并使*stringp指向它的下一个字符。如果找不到任何分割字符，则将*stringp置为NULL。
c：strsep内部没有使用静态指针，因而strsep是线程安全的。
d：strsep返回的子串有可能是空字符串，实际上，就是因为strtok无法返回空子串，才引入的strsep函数。
   不过strtok符合C89/C99标准，因而移植性更好。但strsep却不是。
e: "二八定律",即80%情况下分隔符只有一个字节，使用strchr完成,20%情况有多个字节,调用strpbrk完成
*************************************************************/

char *my_strsep(char **stringp, const char *delim)
{
    /***strsep函数用来将字符串分割成一个个片段***/
    char *begin,
         *end;
    begin = *stringp;
    if(begin == NULL)
    {
        return NULL;
    }

    /*delim分隔符是单个字符的情况是非常频繁的，因此不需要使用代价昂贵的strpbrk函数
     而只需要调用strchr就能解决*/
    if(delim[0] == '\0' || delim[1] == '\0')
    {
        char ch = delim[0];
        if(ch == '\0')
        {
            end = NULL;
        }
        else
        {
            if(*begin == ch)
            {
                end = begin;
            }
            else if(*begin == '\0')
            {
                end = NULL;
            }
            else
            {
                end = strchr(begin + 1, ch);
            }
        }
    }
    else
    {
        /*delim有两个字符以上,才调用strpbrk*/
        end = strpbrk(begin, delim);
    }

    if(end)
    {
        /*用0封闭这个token；返回stringp，指向一个null指针*/
        *end++ = '\0';
        *stringp = end;
    }
    else
    {
        /*没有出现delim，这是最后一个token*/
        *stringp = NULL;
    }
    return begin;
}

int main()
{
    char source[] = "hello, world! welcome to China!";
    char delim[] = " ,!";

    char *s = strdup(source);
    char *token;
    for(token = my_strsep(&s, delim); token != NULL; token = my_strsep(&s, delim))
    {
        printf(token);
        printf("+");
    }
    printf("\n");
    return 0;
}
```

### strdel函数

这个是我面试的一个上机题目，不是库里边的，所以自己写出原型。

char *strDel(char* str,const char chToDel)，删除str中所有的chToDel字符。

**源码：**

```c
//基本思想是：通过pRefStr2遍历str，找出不等于chToDel的值，将其赋值与pRefStr2
char *strDel(char* str,char chToDel)
{
	
	assert((str!=NULL)&& (chToDel!=NULL));
   char *pRefStr1, *pRefStr2;
	pRefStr1=pRefStr2=str;//将两个指针变量同时指向str，后续改变指针变量的值相当于改变str的值

    while(*pRefStr2++){
		if(*pRefStr2!=chToDel) 
		{
			*pRefStr1++=*pRefStr2;//找到不等于chToDel的值，并将其赋值与pRefStr1，再将pRefStr1的指针向后移动；
		}
	 }
    *pRefStr1='\0';
    return str;
}
```

关键点： 注意C语言程序的顺序执行，以及指针。

```c
#include<iostream.h>
#include<assert.h>
void main()
{
	char destStr[10]="aadddfcca";
	char delStr='a';
	strDel(destStr,delStr);
	cout<<destStr<<endl;}
```





### 参考文章

**链接:**

1. http://blog.csdn.net/kangroger/article/details/24383571
2. http://zheng-ji.info/blog/2014/02/05/shen-ru-strtokhan-shu/
3. http://blog.csdn.net/gqtcgq/article/details/48399957

## 相关参考

[C语言str函数的源码](https://blog.csdn.net/xingerr/article/details/70257234)

[C语言字符串函数源码详解](https://blog.csdn.net/wjeson/article/details/9185585)