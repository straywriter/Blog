---
title: C++ std::forward完美转发
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- C++
---

-----



### a



完美转发

源码剖析

完美转发失败情形

示例





## 简介

- C++所谓的完美转发，是指std::forward会将输入的参数原封不动地传递到下一个函数中，这个“原封不动”指的是，如果输入的参数是左值，那么传递给下一个函数的参数的也是左值；如果输入的参数是右值，那么传递给下一个函数的参数的也是右值。



## 完美转发失败情形

五种完美转发失败的情形

　　1. #### 使用大括号初始化列表时

　　（1）失败原因分析：由于转发函数是个模板函数，而在模板类型推导中，大括号初始不能自动被推导为std::initializer_list<T>。

　　（2）解决方案：先用auto声明一个局部变量，再将该局部变量传递给转发函数。

　　2. #### 0和NULL用作空指针时

　　（1）失败原因分析：0或NULL以空指针之名传递给模板时，类型推导的结果是整型，而不是所希望的指针类型。

　　（2）解决方案：传递nullptr，而非0或NULL。

　　3. #### 仅声明static const 整型成员变量，而无其定义时。

　　（1）失败原因分析：C++中常量一般是进入符号表的，只有对其取地址时才会实际分配内存。调用f函数时，其实参是直接从符号表中取值，此时不会发生问题。但当调用fwd时由于其形参是万能引用，而引用本质上是一个可解引用的指针。因此当传入fwd时会要求准备某块内存以供解引用出该变量出来。但因其未定义，也就没有实际的内存空间， 编译时可能失败（取决于编译器和链接器的实现）。

　　（2）解决方案：在类外定义该成员变量。注意这声变量在声明时一般会先给初始值。因此定义时无需也不能再重复指定初始值。

　　4. #### 使用重载函数名或模板函数名时

　　（1）失败原因分析：由于fwd是个模板函数，其形参没有任何关于类型的信息。当传入重载函数名或模板函数（代表许许多多的函数）时，就会导致fwd的形参不知绑定到哪个函数上。

　　（2）解决方案：在调用fwd调用时手动为形参指定类型信息。

　　5. #### 转发位域时

　　（1）失败原因分析：位域是由机器字的若干任意部分组成的（如32位int的第3至5个比特），但这样的实体是无法直接取地址的。而fwd的形参是个引用，本质上就是指针，所以也没有办法创建指向任意比特的指针。

　　（2）解决方案：制作位域值的副本，并以该副本来调用转发函数。





## 示例



### make_shared  源码实现

**make_shared 用来创建一个对象的智能指针，并且可以通过使用不同的参数来构造所创建对象的指针，这个函数实现如下：**

```
template<class _Ty,
	class... _Types>
_NODISCARD inline shared_ptr<_Ty> make_shared(_Types&&... _Args)
{	// make a shared_ptr
	const auto _Rx = new _Ref_count_obj<_Ty>(_STD forward<_Types>(_Args)...);  //参数完美转发

	shared_ptr<_Ty> _Ret;
	_Ret._Set_ptr_rep_and_enable_shared(_Rx->_Getptr(), _Rx);
	return (_Ret);
}
```





### 引用折叠 示例

```cpp
#include <iostream>

using namespace std;

class Widget{};

template<typename T>
void func(T&& param){}

//Widget工厂函数
Widget widgetFactory() 
{
    return Widget();
}

//类型别名
template<typename T>
class Foo
{
public:
    typedef T&& RvalueRefToT;
};

int main()
{
    int x = 0;
    int& rx = x;
    //auto& & r = x; //error，声明“引用的引用”是非法的！

    //1. 引用折叠发生的语境1——模板实例化
    Widget w1;
    func(w1); //w1为左值，T被推导为Widget&。代入得void func(Widget& && param);
              //引用折叠后得void func(Widget& param)

    func(widgetFactory()); //传入右值，T被推导为Widget，代入得void func(Widget&& param)
                           //注意这里没有发生引用的折叠。

    //2. 引用折叠发生的语境2——auto类型推导
    auto&& w2 = w1; //w1为左值auto被推导为Widget&，代入得Widget& && w2，折叠后为Widget& w2
    auto&& w3 = widgetFactory(); //函数返回Widget，为右值，auto被推导为Widget，代入得Widget w3

    //3. 引用折叠发生的语境3——tyedef和using
    Foo<int&> f1;  //T被推导为int&，代入得typedef int& && RvalueRefToT;折叠后为typedef int& RvalueRefToT

    //4. 引用折叠发生的语境3——decltype
    decltype(x)&& var1 = 10;  //由于x为int类型，代入得int&& rx。
    decltype(rx) && var2 = x; //由于rx为int&类型，代入得int& && var2，折叠后得int& var2

    return 0;
}
```

### 不完美转发示例

