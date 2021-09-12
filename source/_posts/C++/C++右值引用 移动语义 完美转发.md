---
title: C++右值引用 移动语义 完美转发
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- C++
---

 -----

##  

- 右值引用
  - 左值和右值
  - 左值引用和右值引用
  - 常量左值引用
  - 万能引用
  - 引用折叠
- 移动语言
  - 移动构造和移动赋值
  - universal references(通用引用)
- 完美转发



## 右值引用





```
int x = 20;   // 左值
int&& rx = x * 2;  // x*2的结果是一个右值，rx延长其生命周期
int y = rx + 2;   // 因此你可以重用它：42
rx = 100;         // 一旦你初始化一个右值引用变量，该变量就成为了一个左值，可以被赋值
```



右值引用的作用

- 延长生命周期
-  转移不可拷贝的资源







### 左值和右值

- 在C++11提出右值引用之前，C++03及更早的C++标准中，表达式的“值分类”（value categories）属性为左值或右值。
- 左值是指表达式结束后依然存在的*持久化对象*，右值是指表达式结束时就不再存在的*临时对象*。
-  左值和右值的区分标准在于能否获取地址。 
- `C++`中所有的值都必然属于左值、右值二者之一。
- 所有的具名变量或者对象都是左值，而右值不具名。
- 区分左值和右值的便捷方法：看能不能对表达式取地址，如果能，则为左值，否则为右值。
- 凡是真正的存在内存当中，而不是寄存器当中的值就是左值，其余的都是右值。
- 右值可以是字面量、临时对象等表达式。
- C++的const左值是不可赋值的；而作为临时对象的右值可能允许被赋值

**C++03及以前的标准定义了在表达式中左值到右值的三类隐式自动转换：**

- 左值转化为右值；如整数变量`i`在表达式 `（i+3）`
- 数组名是常量左值，在表达式中转化为数组首元素的地址值
- 函数名是常量左值，在表达式中转化为函数的地址值

### 左值引用与右值引用

- 引用可以给左值起一个别名，当然也可以给右值起一个别名。

- 绑定左值的引用就是左值引用，绑定右值的引用就是右值引用。

```
int i = 0;
int& j = i;               // 左值引用
int&& k = 0;              // 右值引用
```

`T`是一个具体类型：

1. 左值引用， 使用 `T&`, 只能绑定**左值** 
2. 右值引用， 使用 `T&&`， 只能绑定**右值** 
3. 常量左值， 使用 `const T&`, 既可以绑定**左值**又可以绑定**右值** 
4. 右值引用根据其修饰符的不同，也可以分为非常量右值引用和常量右值引用。
5. **非常量右值引用**只能绑定到非常量右值，不能绑定到非常量左值、常量左值和常量右值
6. **常量右值引用**可以绑定到非常量右值和常量右值，不能绑定到非常量左值和常量左值
7. 已命名的**右值引用**，编译器会认为是个**左值** 
8. 编译器有返回值优化，但不要过于依赖

-----



### 常量左值引用

- **常量左值引用是个万能的引用类型。它既可以绑定到左值也可以绑定到右值**。

- 它像右值引用一样可以延长右值的生命期。不过相比于右值引用所引用的右值，常量左值引用的右值在它的余生中只能是只读的；
- 常量左值引用可以绑定到所有类型的值，包括非常量左值、常量左值、非常量右值和常量右值。
- 常量左值应用可以理解为常量引用。
- 使用左值引用初始化右值，安全地将右值绑定(bind)到左值的唯一方法是通过将左值标记为const。



### 万能引用

- 当T是一个具体的类型时，T&&表示右值引用，只能绑定右值。

- 若T&&在发生自动类型推断的时候，它是未定的引用类型，如果被一个左值初始化，它就是一个左值引用；
- 如果它被一个右值初始化，它就是一个右值引用，
- 它是左值引用还是右值引用取决于它的初始化。因此，也被称为万能引用。





### 引用折叠/转发引用

- 在C++中，引用的引用是非法的。对于C++语言，不可以在源程序中直接对引用类型再施加引用。`T& &`将编译报错。
- 编译器在通过类型别名或模板参数推导等语境中，会间接定义出引用的引用，这时引用会形成折叠。
- 引用折叠只会发生在模板实例化、auto类型推导、创建和运用typedef和别名声明、以及decltype语境中。
- 万能引用就是利用模板推导和引用折叠的相关规则，生成不同的实例化模板来接收传进来的参数。

**具体规则：**

- 所有右值引用折叠到右值引用上仍然是一个右值引用。如T&& &&折叠为T&&。

