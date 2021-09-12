---
title: C++ string的SOW和SSO策略
top: false
mathjax: true
categories:
- C++
---

-----





COW(Copy-On-Write)：写时复制，即复制的时候不立即申请内存(浅拷贝)，而在写操作的时候才开始申请内存进行复制。

SSO(Small String Optimization)：短字符串优化，即复制时立即申请内存(深拷贝)，但当字符串较短时存在栈中。



## 相关参考

[c++ - Meaning of acronym SSO in the context of std::string - Stack Overflow](https://stackoverflow.com/questions/10315041/meaning-of-acronym-sso-in-the-context-of-stdstring)

[C++ 字符串拷贝优化策略：Eager-Copy、SSO 与 COW | 曜彤.手记 (yhspy.com)](https://www.yhspy.com/2020/05/24/C-字符串拷贝优化策略：Eager-Copy、SSO-与-COW/)

[C++ Small String Optimization | Yihao Liu (tc-imba.github.io)](https://tc-imba.github.io/posts/cpp-sso)

[Copy-on-write - Wikipedia](https://en.wikipedia.org/wiki/Copy-on-write)

[c++ - What are the mechanics of short string optimization in libc++? - Stack Overflow](https://stackoverflow.com/questions/21694302/what-are-the-mechanics-of-short-string-optimization-in-libc)

[elliotgoodrich/SSO-23: Memory optimal Small String Optimization implementation for C++ (github.com)](https://github.com/elliotgoodrich/SSO-23)

[C++ folly库解读（一） Fbstring —— 一个完美替代std::string的库 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/348614098)

[folly/FBString.md at master · facebook/folly (github.com)](https://github.com/facebook/folly/blob/master/folly/docs/FBString.md)

[漫步Facebook开源C++库folly(1)：string类的设计 - PromisE_谢 - 博客园 (cnblogs.com)](https://www.cnblogs.com/promise6522/archive/2012/06/05/2535530.html)

[C++ folly库解读（一） Fbstring —— 一个完美替代std::string的库(上) (qq.com)](https://mp.weixin.qq.com/s?__biz=MzI1OTI4MTEyMQ==&mid=2247484369&idx=1&sn=625c17684a5c5b4b32f5410b86591f2e&chksm=ea7a1a5fdd0d93496a925d58a3d6352ff8c12b76bedfb7e88e5a45483dbfde485199c45c2901&scene=21#wechat_redirect)