```
#include <iostream>
using namespace std;

void print(const int& t)  //左值版本
{
    cout <<"void print(const int& t)" << endl;
}

void print(int&& t)     //右值版本
{
    cout << "void print(int&& t)" << endl;
}

template<typename T>
void testForward(T&& param)
{
    //不完美转发
    print(param);            //param为形参，是左值。调用void print(const int& t)
    print(std::move(param)); //转为右值。调用void print(int&& t)

    //完美转发
    print(std::forward<T>(param)); //只有这里才会根据传入param的实参类型的左右值进转发
}

int main()
{
    cout <<"-------------testForward(1)-------------" <<endl;
    testForward(1);    //传入右值

    cout <<"-------------testForward(x)-------------" << endl;
    int x = 0;
    testForward(x);    //传入左值

    return 0;
}
/*输出结果
-------------testForward(1)-------------
void print(const int& t)
void print(int&& t)
void print(int&& t)       //完美转发，这里转入的1为右值，调用右值版本的print
-------------testForward(x)-------------
void print(const int& t)
void print(int&& t)
void print(const int& t) //完美转发，这里转入的x为左值，调用左值版本的print
*/
```



### 使用 std::move、std::forward 优化

```
#include <iostream>
#include <memory>
using namespace std;

//1. 针对右值引用实施std::move，针对万能引用实施std::forward
class Data{};

class Widget
{
    std::string name;
    std::shared_ptr<Data> ptr;
public:
    Widget() { cout <<"Widget()"<<endl; };

    //复制构造函数
    Widget(const Widget& w):name(w.name), ptr(w.ptr)
    {
        cout <<"Widget(const Widget& w)" << endl;
    }
    //针对右值引用使用std::move
    Widget(Widget&& rhs) noexcept: name(std::move(rhs.name)), ptr(std::move(rhs.ptr))
    {
        cout << "Widget(Widget&& rhs)" << endl;
    }

    //针对万能引用使用std::forward。
    //注意，这里使用万能引用来替代两个重载版本：void setName(const string&)和void setName(string&&)
    //好处就是当使用字符串字面量时，万能引用版本的效率更高。如w.setName("SantaClaus")，此时字符串会被
    //推导为const char(&)[11]类型，然后直接转给setName函数（可以避免先通过字量面构造临时string对象）。
    //并将该类型直接转给name的构造函数，节省了一个构造和释放临时对象的开销，效率更高。
    template<typename T>
    void setName(T&& newName)
    {
        if (newName != name) { //第1次使用newName
            name = std::forward<T>(newName); //针对万能引用的最后一次使用实施forward
        }
    }
};

//2. 按值返回函数
//2.1 按值返回的是一个绑定到右值引用的对象
class Complex 
{
    double x;
    double y;
public:
    Complex(double x =0, double y=0):x(x),y(y){}
    Complex& operator+=(const Complex& rhs) 
    {
        x += rhs.x;
        y += rhs.y;
        return *this;
    }
};

Complex operator+(Complex&& lhs, const Complex& rhs) //重载全局operator+
{
    lhs += rhs;
    return std::move(lhs); //由于lhs绑定到一个右值引用，这里可以移动到返回值上。
}

//2.2 按值返回一个绑定到万能引用的对象
template<typename T>
auto test(T&& t)
{
    return std::forward<T>(t); //由于t是一个万能引用对象。按值返回时实施std::forward
                               //如果原对象一是个右值，则被移动到返回值上。如果原对象
                               //是个左值，则会被拷贝到返回值上。
}

//3. RVO优化
//3.1 返回局部对象
Widget makeWidget()
{
    Widget w;

    return w;  //返回局部对象，满足RVO优化两个条件。为避免复制，会直接在返回值内存上创建w对象。
               //但如果改成return std::move(w)时，由于返回值类型不同（Widget右值引用，另一个是Widget）
               //会剥夺RVO优化的机会，就会先创建w局部对象，再移动给返回值，无形中增加一个移动操作。
               //对于这种满足RVO条件的，当某些情况下无法避免复制的（如多路返回），编译器仍会默认地对
               //将w转为右值，即return std::move(w)，而无须用户显式std::move!!!
}

//3.2 按值形参作为返回值
Widget makeWidget(Widget w) //注意，形参w是按值传参的。
{
    //...

    return w; //这里虽然不满足RVO条件（w是形参，不是函数内的局部对象），但仍然会被编译器优化。
              //这里会默认地转换为右值，即return std::move(w)
}

int main()
{
    cout <<"1. 针对右值引用实施std::move，针对万能引用实施std::forward" << endl;
    Widget w;
    w.setName("SantaClaus");

    cout << "2. 按值返回时" << endl;
    auto t1 = test(w); 
    auto t2 = test(std::move(w));

    cout << "3. RVO优化" << endl;
    Widget w1 = makeWidget();   //按值返回局部对象（RVO）
    Widget w2 = makeWidget(w1); //按值返回按值形参对象

    return 0;
}
/*输出结果
1. 针对右值引用实施std::move，针对万能引用实施std::forward
Widget()
2. 按值返回时
Widget(const Widget& w)
Widget(Widget&& rhs)
3. RVO优化
Widget()
Widget(Widget&& rhs)
Widget(const Widget& w)
Widget(Widget&& rhs)
*/
```