- 所有的其他引用类型之间的折叠都将变成左值引用。如T& &, T& &&, T&& &折叠为T&。可见左值引用会传染，沾上一个左值引用就变左值引用了。根本原因：在一处声明为左值，就说明该对象为持久对象，编译器就必须保证此对象可靠(左值)。
- T& &变为T&
- T& &&变为T&
- T&& &变为T&
- T&& &&变为T&&

**所以：**

- 当万能引用(T&&)绑定到左值时，T会被推导为T&类型。从而参数类型为`T& &&`，引用折叠后的类型为T&，左值引用；
- 当万能引用(T&&)绑定到右值时，T会被推导为T&&类型。从而参数类型为`T&& &&`，引用折叠后的类型为T&&，右值引用。



#### 模板参数类型推导

对函数模板`template<typename T>void foo(T&&);`，应用上述引用折叠规则，可推导出如下结论：

- 如果实参是类型A的左值，则模板参数T的类型为A&，形参类型为A&；
- 如果实参是类型A的右值，则模板参数T的类型为A&&，形参类型为A&&。

```cpp
template <class T > class vector {
    public: 
    void push_back(T&& x); // T是类模板参数 ⇒ 该成员函数不需要类型推导;这里的函数参数类型就是T的右值引用
     template <class Args> void emplace_back(Args&& args); //  该成员函数是个函数模板，有自己的模板参数，需要类型推导
};
```

```cpp
template<typename T>void f(const T&& param); // 这里的“&&”不需要类型推导，意味着“常量类型T的右值引用”
template<typename T>void f(std::vector<T>&& param);  // 这里的“&&”不需要类型推导，意味着std::vector<T>的右值引用
```

#### typedef的类型推导

```cpp
template<typename T>    class Widget {
    typedef T& LvalueRefType;

};
Widget<int&&> w; // LvalueRefType的类型为int&
void f(Widget<int&>::LvalueRefType&& param); //param的类型为int&
```



#### decltype类型推导

```cpp
int var;
decltype(var)&& v1=std::move(var); //类型是int&&
```



### 示例

**万能引用示例**

```cpp
#include <iostream>

template<typename T>
void fun(T&& t) {}

int main(int argc, char *argv[]) 
{
  int x = 10;
  fun(10);                // t是右值
  fun(x);                 // t是左值

  return 0;
}
```

**左值引用使用右值引用示例**

```cpp
struct obj
{
};

obj bar()
{
    obj x;
    return x;
}

int main()
{
    obj &a1 = bar();
    obj &&a2 = bar();
    return 0;
};
```

```
In function 'int main()':
10:16: error: cannot bind non-const lvalue reference of type 'obj&' to an rvalue of type 'obj'
  obj & a1 = bar();
```

**正确：**

```cpp
struct obj
{
};

obj bar()
{
    obj x;
    return x;
}

int main()
{
    const obj &a1 = bar();
    obj &&a2 = bar();
    return 0;
};
```

**移动构造器和移动赋值运算符示例：**

```cpp
struct X  
{  
    X();                         //缺省构造器  
    X(const X& that);            //拷贝构造器  
    X(X&& that);                 //移动构造器  
    X& operator=(const X& that); //拷贝赋值运算符  
    X& operator=(X&& that);      //移动赋值运算符  
};  
  
X a;                             //调用缺省构造器  
X b = a;                         //调用拷贝构造器  
X c = std::move(b);              //调用移动构造器  
b = a;                           //调用拷贝赋值运算符  
c = std::move(b);                //调用移动赋值运算符  
```



## 移动语义

作为一种追求执行效率的语言，C++在用临时对象或函数返回值给左值对象赋值时的深度拷贝（deep copy）一直受到诟病。考虑到临时对象的生命期仅在表达式中持续，如果把临时对象的内容直接移动（move）给被赋值的左值对象，效率改善将是显著的。这就是**移动语义**的来源。



- 与传统的拷贝赋值运算符（copy assignment）成员函数、拷贝构造（copy ctor）成员函数对应，移动语义需要有移动赋值（move assignment）成员函数、移动构造（move ctor）成员函数的实现机制。可以通过函数重载来确定是调用拷贝语义还是移动语义的实现。


- 右值引用就是为了实现移动语义与完美转发所需要而设计出来的新的数据类型。右值引用的实例对应于临时对象；右值引用并区别于左值引用，用作形参时能重载辨识（overload resolution）是调用拷贝语义还是移动语义的函数。

- 无论是传统的左值引用还是C++11引进的右值引用，从编译后的反汇编层面上，都是对象的存储地址与自动解引用（dereference）。因此，右值引用与左值引用的变量都不能悬空(dangling)，也即定义时必须初始化从而绑定到一个对象上。

- 右值引用变量绑定的对象，是编程者认为可以通过移动语义移走其内容的对象，对这种对象就需要定义为一种独特的值分类，即C++11标准称之为“临终值”（eXpire Value）。

