---
title: C++格式化输出
top: false
mathjax: true
categories:
- C++
---

-----

## printf 格式化输出

**格式化字符串定义**

> **格式化字符串**，是一些程序设计语言在格式化输出API函数中用于指定输出参数的格式与相对位置的字符串参数，例如C、C++等程序设计语言的printf类函数，其中的转换说明（conversion specification）用于把随后对应的0个或多个函数参数转换为相应的格式输出；格式化字符串中转换说明以外的其它字符原样输出。

### POSIX标准下的printf函数

#### 格式化占位符

格式化字符串中的占位符用于指明输出的参数值如何格式化。

格式化占位符（format placeholder），语法是：

> **%[parameter\]\[flags]\[field width]\[.precision][length]type**

**Parameter**可以忽略或者是：

| 字符 | 描述                                                         |
| :--: | :----------------------------------------------------------- |
|  n$  | **n是用这个格式说明符（specifier）显示第几个参数**；这使得参数可以输出多次，使用多个格式说明符，以不同的顺序输出。 如果任意一个占位符使用了parameter，则其他所有占位符必须也使用parameter。这是POSIX扩展，不属于ISO C。例： `printf("%2$d %2$#x; %1$d %1$#x",16,17) 产生"17 0x11; 16 0x10"` |

**Flags**可为0个或多个：

| 字符 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| +    | 总是表示有符号数值的'+'或'-'号，缺省情况是忽略正数的符号。仅适用于数值类型。 |
| 空格 | 使得有符号数的输出如果没有正负号或者输出0个字符，则前缀1个空格。如果空格与'+'同时出现，则空格说明符被忽略。 |
| -    | 左对齐。缺省情况是右对齐。                                   |
| #    | 对于'g'与'G'，不删除尾部0以表示精度。对于'f', 'F', 'e', 'E', 'g', 'G', 总是输出小数点。对于'o', 'x', 'X', 在非0数值前分别输出前缀0, 0x, and 0X表示数制。 |
| 0    | 如果width选项前缀以0，则在左侧用0填充直至达到宽度要求。例如printf("%2d", 3)输出" 3"，而printf("%02d", 3)输出"03"。如果0与-均出现，则0被忽略，即左对齐依然用空格填充。 |

**Field Width**给出显示数值的最小宽度，典型用于制表输出时填充固定宽度的表目。实际输出字符的个数不足域宽，则根据左对齐或右对齐进行填充。实际输出字符的个数超过域宽并不引起数值截断，而是显示全部。宽度值的前导0被解释为0填充标志，如上述；前导的负值被解释为其绝对值，负号解释为左对齐标志。如果域宽值为`*`，则由对应的函数参数的值为当前域宽。

**Precision**通常指明输出的*最大*长度，依赖于特定的格式化类型。对于d、i、u、x、o的整型数值，是指最小数字位数，不足的位要在左侧补0，如果超过也不截断，缺省值为1。对于a,A,e,E,f,F的浮点数值，是指小数点右边显示的数字位数，必要时四舍五入；缺省值为6。对于g,G的浮点数值，是指有效数字的最大位数。对于s的字符串类型，是指输出的字节的上限，超出限制的其它字符将被截断。如果域宽为`*`，则由对应的函数参数的值为当前域宽。如果仅给出了小数点，则域宽为0。

**Length**指出浮点型参数或整型参数的长度。此项Microsoft称为“Size”。可以忽略，或者是下述：

| 字符 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| hh   | 对于整数类型，printf期待一个从char提升的int尺寸的整型参数。  |
| h    | 对于整数类型，printf期待一个从short提升的int尺寸的整型参数。 |
| l    | 对于整数类型，printf期待一个long尺寸的整型参数。 对于浮点类型，printf期待一个double尺寸的整型参数。 对于字符串s类型，printf期待一个wchar_t指针参数。 对于字符c类型，printf期待一个wint_t型的参数。 |
| ll   | 对于整数类型，printf期待一个long long尺寸的整型参数。Microsoft也可以使用I64 |
| L    | 对于浮点类型，printf期待一个long double尺寸的整型参数。      |
| z    | 对于整数类型，printf期待一个size_t尺寸的整型参数。           |
| j    | 对于整数类型，printf期待一个intmax_t尺寸的整型参数。         |
| t    | 对于整数类型，printf期待一个ptrdiff_t尺寸的整型参数。        |

