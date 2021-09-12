---
title: C++consteptr常量表达式
top: false
mathjax: true
categories:
- C++
---

-----



-----





## constexpr

constexpr 说明符声明可以在编译时求得函数或变量的 值。 然后这些变量和函数（若给定了合适的函数实参）即可用于仅允许编译时常量表达式之处。

## constexpr变量



**constexpr 变量**必须满足下列要求：

- 其类型必须是字面类型 (LiteralType) 。
- 它必须被立即初始化
- 其初始化的全表达式，包括所有隐式转换、构造函数调用等，都必须是常量表达式







## constexpr函数

**constexpr 函数**必须满足下列要求：

- 其返回类型（若存在）必须是字面类型 (LiteralType)
- 其每个参数都必须是字面类型 (LiteralType)
  对于构造函数与析构函数 (C++20 起)，该类必须无虚基类
- 至少存在一组实参值，使得函数的一个调用为核心常量表达式的被求值的子表达式（对于构造函数为足以用于常量初始化器） (C++14 起)。不要求对这点的诊断。

```cpp
constexpr int multiply (int x, int y)
{
return x * y;
}
// 将在编译时计算
const int val = multiply( 10, 10 );
```



一个constexpr函数有一些必须遵循的严格要求：

- 函数中只能有一个return语句（有极少特例）
- 只能调用其它constexpr函数
- 只能使用全局constexpr变量



## constexpr if



**示例：**

```cpp
#include <iostream>
#include <stdexcept>
 
// C++11 constexpr 函数使用递归而非迭代
// （C++14 constexpr 函数可使用局部变量和循环）
constexpr int factorial(int n)
{
    return n <= 1? 1 : (n * factorial(n - 1));
}
 
// 字面类
class conststr {
    const char* p;
    std::size_t sz;
public:
    template<std::size_t N>
    constexpr conststr(const char(&a)[N]): p(a), sz(N - 1) {}
 
    // constexpr 函数通过抛异常来提示错误
    // C++11 中，它们必须用条件运算符 ?: 这么做
    constexpr char operator[](std::size_t n) const
    {
        return n < sz ? p[n] : throw std::out_of_range("");
    }
    constexpr std::size_t size() const { return sz; }
};
 
// C++11 constexpr 函数必须把一切放在单条 return 语句中
// （C++14 无此要求）
constexpr std::size_t countlower(conststr s, std::size_t n = 0,
                                             std::size_t c = 0)
{
    return n == s.size() ? c :
           'a' <= s[n] && s[n] <= 'z' ? countlower(s, n + 1, c + 1) :
                                        countlower(s, n + 1, c);
}
 
// 输出要求编译时常量的函数，用于测试
template<int n>
struct constN
{
    constN() { std::cout << n << '\n'; }
};
 
int main()
{
    std::cout << "4! = " ;
    constN<factorial(4)> out1; // 在编译时计算
 
    volatile int k = 8; // 不允许使用 volatile 者优化
    std::cout << k << "! = " << factorial(k) << '\n'; // 运行时计算
 
    std::cout << "the number of lowercase letters in \"Hello, world!\" is ";
    constN<countlower("Hello, world!")> out2; // 隐式转换到 conststr
}
```

```
4! = 24
8! = 40320
the number of lowercase letters in "Hello, world!" is 9
```





## 相关文章

[C++11之常量表达式_an505479313的博客-CSDN博客_c++11 常量表达式](https://blog.csdn.net/an505479313/article/details/52819885?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-0&spm=1001.2101.3001.4242)

[constexpr 说明符(C++11 起) - cppreference.com](https://zh.cppreference.com/w/cpp/language/constexpr)

[constexpr (C++) | Microsoft Docs](https://docs.microsoft.com/en-us/cpp/cpp/constexpr-cpp?view=msvc-160)

https://zh.wikipedia.org/wiki/Constexpr