- 临终值对象既有存储地址因此可以绑定到右值引用变量上，而且它又是一个即将停止使用的对象可以被移走内容。所以临终值既不同于左值，也不同于传统的右值（C++11称之为纯右值），不能取地址运算（&）。另一方面，临终值兼有传统的左值与右值的性质：既对应于一个（临时）对象，称之为有标识（identity）；同时其内容可以移走，称之为可移动性（movability）。C++11标准把临终值与左值合称为广义左值，即指向某个物理存在的对象；把临终值与纯右值（对应C++03时的右值概念）合称为右值（C++11重新定义的概念），其内容可以移走（该右值生命期到此为止，此后将不再使用）。之所以称为右值而不叫做广义右值，是因为右值引用即可以与临终值对象绑定，也可以与纯右值对象绑定（这时往往自动生成一个临时对象）。

- C++语言在引入了右值引用之后，面临着一个问题：如何让编程者指出哪个对象具有临终值？这有两种显式指定方法：如果函数（或运算符）的返回类型为右值引用，或者通过类型转换如static_cast<Type&&>或者std::move()模板函数。



### 移动语义

- 移动语义（move semantics）是指某个对象接管另一个对象所拥有的外部资源的所有权。
- 移动语义需要通过移动（窃取）其他对象所拥有的资源来完成。

**移动语义的具体实现（即一次that对象到this对象的移动（move））通常包含以下若干步骤：**

> - 如果this对象自身也拥有资源，释放该资源
> - 将this对象的指针或句柄指向that对象所拥有的资源
> - 将that对象原本指向该资源的指针或句柄设为空值

### 拷贝语义

- 传统的拷贝语义（copy semantics）是指某个对象拷贝（复制）另一个对象所拥有的外部资源并获得新生资源的所有权。

**拷贝语义的具体实现（即一次that对象到this对象的拷贝（copy））通常包含以下若干步骤：**

> - 如果this对象自身也拥有资源，释放该资源
>
> - 拷贝（复制）that对象所拥有的资源
>
> - 将this对象的指针或句柄指向新生的资源
>
> - 如果that对象为临时对象（右值），那么拷贝完成之后that对象所拥有的资源将会因that对象被销毁而即刻得以释放

### 示例

```cpp
#include <iostream>  
  
class MemoryBlock  
{  
public:  
  
    // 构造器（初始化资源）  
    explicit MemoryBlock(size_t length)  
        : _length(length)  
        , _data(new int[length])  
    {  
    }  
  
    // 析构器（释放资源）  
    ~MemoryBlock()  
    {  
        if (_data != nullptr)  
        {  
            delete[] _data;  
        }  
    }  
  
    // 拷贝构造器（实现拷贝语义：拷贝that）  
    MemoryBlock(const MemoryBlock& that)  
        // 拷贝that对象所拥有的资源  
        : _length(that._length)  
        , _data(new int[that._length])  
    {  
        std::copy(that._data, that._data + _length, _data);  
    }  
  
    // 拷贝赋值运算符（实现拷贝语义：释放this ＋ 拷贝that）  
    MemoryBlock& operator=(const MemoryBlock& that)  
    {  
        if (this != &that)  
        {  
            // 释放自身的资源  
            delete[] _data;  
  
            // 拷贝that对象所拥有的资源  
            _length = that._length;  
            _data = new int[_length];  
            std::copy(that._data, that._data + _length, _data);  
        }  
        return *this;  
    }  
  
    // 移动构造器（实现移动语义：移动that）  
    MemoryBlock(MemoryBlock&& that)  
        // 将自身的资源指针指向that对象所拥有的资源  
        : _length(that._length)  
        , _data(that._data)  
    {  
        // 将that对象原本指向该资源的指针设为空值  
        that._data = nullptr;  
        that._length = 0;  
    }  
  
    // 移动赋值运算符（实现移动语义：释放this ＋ 移动that）  
    MemoryBlock& operator=(MemoryBlock&& that)  
    {  
        if (this != &that)  
        {  
            // 释放自身的资源  
            delete[] _data;  
  
            // 将自身的资源指针指向that对象所拥有的资源  
            _data = that._data;  
            _length = that._length;  
  
            // 将that对象原本指向该资源的指针设为空值  
            that._data = nullptr;  
            that._length = 0;  
        }  
        return *this;  
    }  
private:  
    size_t _length; // 资源的长度  
    int* _data; // 指向资源的指针，代表资源本身  
};  
  
MemoryBlock f() { return MemoryBlock(50); }  
  
int main()  
{  
    MemoryBlock a = f();            // 调用移动构造器，移动语义  
    MemoryBlock b = a;              // 调用拷贝构造器，拷贝语义  
    MemoryBlock c = std::move(a);   // 调用移动构造器，移动语义  
    a = f();                        // 调用移动赋值运算符，移动语义  
    b = a;                          // 调用拷贝赋值运算符，拷贝语义  
    c = std::move(a);               // 调用移动赋值运算符，移动语义  
}  
```



