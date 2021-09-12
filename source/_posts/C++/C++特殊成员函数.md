---
title: C++特殊成员函数
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- C++
---

-----



- Default constructors //默认构造函数
- Destructors //析构函数
- Copy constructors //拷贝构造函数
- Copy assignment operators //拷贝赋值操作
- Move constructors // move 构造函数
- Move assignment operators //move 复制操作



default 和delete



## 简介

- default constructor
- copy constructor
- move constructor
- destructor
- copy assignment
- move assignment
- Conversion constructors



|         Function         |                   syntax                    |
| :----------------------: | :-----------------------------------------: |
|   Default constructor    |                 `Class();`                  |
|     Copy constructor     |        `Class(const Class& other);`         |
|     Move constructor     |      `Class(Class&& other) noexcept;`       |
| Copy assignment operator |   `Class& operator=(const Class& other);`   |
| Move assignment operator | `Class& operator=(Class&& other) noexcept;` |
|        Destructor        |            `~Class() noexcept;`             |



## Default constructor 默认构造函数

默认构造函数（default constructor）就是在没有显式提供初始化式时调用的构造函数。它由不带参数的构造函数，或者为所有的形参提供默认实参的构造函数定义。如果定义某个类的变量时没有提供初始化时就会使用默认构造函数。默认构造函数会对数据成员进行默认初始化



**注意事项：**

- 如果被初始化地对象没有缺省构造函数，则编译时报错。
- 如果一个类没有显式定义构造函数，编译器将为其**隐式**地定义缺省构造函数。隐式定义的缺省构造函数的函数体为空。
- 如果类显式地定义了一些构造函数，但不是缺省构造函数，编译器则不会为该类隐式定义缺省构造函数。
- 该类就没有缺省构造函数了。也就是说，并不是所有类都有缺省构造函数。这是很多常见错误的原因。
- 如果类的基类没有缺省构造函数，那么编译器也不会为该类隐式地定义缺省构造函数。因为该类即使隐式地定义缺省构造函数，也无法初始化其基类。
- 如果类的基类的缺省构造函数为私有，那么编译器会为该类隐式地定义缺省构造函数，但编译报错“cannot access private member declared in class 基类名字”。
- 对于union数据类型，如果它包含了一个成员有非缺省的构造函数，那么这个union类型的构造函数必须显式调用这个成员类型的构造函数。



### 默认构造函数的调用

**C++中，默认构造函数会在下列情形被自动调用：**

- 对象被定义时无参数，例如：`MyClass x;`; 或动态分配对象时无参数列表，例如：`new MyClass`或`new MyClass()`; 缺省构造函数用于初始化对象。
- 对象数组被定义时，例如：`MyClass x[10];`; 或被动态分配时，例如：`new MyClass [10]`;缺省构造函数初始化数组的每个对象。
- 当派生类在其初始化列表（initializer list）中没有显式调用其基类对象的构造函数时，基类的缺省构造函数被自动调用。
- 当一个类的构造函数的初始化列表中没有显式调用其对象成员（object component或object-valued field）的构造函数时，对象成员的缺省构造函数被自动调用。
- C++标准程序库中，一些容器的填充值使用了值对象的缺省构造函数，如果这些值没有显式地给出。例如：`vector<MyClass>(10);`用10个元素初始化vector，这些元素用其缺省构造函数来初始化。



### 编译器需要默认构造函数的四种情况

1. 含有类对象数据成员，该类对象类型有默认构造函数。

2. 基类带有默认构造函数的派生类。

3. 带有虚函数的类　　

   类带有虚函数可以分为两种情况：

   1. 类本身定义了自己的虚函数
   2. 类从继承体系中继承了虚函数（成员函数一旦被声明为虚函数，继承不会改变虚函数的”虚性质“）。





**示例：**

对象生成时如果没有显式地调用构造函数，则缺省构造函数会被自动调用。

