---
title: C++格式化输出
top: false
mathjax: true
categories:
- C++
---

-----



# 



-----







## printf 格式化输出









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