## 完美转发

- 所谓转发，就是通过一个函数将参数继续转交给另一个函数进行处理，原参数可能是右值，可能是左值，如果还能继续保持参数的原有特征，那么它就是完美的。 


- 完美转发也是C++11标准引入右值引用这一概念所要实现的目标之一。万能引用的引入主要是为了解决完美转发问题。
- 完美转发（perfect forwarding）问题是指函数模板在向其他函数转发（传递）自身参数（形参）时该如何保留该参数（实参）的左右值属性的问题。
- 函数模板在向其他函数转发（传递）自身形参时，如果相应实参是左值，它就应该被转发为左值；同样如果相应实参是右值，它就应该被转发为右值。这样做是为了保留在其他函数针对转发而来的参数的左右值属性进行不同处理（比如参数为左值时实施拷贝语义；参数为右值时实施移动语义）的可能性。
- 如果将自身参数不分左右值一律转发为左值，其他函数就只能将转发而来的参数视为左值，从而失去针对该参数的左右值属性进行不同处理的可能性。







### 示例

-----

**在C++程序设计中，一个常见的类工厂函数，如下例：**

```cpp
template <typename T, typename Arg> 
shared_ptr<T> factory(Arg arg)
{
    return shared_ptr<T>( new T(arg) );
}
```

参数对象arg在上例中是传值方式传递，这带来了生成额外的临时对象的代价。对于类工厂函数，完美的参数传递应该是引用方式传递。因而，在`boost:bind`中，参数是左值引用：

```cpp
template <typename T, typename Arg> 
shared_ptr<T> factory(Arg& arg)
{
    return shared_ptr<T>( new T(arg) );
}
```

这种实现的问题是形参不能绑定右值实参。如`factory<X>(102)`将编译报错。进一步解决办法是按常量引用方式传递参数，如下例：

```cpp
template <typename T, typename Arg> 
shared_ptr<T> factory(const Arg& arg)
{
    return shared_ptr<T>( new T(arg) );
}
```

这种实现的问题是不能支持移动语义。

形参使用右值引用可以解决完美转发问题。

-----



```cpp
template<class T>
int f(T&& x) {                    // x 是转发引用
    return g(std::forward<T>(x)); // 从而能被转发
}
int main() {
    int i;
    f(i); // 实参是左值，调用 f<int&>(int&), std::forward<int&>(x) 是左值
    f(0); // 实参是右值，调用 f<int>(int&&), std::forward<int>(x) 是右值
}
 
template<class T>
int g(const T&& x); // x 不是转发引用：const T 不是无 cv 限定的
 
template<class T> struct A {
    template<class U>
    A(T&& x, U&& y, int* p); // x 不是转发引用：T 不是构造函数的类型模板形参
                             // 但 y 是转发引用
};
```



-----

悬垂引用
尽管引用一旦初始化，就始终指代一个有效的对象或函数，但有可能创建一个程序，被指代对象的生存期结束，但引用仍保持可访问（悬垂（dangling））。访问这种引用是未定义行为。 一个常见例子是返回自动变量的引用的函数：

```cpp
std::string& f()
{
    std::string s = "Example";
    return s; // 退出 s 的作用域:
              // 调用其析构函数并解分配其存储
}
 
std::string& r = f(); // 悬垂引用
std::cout << r;       // 未定义行为：从悬垂引用读取
std::string s = f();  // 未定义行为：从悬垂引用复制初始化
```



-----

auto&&，但当其从花括号包围的初始化器列表推导时则不是：

```cpp
auto&& vec = foo();       // foo() 可以是左值或右值，vec 是转发引用
auto i = std::begin(vec); // 也可以
(*i)++;                   // 也可以
g(std::forward<decltype(vec)>(vec)); // 转发，保持值类别
 
for (auto&& x: f()) {
  // x 是转发引用；这是使用范围 for 循环的最安全方式
}
 
auto&& z = {1, 2, 3}; // *不是*转发引用（初始化器列表的特殊情形）
```



## 相关参考

[右值引用wiki](https://zh.wikipedia.org/zh-hans/%E5%8F%B3%E5%80%BC%E5%BC%95%E7%94%A8)

[pdf N0345](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/1993/N0345.pdf)

[C++11 右值引用与移动构造函数](https://www.jianshu.com/p/cb82c8b72c6b)