### 完美转发失败

```cpp
#include <iostream>
#include <vector>

using namespace std;

//1. 大括号初始化列表
void f(const std::vector<int>& v)
{
    cout << "void f(const std::vector<int> & v)" << endl;
}

//2. 0或NULL用作空指针时
void f(int x)
{
    cout << "void f(int x)" << endl;
}


//3. 仅声明static const的整型成员变量而无定义
class Widget
{
public:
    static const  std::size_t MinVals = 28; //仅声明，无定义（因为静态变量需在类外定义！）
};

//const std::size_t Widget::MinVals; //在类外定义，无须也不能重复指定初始值。

//4. 使用重载函数名或模板函数名
int f(int(*pf)(int))
{
    cout <<"int f(int(*pf)(int))" << endl;
    return 0;
}

int processVal(int value) { return 0; }
int processVal(int value, int priority) { return 0; }

//5.位域
struct IPv4Header
{
    std::uint32_t version : 4,
                  IHL : 4,
                  DSCP : 6,
                  ECN : 2,
                  totalLength : 16;
    //...
};

template<typename T>
T workOnVal(T param)  //函数模板，代表许许多多的函数。
{
    return param;
}

//用于测试的转发函数
template<typename ...Ts>
void fwd(Ts&& ... param)  //转发函数
{
    f(std::forward<Ts>(param)...);  //目标函数
}

int main()
{
    cout <<"-------------------1. 大括号初始化列表---------------------" << endl;    
    //1.1 用同一实参分别调用f和fwd函数
    f({ 1, 2, 3 });  //{1, 2, 3}会被隐式转换为std::vector<int>
    //fwd({ 1, 2, 3 }); //编译失败。由于fwd是个函数模板，而模板推导时{}不能自动被推导为std:;initializer_list<T>
    //1.2 解决方案
    auto il = { 1,2,3 };
    fwd(il);

    cout << "-------------------2. 0或NULL用作空指针-------------------" << endl;
    //2.1 用同一实参分别调用f和fwd函数
    f(NULL);   //调用void f(int)函数，
    fwd(NULL); //NULL被推导为int，仍调用void f(int)函数
    //2.2 解决方案：使用nullptr
    f(nullptr);  //匹配int f(int(*pf)(int))
    fwd(nullptr);

    cout << "-------3. 仅声明static const的整型成员变量而无定义--------" << endl;
    //3.1 用同一实参分别调用f和fwd函数
    f(Widget::MinVals);   //调用void f(int)函数。实参从符号表中取得，编译成功！
    fwd(Widget::MinVals); //fwd的形参是引用，而引用的本质是指针，但fwd使用到该实参时需要解引用
                          //这里会因没有为MinVals分配内存而出现编译失败（取决于编译器和链接器）
    //3.2 解决方案：在类外定义该变量

    cout << "-------------4. 使用重载函数名或模板函数名---------------" << endl;
    //4.1 用同一实参分别调用f和fwd函数
    f(processVal);   //ok，由于f形参为int(*pf)(int)，带有类型信息，会匹配int processVal(int value)
    //fwd(processVal); //error,fwd的形参不带任何类型信息，不知该匹配哪个processVals重载函数。
    //fwd(workOnVal);  //error,workOnVal是个函数模板，代表许许多多的函数。这里不知绑定到哪个函数
    //4.2 解决方案：手动指定类型信息
    using ProcessFuncType = int(*)(int);
    ProcessFuncType processValPtr = processVal;
    fwd(processValPtr);
    fwd(static_cast<ProcessFuncType>(workOnVal));   //调用int f(int(*pf)(int))

    cout << "----------------------5. 转发位域时---------------------" << endl;
    //5.1 用同一实参分别调用f和fwd函数
    IPv4Header ip = {};
    f(ip.totalLength);  //调用void f(int)
    //fwd(ip.totalLength); //error，fwd形参是引用，由于位域是比特位组成。无法创建比特位的引用！
    //解决方案：创建位域的副本，并传给fwd
    auto length = static_cast<std::uint16_t>(ip.totalLength);
    fwd(length);
    
    return 0;
}
/*输出结果
-------------------1. 大括号初始化列表---------------------
void f(const std::vector<int> & v)
void f(const std::vector<int> & v)
-------------------2. 0或NULL用作空指针-------------------
void f(int x)
void f(int x)
int f(int(*pf)(int))
int f(int(*pf)(int))
-------3. 仅声明static const的整型成员变量而无定义--------
void f(int x)
void f(int x)
-------------4. 使用重载函数名或模板函数名---------------
int f(int(*pf)(int))
int f(int(*pf)(int))
int f(int(*pf)(int))
----------------------5. 转发位域时---------------------
void f(int x)
void f(int x)
*/
```



## 相关参考

[完美转发(std::forward)](https://www.cnblogs.com/5iedu/p/11324772.html)

[](https://www.artima.com/articles/a-brief-introduction-to-rvalue-references)

[](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2005/n1861.html)