```cpp
class MyClass
{
  public:
    MyClass();             // constructor declared

  private:
    int x;
};

MyClass :: MyClass()       // constructor defined   
{
    x = 100;
}

int main()
{
    MyClass object;        // object created 
}                          // => default constructor called automatically
```

显示调用

```cpp
int main()
{
    MyClass * pointer = new MyClass();  // object created 
}    
```

### 示例

在上述这些情形中，如果被初始化地对象没有缺省构造函数，则编译时报错。

如果一个类没有显式定义构造函数，编译器将为其**隐式**地定义缺省构造函数（下文给出了例外情形）。隐式定义的缺省构造函数的函数体为空。例如：

```cpp
class MyClass
{
    int x;                 // no constructor          
};                         // => the compiler produces an (implicit) default constructor
int main()
{
    MyClass object;        // no error: the (implicit) default constructor is called
}
```

如果类显式地定义了一些构造函数，但不是缺省构造函数，编译器则不会为该类隐式定义缺省构造函数。该类就没有缺省构造函数了。也就是说，并不是所有类都有缺省构造函数。这是很多常见错误的原因。例如：

```cpp
class MyClass 
{
  public:
    MyClass (int y);         // a constructor     

  private:
    int x;
};
MyClass :: MyClass (int y)
{
    x = y;
}
int main()
{
    MyClass object(100);     // constructor called
    MyClass *pointer;        // for declaration do not need to know about existing constructors
    pointer = new MyClass(); // error: no default constructor    
    return 0;
}
```

如果类的基类没有缺省构造函数，那么编译器也不会为该类隐式地定义缺省构造函数。因为该类即使隐式地定义缺省构造函数，也无法初始化其基类。例如：

```cpp
class A
{
public:
	explicit A(int){}
};

class B: public A
{
};

int main()
{
	B b1;         //编译报错
}
```

如果类的基类的缺省构造函数为私有，那么编译器会为该类隐式地定义缺省构造函数，但编译报错“cannot access private member declared in class 基类名字”。例如：

```cpp
class A
{
   A(){};
public:
	
};

class B: public A
{
};

int main()
{
	B b1;         //编译报错
}
```

对于union数据类型，如果它包含了一个成员有非缺省的构造函数，那么这个union类型的构造函数必须显式调用这个成员类型的构造函数。例如：

```cpp
struct bar {
	bar() {};
	bar(int) {};
};
union foo { 
	int i;
	float j;
	bar b;
        // foo(): b{ } { };    //只有增加这行，才能成功地构造一个foo类型的对象
};
foo vvv;         // syntax error: attempting to reference a deleted function
```



## Copy constructor 拷贝构造函数





## Move constructor 移动构造函数



## Copy assignment operator



## Move assignment operator



## Destructor



## 示例

### 验证默认复制构造函数是浅拷贝

```cpp
#include <iostream>
using namespace std;

class Foo {
public:
    Foo():_data(nullptr),_len(0){}
    Foo(unsigned int len):_data(new unsigned char (len)), _len(len) {
        // std::copy()
    }
    // 默认是浅拷贝
    unsigned char* get_data() const { return _data;}
    unsigned int get_len() const {return _len;}
private:
    unsigned char* _data;
    unsigned int _len;
};
int main()
{
    Foo a(10);
    std::cout << static_cast<const void*>(a.get_data()) << std::endl;
    std::cout << a.get_len() << std::endl;
    Foo b = a;
    std::cout << static_cast<const void*>(b.get_data()) << std::endl;
    std::cout << b.get_len() << std::endl;
    return 0;
}
```

```
0xd46d80
10
0xd46d80
10
```





## 相关参考

[拷贝构造函数wiki](https://zh.wikipedia.org/wiki/%E8%A4%87%E8%A3%BD%E5%BB%BA%E6%A7%8B%E5%AD%90)

[C++: 默认构造函数](https://zhuanlan.zhihu.com/p/38582734)