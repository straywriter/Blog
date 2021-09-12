---
title: C++  const_cast
top: false
mathjax: true
categories:
- C++
---

-----



C++提供了四个转换运算符：
\1. const_cast (expression)
\2. static_cast (expression)
\3. reinterpret_cast (expression)
\4. dynamic_cast (expression)

## const_cast (expression)

用const_cast来去除const限定

```
const int constant = 21;
const int* const_p = &constant;
int* modifier = const_cast<int*>(const_p);
*modifier = 7;1234
```





这样修饰后，就可以顺利编译通过。

**原因：**我们可能调用了一个参数不是const的函数，而我们要传进去的实际参数确实const的，但是我们知道这个函数是不会对参数做修改的。于是我们就需要使用const_cast去除const限定，以便函数能够接受这个实际参数。

*使用const_cast去除const限定的目的绝对不是为了修改它的内容，只是出于无奈*





## 相关参考

[cppreference](https://en.cppreference.com/w/cpp/language/const_cast)