此外，在ISO C99广泛接受前，还有几个平台相关的length选项：

| 字符 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| I    | 对于有符号整数类型，printf期待一个ptrdiff_t尺寸的整型参数。对于无符号整数类型，printf期待一个size_t尺寸的整型参数。常见于Win32/Win64平台。 |
| I32  | 对于整数类型，printf期待一个32位（双字）的整型参数。常见于Win32/Win64平台。 |
| I64  | 对于整数类型，printf期待一个64位（四字）的整型参数。常见于Win32/Win64平台。 |
| q    | 对于整数类型，printf期待一个64位（四字）的整型参数。常见于BSD平台。 |

ISO C99的头文件`inttypes.h`包含了许多宏，用于平台独立的`printf`编码。例如：

#### 类型

**Type**，也称转换说明（conversion specification/specifier），可以是：

| 字符 | 描述                                                         |
| :--- | :----------------------------------------------------------- |
| d,i  | 有符号十进制数值int。'%d'与'%i'对于输出是同义；但对于scanf()输入二者不同，其中%i在输入值有前缀0x或0时，分别表示16进制或8进制的值。如果指定了精度，则输出的数字不足时在左侧补0。默认精度为1。精度为0且值为0，则输出为空。 |
| u    | 十进制unsigned int。如果指定了精度，则输出的数字不足时在左侧补0。默认精度为1。精度为0且值为0，则输出为空。 |
| f,F  | double型输出10进制定点表示。'f'与'F'差异是表示无穷与NaN时，'f'输出'inf', 'infinity'与'nan'；'F'输出'INF', 'INFINITY'与'NAN'。小数点后的数字位数等于精度，最后一位数字四舍五入。精度默认为6。如果精度为0且没有#标记，则不出现小数点。小数点左侧至少一位数字。 |
| e,E  | double值，输出形式为10进制的([-]d.ddd e[+/-]ddd). E版本使用的指数符号为E（而不是e）。指数部分至少包含2位数字，如果值为0，则指数部分为00。Windows系统，指数部分至少为3位数字，例如1.5e002，也可用Microsoft版的运行时函数_set_output_format 修改。小数点前存在1位数字。小数点后的数字位数等于精度。精度默认为6。如果精度为0且没有#标记，则不出现小数点。 |
| g,G  | double型数值，精度定义为全部有效数字位数。当指数部分在闭区间 [-4,精度] 内，输出为定点形式；否则输出为指数浮点形式。'g'使用小写字母，'G'使用大写字母。小数点右侧的尾数0不被显示；显示小数点仅当输出的小数部分不为0。 |
| x,X  | 16进制unsigned int。'x'使用小写字母；'X'使用大写字母。如果指定了精度，则输出的数字不足时在左侧补0。默认精度为1。精度为0且值为0，则输出为空。 |
| o    | 8进制unsigned int。如果指定了精度，则输出的数字不足时在左侧补0。默认精度为1。精度为0且值为0，则输出为空。 |
| s    | 如果没有用l标志，输出null结尾字符串直到精度规定的上限；如果没有指定精度，则输出所有字节。如果用了l标志，则对应函数参数指向wchar_t型的数组，输出时把每个宽字符转化为多字节字符，相当于调用wcrtomb函数。 |
| c    | 如果没有用l标志，把int参数转为unsigned char型输出；如果用了l标志，把wint_t参数转为包含两个元素的wchart_t数组，其中第一个元素包含要输出的字符，第二个元素为null宽字符。 |
| p    | void *型                                                     |
| a,A  | double型的16进制表示，"[−]0xh.hhhh p±d"。其中指数部分为10进制表示的形式。例如：1025.010输出为0x1.004000p+10。'a'使用小写字母，'A'使用大写字母。（C++11流使用hexfloat输出16进制浮点数） |
| n    | 不输出字符，但是把已经成功输出的字符个数写入对应的整型指针参数所指的变量。 |
| %    | '%'字面值，不接受任何flags, width, precision or length。     |

