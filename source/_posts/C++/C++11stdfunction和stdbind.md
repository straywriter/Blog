---
title: C++11 stdfunction和stdbind
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- C++
---

-----







> ### `std::function`
>
> Lambda 表达式的本质是一个和函数对象类型相似的类类型（称为闭包类型）的对象（称为闭包对象）， 当 Lambda 表达式的捕获列表为空时，闭包对象还能够转换为函数指针值进行传递，例如:
>
> ```cpp
> #include <iostream>
> 
> using foo = void(int); // 定义函数类型, using 的使用见别名语法
> void functional(foo f) { // 定义在参数列表中的函数类型 foo 被视为退化后的函数指针类型 foo*
>     f(1); // 通过函数指针调用函数
> }
> 
> int main() {
>     auto f = [](int value) {
>         std::cout << value << std::endl;
>     };
>     functional(f); // 传递闭包对象，隐式转换为 foo* 类型的函数指针值
>     f(1); // lambda 表达式调用
>     return 0;
> }
> ```
>
> 上面的代码给出了两种不同的调用形式，一种是将 Lambda 作为函数类型传递进行调用， 而另一种则是直接调用 Lambda 表达式，在 C++11 中，统一了这些概念，将能够被调用的对象的类型， 统一称之为**可调用类型**。而这种类型，便是通过`std::function`引入的。
>
> C++11`std::function`是一种通用、多态的函数封装， 它的实例可以对任何可以调用的目标实体进行存储、复制和调用操作， 它也是对 C++中现有的可调用实体的一种**类型安全**的包裹（相对来说，函数指针的调用不是类型安全的）， 换句话说，就是函数的容器。当我们有了函数的容器之后便能够更加方便的将函数、函数指针作为对象进行处理。 例如：
>
> ```cpp
> #include <functional>
> #include <iostream>
> 
> int foo(int para) {
>     return para;
> }
> 
> int main() {
>     // std::function 包装了一个返回值为 int, 参数为 int 的函数
>     std::function<int(int)> func = foo;
> 
>     int important = 10;
>     std::function<int(int)> func2 = [&](int value) -> int {
>         return 1+value+important;
>     };
>     std::cout << func(10) << std::endl;   // 10
>     std::cout << func2(10) << std::endl;  // 21
> }
> ```
>
> **std::bind和std::placeholders**
>
> `std::bind`则是用来绑定函数调用的参数的， 它解决的需求是我们有时候可能并不一定能够一次性获得调用某个函数的全部参数，通过这个函数， 我们可以将部分调用参数提前绑定到函数身上成为一个新的对象，然后在参数齐全后，完成调用。 例如：
>
> ```cpp
> int foo(int a, int b, int c) {
>     ;
> }
> int main() {
>     // 将参数1,2绑定到函数 foo 上，但是使用 std::placeholders::_1 来对第一个参数进行占位
>     auto bindFoo = std::bind(foo, std::placeholders::_1, 1,2);
>     // 这时调用 bindFoo 时，只需要提供第一个参数即可
>     bindFoo(1);
> }
> ```
>
> > **提示：**注意`auto`关键字的妙用。有时候我们可能不太熟悉一个函数的返回值类型， 但是我们却可以通过`auto`的使用来规避这一问题的出现。
>
> 最后看一个来自于https://zhuanlan.zhihu.com/p/56176729的例子，来对以上的内容进行总结：
>
> ```cpp
> void show(int x) { std::cout << x << std::endl; }
> 
> // Remove arguments from show(...)
> using myFunc= decltype(std::bind(show, 3));                  // 注释1
> auto myShow= std::make_shared<myFunc>(std::bind(show, 3));   // 注释2
> 
> // Remove return type from show(...)
> auto showTranslate= [myShow] { myShow->operator()(); };      // 注释3
> showTranslate();                                             // 注释4
> // output： 3
> ```
>
> 注释1，[std::bind()](https://link.zhihu.com/?target=http%3A//www.cplusplus.com/reference/functional/bind/)返回的myFunc是[std::function](https://link.zhihu.com/?target=http%3A//www.cplusplus.com/reference/functional/function/)类型，下面是std::function模板的用法：
>
> ```cpp
> std::function<void ()> func = std::bind(show, 3); //不需要传入参数了
> func();  // output: 3
> func.operator()();  // output: 3
> ```
>
> 注释2，由[std::make_shared](https://link.zhihu.com/?target=http%3A//www.cplusplus.com/reference/memory/make_shared/)生成一个myFunc类型对象，并返回一个[std::shared_ptr](https://link.zhihu.com/?target=http%3A//www.cplusplus.com/reference/memory/shared_ptr/)指针
>
> 注释3，把lambda表达式赋值给showTranslate，捕获变量myShow，然后调用operator()()方法。“myShow->operator()();”还有另外一种写法：













**https://www.jianshu.com/p/f191e88dcc80**