宽度与精度格式化参数可以忽略，或者直接指定，或者用星号"`*`"表示取对应函数参数的值。例如`printf("%*d", 5, 10)`输出"`10`"；`printf("%.*s", 3, "abcdef")` 输出"`abc`"。

如果函数参数太少，不能匹配所有的格式参数说明符，或者函数参数的类型不匹配，将导致未定义（undefined）行为。过多的函数参数被忽略。许多时候，未定义的行为将导致[格式化字符串攻击](https://zh.wikipedia.org/w/index.php?title=格式化字符串攻击&action=edit&redlink=1)。

某些编译器，如[GCC](https://zh.wikipedia.org/wiki/GCC)，会静态检查printf这一类函数的格式化参数并编译警告存在的问题（当使用编译标志`-Wall`或`-Wformat`）。GCC也会对用户自定义的printf风格函数做静态检查，如果在函数定义时使用了非标准的"`format`" `__attribute__`。

### printf函数的引申

其实不止printf函数可以格式化字符串，printf函数的变形也可以，例如printf_chk

printf_chk和printf不同的地方有两点：

1. 不能使用$n不连续的打印
2. 在使用%n的时候会做一系列检查



## cout 格式化输出

**ostream 类中可实现格式化输出的常用成员方法，以及它们各自的用法:**

| 成员函数          | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| flags(fmtfl)      | 当前格式状态全部替换为 fmtfl。注意，fmtfl 可以表示一种格式，也可以表示多种格式。 |
| precision(n)      | 设置输出浮点数的精度为 n。                                   |
| width(w)          | 指定输出宽度为 w 个字符。                                    |
| fill(c)           | 在指定输出宽度的情况下，输出的宽度不足时用字符 c 填充（默认情况是用空格填充）。 |
| setf(fmtfl, mask) | 在当前格式的基础上，追加 fmtfl 格式，并删除 mask 格式。其中，mask 参数可以省略。 |
| unsetf(mask)      | 在当前格式的基础上，删除 mask 格式。                         |

**其中，对于表 1 中 flags() 函数的 fmtfl 参数、setf() 函数中的 fmtfl 参数和 mask 参数以及 unsetf() 函数 mask 参数，可以选择表 2 中列出的这些值：**

| 标 志           | 作 用                                                        |
| --------------- | ------------------------------------------------------------ |
| ios::boolapha   | 把 true 和 false 输出为字符串                                |
| ios::left       | 输出数据在本域宽范围内向左对齐                               |
| ios::right      | 输出数据在本域宽范围内向右对齐                               |
| ios::internal   | 数值的符号位在域宽内左对齐，数值右对齐，中间由填充字符填充   |
| ios::dec        | 设置整数的基数为 10                                          |
| ios::oct        | 设置整数的基数为 8                                           |
| ios::hex        | 设置整数的基数为 16                                          |
| ios::showbase   | 强制输出整数的基数（八进制数以 0 开头，十六进制数以 0x 打头） |
| ios::showpoint  | 强制输出浮点数的小点和尾数 0                                 |
| ios::uppercase  | 在以科学记数法格式 E 和以十六进制输出字母时以大写表示        |
| ios::showpos    | 对正数显示“+”号                                              |
| ios::scientific | 浮点数以科学记数法格式输出                                   |
| ios::fixed      | 浮点数以定点格式（小数形式）输出                             |
| ios::unitbuf    | 每次输出之后刷新所有的流                                     |

-----

**iostream 中定义的操作符：**

| 操作符      | 描述                       | 输入 | 输出 |
| :---------- | :------------------------- | :--- | :--- |
| boolalpha   | 启用boolalpha标志          | √    | √    |
| dec         | 启用dec标志                | √    | √    |
| endl        | 输出换行标示，并清空缓冲区 |      | √    |
| ends        | 输出空字符                 |      | √    |
| fixed       | 启用fixed标志              |      | √    |
| flush       | 清空流                     |      | √    |
| hex         | 启用 hex 标志              | √    | √    |
| internal    | 启用 internal 标志         |      | √    |
| left        | 启用 left 标志             |      | √    |
| noboolalpha | 关闭boolalpha 标志         | √    | √    |
| noshowbase  | 关闭showbase 标志          |      | √    |
| noshowpoint | 关闭showpoint 标志         |      | √    |
| noshowpos   | 关闭showpos 标志           |      | √    |
| noskipws    | 关闭skipws 标志            | √    |      |
| nounitbuf   | 关闭unitbuf 标志           |      | √    |
| nouppercase | 关闭uppercase 标志         |      | √    |
| oct         | 启用 oct 标志              | √    | √    |
| right       | 启用 right 标志            |      | √    |
| scientific  | 启用 scientific 标志       |      | √    |
| showbase    | 启用 showbase 标志         |      | √    |
| showpoint   | 启用 showpoint 标志        |      | √    |
| showpos     | 启用 showpos 标志          |      | √    |
| skipws      | 启用 skipws 标志           | √    |      |
| unitbuf     | 启用 unitbuf 标志          |      | √    |
| uppercase   | 启用 uppercase 标志        |      | √    |
| ws          | 跳过所有前导空白字符       | √    |      |

-----

**iomanip 中定义的操作符：**

| 操作符                | 描述                     | 输入 | 输出 |
| :-------------------- | :----------------------- | :--- | :--- |
| resetiosflags(long f) | 关闭被指定为f的标志      | √    | √    |
| setbase(int base)     | 设置数值的基本数为base   |      | √    |
| setfill(int ch)       | 设置填充字符为ch         |      | √    |
| setiosflags(long f)   | 启用指定为f的标志        | √    | √    |
| setprecision(int p)   | 设置数值的精度(四舍五入) |      | √    |
| setw(int w)           | 设置域宽度为w            |      | √    |



### 示例

```cpp
#include <iostream>
#include <iomanip>
using namespace std;
int main()
{
    cout<<setiosflags(ios::left|ios::showpoint);  // 设左对齐，以一般实数方式显示
    cout.precision(5);       // 设置除小数点外有五位有效数字 
    cout<<123.456789<<endl;
    cout.width(10);          // 设置显示域宽10 
    cout.fill('*');          // 在显示区域空白处用*填充
    cout<<resetiosflags(ios::left);  // 清除状态左对齐
    cout<<setiosflags(ios::right);   // 设置右对齐
    cout<<123.456789<<endl;
    cout<<setiosflags(ios::left|ios::fixed);    // 设左对齐，以固定小数位显示
    cout.precision(3);    // 设置实数显示三位小数
    cout<<999.123456<<endl; 
    cout<<resetiosflags(ios::left|ios::fixed);  //清除状态左对齐和定点格式
    cout<<setiosflags(ios::left|ios::scientific);    //设置左对齐，以科学技术法显示 
    cout.precision(3);   //设置保留三位小数
    cout<<123.45678<<endl;
    return 0; 
}
```

```
123.46
****123.46
999.123
1.235e+02
```

```cpp
setiosflags(ios::fixed) 固定的浮点显示 
setiosflags(ios::scientific) 指数表示 
setiosflags(ios::left) 左对齐 
setiosflags(ios::right) 右对齐 
setiosflags(ios::skipws 忽略前导空白 
setiosflags(ios::uppercase) 16进制数大写输出 
setiosflags(ios::lowercase) 16进制小写输出 
setiosflags(ios::showpoint) 强制显示小数点 
setiosflags(ios::showpos) 强制显示符号 
```



## 相关参考

[C++ cout格式化输出（超级详细） (biancheng.net)](http://c.biancheng.net/view/7578.html)

[C++ 基本的输入输出 | 菜鸟教程 (runoob.com)](https://www.runoob.com/cplusplus/cpp-basic-input-output.html)

[C 库函数 – printf() | 菜鸟教程 (runoob.com)](https://www.runoob.com/cprogramming/c-function-printf.html)

[printf 格式化输出符号详细说明_xiexievv的专栏-CSDN博客_printf格式化输出](https://blog.csdn.net/xiexievv/article/details/6831194)

[格式规范语法： `printf` 和 `wprintf` 函数 | Microsoft Docs](https://docs.microsoft.com/zh-cn/cpp/c-runtime-library/format-specification-syntax-printf-and-wprintf-functions?view=msvc-160)

[printf格式化字符串 | 上善若水 (introspelliam.github.io)](https://introspelliam.github.io/2017/08/04/printf格式化字符串/)

