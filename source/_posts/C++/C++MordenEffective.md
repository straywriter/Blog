---
title: Effective Modern C++ 读书总结
top: false
mathjax: true
categories:
- C++
---

-----



## Item1 Understand template type deduction

  C++的模板对于类型推导来说，在通常情况推导出的类型和我们期望的是一致的，然而事情并不是总是这样如此美妙。例如下面这个例子:

```
template<typename T>
void f(`参数类型` parm);
```


参数类型可以是下面几种情况:

1. T&
2. const T&
3. T&&
4. T

上面的几种情况可以分成三种类型，1和2可以归为引用类型，3是右值引用类型(也可以叫做通用引用类型)，4则是类型本身，没有额外的修饰。对于这三种类型来说模板类型推导有着不一样的规则。   

**Case1: 引用类型**
**类型推导规则如下:**

1. 如果传入的类型是引用类型，那么会忽视掉引用部分，如果是指针类型，则会忽视指针部分
2. 如果传入的类型不是引用类型那么原来是什么类型就推导成什么类型

当参数类型是T&的时候，传入下列三个参数。

```
int x = 27;
const int cx = x;
const int& rx= x;
```

对于上面三个变量的类型传递给模板函数f的时候,参数parm的类型会推导成如下:

```
x  被推导成 int&        //匹配规则2，x被推导成int，因为参数类型是T&，所以就是int&
cx 被推导成 const int&  //匹配规则2
rx 被推导成 const int&  //匹配规则1，被推导成const int&，忽视引用部分，因为参数类型是T&，所以最后就是const int&
```

当参数类型是`const T&`，传入上面三个参数，推导后的类型结果如下:

```
x   被推导成 const int&
cx  被推导成 const int& //const 叠加了
rx  被推导成 const int& //发生了引用折叠
```

当参数类型是`T*`或`const T*`的时候本质上和上面的是相同的。当传入一个指针类型，会导致指针折叠。

> 总结一句话，当参数类型是引用类型，那么parm最终的类型就是引用类型。如果有CV限制符，那么parm也会带上CV限制符，如果传入的类型含有引用会发生引用折叠，不会变成`&&`，如果包含cv限制符，也会进行叠加，不会变成`const const`。

**Case2: 右值引用类型**

类型推导规则如下:

1. 如果传入的是一个左值，那么会被推导成引用类型
2. 如果传入的是右值，就按照Case1的规则推导

当参数类型是`T&&`，传入下列三个参数:

```
int x = 27;
const int cx = x;
const int& rx = x;
```

对于上面三个变量的类型传递给模板函数f的时候,参数`param`的类型会推导成如下:

```
x   被推导成  int&      //匹配规则1，x是左值被推导成引用类型
cx  被推导成 const int& //匹配规则1,cx也是左值，cv限制符会带上。
27  被推导成 int&&      //匹配规则2, 27是个右值，T被推导成int，因为参数类型是T&&，所有被推导成int&&
```

> `param`的类型，取决于传入的变量是否是右值，如果是右值，最终`param`的类型就是`&&`，有CV限制符就带上。如果是左值那么param的最终类型就是引用类型了。

**Case3: 类型本身**


类型推导规则如下:

1. 如果传入的参数是引用类型，忽视引用部分。
2. 如果不是引用类型，但是带有const,volatile等同样也忽视。
3. 不是引用类型，也没有const volatile修饰符，按照正常规则推导

当参数类型是`T`，传入下列三个参数:

```
int x = 27;
const int cx = x;
const int& rx = x;
```

对于上面三个变量的类型传递给模板函数f的时候,参数parm的类型会推导成如下:

```
x   被推导成 int  //匹配规则3
cx  被推导成 int //匹配规则2
rx  被推导成 int //匹配规则1
```

> parm的类型就是传入的类型去掉引用，指针和CV限制符等。

上面的类型推导规则基本上包含了绝大多数模板推导规则，除此之外还有两个类型的推导需要关注。

- 数组类型参数
      

  数组类型和指针指向一个数组这是两个不同的类型，数组如果传递给函数会退化为指针，因此在模板参数类型的时候，如果参数类型是T，那么数组会被推导成指针类型，如果参数类型是T&，那么数组就会被推导成数组类型，可以使用sizeof求数组的大小。

- 函数作为参数
     

  函数作为参数会被退化成函数指针，如果参数类型是T，函数会被推导成一个函数指针，指向这个函数，如果参数类型是T&，那么函数会被推导成一个引用指向函数的引用。

> Tips: 
> 
>
> 1. 在模版类型推到的时候，如果传递的参数是引用类型，那么可以看作是非引用类型的，也就是说类型的引用部分被忽略。 
>
> 2. 当对通用引用类型的参数进行类型推导时，左值参数需要特殊对待。 
>   
> 3. 当推导正常的参数类型时，const和volatile类型的参数会被忽略掉const和volatile部分。 
> 4. 在模版类型推导时，如果参数是数组或是函数名会退化为指针，除非这些参数是用来初始化 引用的。





## Item2 Understand auto type deduction

```
template<typename T>
void f(`参数类型` parm);
```

根据参数类型和传入的**var**的类型的不同，推导的规则也相应不同，那么auto是如何和模板推导规则关联呢?

```
f(var)
```

```
auto x = 27             //对应Item1中的Case3，x的类型就是int
int& z = x;
const int y = 19;
auto cx = x;            //对应Item1中的Case3，cx的类型就是int，忽略了y的CV限制符和引用等
const auto& rx = z;     //对应Item1中的Case1，rx的类型是const引用类型，忽略z的引用
auto&& rrx = 27         //对应Item1中的Case2，rrx的类型int&&

参数类型 param = var
```

auto可以总结为上面这种形式。根据赋值操作右边的var类型，和变量名左边的参数类型结合起来进行推导。在Item1中还提到了两个额外的类型推导，一个是数组类型，另外一个是函数类型。对应到**auto**则如下:

```
const char name[] = "test";
auto arr1 = name;       //arr1的类型是const char*类型
auto& arr2 = name;      //arr2的类型是const char(&)[5]

void someFunc(int,doubel);
auto func1 = someFunc;  //void(*)(int,double)
auto& func2 = someFunc; //void(&)(int,double)
```

 除了上面提到的推导规则外，**auto**还有一个和模板类型推导不一样的地方。这也是本节Item需要关注的。在**C++11**中引入了统一初始化列表，相对应的则是`std::initializer_list<T>`模板类型。

```
auto  x1 = {1,2,3}; //此时x1被推导为std::initializer_list<T>
```

因为初始化列表是模板类型，因此所有的元素必须是同一类型，否则会初始化失败。对应到模板类型推导如下:

```
auto x = {11,23,9}
template<typename T>
void f(T param);

f(x);   //编译错误，无法进行推导。
```

模板类型推导居然无法识别初始化列表，只能写成下面这种形式才可以进行推导。

```
template<typename T>
void f(std::initializer_list<T> param);
```

 至于为什们，其实我也不清楚，也没有找到相关的资料，就只能当做一个规则记着吧。但是auto也不是那么完美，如果`auto`用于推导函数的返回类型，auto是不能推导初始化列表的。

```
auto createInitList() {     //C++14支持这种写法，C++11中需要结合decltype
    return { 1,2 ,3 };
}
```

除了不能作为函数返回值外，还不能作为lambda的参数，注意是lambda的参数，普通函数的参数是可以的，C++11中不支持lambda的参数使用auto，C++14开始支持。

```
auto lda = [](const auto& v) {};
lda({1,2,3});   //编译出错，无法推导初始化列表。
```

> Tips: 
> \
>
> 1. auto类型推导通常和模版类型推导是一致的，但是auto类型推导对于{}会推导为std::initializer_list，但是模版类型无法对其进行推导 
>
> 2. auto对于函数返回值的类型推导和lambda参数类型推导时就是隐式的模版类型推导，并不是auto类型推导，对于{}无法进行推导



## Item3 Understand decltype

`decltype`是用来推导变量的类型，但是不像auto和模板类型推导那样存在很多类型推导规则，`decltype`推导出来的类型和变量原来的类型一模一样，没有做任何改动。在C++11中`decltype`结合`auto`还可以完成函数返回值的类型推导。

```
template<typename Container,typename Index>
auto AccessContainer(Container& c,Index i) -> decltype(c[i]) {
    return c[i];
}
```

到了C++14的时候就可以省略掉后面的`-> decltype(c[i])`了，变成下面的样子。

```
template<typename Container,typename Index>
auto AccessContainer(Container& c,Index i) {
    return c[i];
}
```

上面的这个例子是用来访问容器中某个位置的元素,也就是容器的`operator[]`操作符返回的元素，按照容器的定义这个操作符应该返回的是一个引用，也就是说你可以像下面这样给某个位置赋值。

```
std::vector<int> d;
AccessContainer(d,5) = 100;
```

但是很不幸，你会发现编译出错，原因则是罪魁祸首的`auto`，返回值遇到auto后，引用被忽略掉了，在Item1中的`Case3`中详细解释了这个规则，为了让上面的的例子可以正常运行，可以做如下改动。

```
template<typename Container,typename Index>
auto& AccessContainer(Container& c,Index i) {
    return c[i];
}
```

返回的是一个`auto&`，至于为什么可以参考Item1中的Case1，除此之外还可以用C++14的另外一种写法如下:

```
template<typename Container,typename Index>
decltype(auto) AccessContainer(Container& c,Index i) {
    return c[i];
}
```

通过`decltype`保证返回变量的本来类型这一特性，保证不丢失`CV`限制符，和引用等，因此在C++14中可以通过`decltype`和`auto`来声明变量，保证变量的类型和赋值的类型一模一样。

```
int ia = 10;
const int& iia = ia;
auto autoia = cw;             //推导出的类型是int，引用和CV限制符都会忽略
decltype(auto) deautoia = cw; //const int& 保证和cw的类型一模一样 
```

上面的方案通过`decltype`和`auto`让返回值的类型变的完美，但是如果用户传入一个const的容器，将会导致编译出错。因为`AccessContainer`的参数类型是非常量引用，为了让他可以接收常量和非常量，需要使用常量引用。

```
template<typename Container,typename Index>
decltype(auto) AccessContainer(const Container& c,Index i) {
    return c[i];
}
```

这带来的另外一个问题就是，`c[i]`，返回的是常量引用，无法修改。好在C++11中引入了右值引用，它还有另外一个名字叫做通用引用，通过名字就可以知道这个引用很通用，它可以接收左值，右值还有带const的。

```
template<typename Container,typename Index>
decltype(auto) AccessContainer(Container&& c,Index i) {
    return c[i];
}
```

到此为止看似已经很完美了，可以接收任何类型的容器，返回值也和传入的类型一致，但是上述方案仍然有不足之处如果用户传入的是一个右值，通过移动语义传递给了AccessContainer的参数c，但是c本身其实是一个左值，如果在AccessContainer中需要把c再次传递给其他的函数的话就不能再次利用右值的移动语义了，带来了不必要的拷贝开销。C++11中的完美转发使得上面的方案变得完美，它可以将参数原封不动的传递给其他的函数。

```
template<typename Container,typename Index>
decltype(auto) AccessContainer(Container&& c,Index i) {
    return std::forward<Container>(c)[i];
}
```

到此为止实现了一个完美的AccessContainer，关于完美转发可以参考这篇文章C++0x里的完美转发到底是神马？decltype如此完美，从文章的开篇我就一直强调decltype的好，它可以完美的得到目标变量的类型，但是在大多数人的眼中C++总是那么的不完美，decltype会打破这个定律吗? 当然没有，decltype也有例外的时候。

```
int x = 0;
decltype(x)     //得到的是int类型
decltype((x))   //得到的是int&类型
```

上面的变量x加了一个括号后就变成了一个引用类型了，根据官方如下:

```
decltype ( entity ) (1) (since C++11)
decltype ( expression ) (2) (since C++11)

1) If the argument is an unparenthesized id-expression or an unparenthesized class member access expression, then decltype yields the type of the entity named by this expression. If there is no such entity, or if the argument names a set of overloaded functions, the program is ill-formed.
2) If the argument is any other expression of type T, and
a) if the value category of expression is xvalue, then decltype yields T&&;
b) if the value category of expression is lvalue, then decltype yields T&;
c) if the value category of expression is prvalue, then decltype yields T.


[ Example:
const int&& foo();
int i;
struct A { double x; };
const A* a = new A();
decltype(foo()) x1 = i;     // type is const int&&
decltype(i) x2;             // type is int
decltype(a->x) x3;          // type is double
decltype((a->x)) x4 = x3;   // type is const double&
—end example ]
```

对于x来说，没有括号括起来，只是一个带有名字的变量，这是id-expression，它的类型是就是这个表达式的entity类型，所以符号第一条规则，类型就是x本身，而(x)不符号第一条，也不符合第二条，因为他是lvalue expression所以符合第三条，就是int&。



## Item4 Know how to view deduced types

在Item3中学习了C++11新特性decltype，decltype可以获取变量或者表达式的类型，但是获取到的类型只能用于定义其他的变量和类型，不能打印出来，也不能用来操作。毕竟是编译期实现，用来做类型反射就算了，那么至少也应该可以打印输出下吧，毕竟书中得来终觉醒。那么本文就介绍几种方法来得到decltype的返回类型的名字。

```
template<typename T>
class TD

TD<decltype(x)> xType;
```

```
错误：聚合‘TD<int> xType’类型不完全，无法被定义
   TD<decltype(x)> xType;
```

得到decltype的返回类型的名字的方法：

- IDE Editors

  最简单的就是依靠C++的IDE帮你识别出`decltype`的返回类型，IDE毕竟不是万能的，所以你要识别的类型要尽可能的简单，不能过于复杂。

- Compiler Diagnostic
  借助于编译器的诊断错误信息。

  通过错误使用`decltype`推导出来的类型让编译器报出编译错误，在编译错误的信息中可以发现`decltype`推导出来的类型名称。例如下面的这个例子:

  ```
  template<typename T>
  class TD
  
  TD<decltype(x)> xType;
  ```

  使用g++编译后，会出现编译出错，诊断信息如下:

  ```
  错误：聚合‘TD<int> xType’类型不完全，无法被定义
     TD<decltype(x)> xType;
  ```

  从上面的诊断信息就可以得出`decltype(x)`的结果就是`int`。

- Runtime Output

  ```
  #include <typeinfo>
  int x = 0;
  std::cout << typeid(decltype(x)).name() << std::endl;
  ```

  上面会输出x的类型的名称，这里应该会输出`int`，但也不尽然，`typeid`的输出结果取决于编译器，MSVC的输出是`int`，而`g++`的输出则是`i`，也就是c++对int的名称重写后的结果。g++其实也可以实现和MSVC的输出结果一样，

到此为止typeinfo看似解决了问题，其实不然，通过typeinfo得到的类型会忽略cv限制符还有引用，真的是差强人意啊。但是对const的指针类型是不会忽略const限制符的。具体可以参考typeid获取完整类型幸好可以借助于boost的Boost.Type-Index库得到精确的类型。

```
template<typename T>
void f(const T& param) {
  using std::cout;
  using boost::typeindex::type_id_with_cvr;

  cout << "param = "
       << type_id_with_cvr<T>().pretty_name()
       << '\n';

  cout << "param = "
       << type_id_with_cvr<decltype(param)>().pretty_name()
       << '\n';
}
```

## Item5 Prefer auto to explicit type declarations



```
vector<int>::iterator it = xxx.begin();
vector<map<int,int> >::iterator
```

到了C++11，算是对上面碰到的问题有了一个比较折中的解决方案了，如下:

```
auto x1;    //没有初始化会报错
auto it = xxx.begin();  //没有冗长的类型名了
```





-----

**auto类型和闭包**



```
  auto f1 = [](const std::unique_ptr<int>& p1,const std::unique_ptr<int>& p2) {
    return *p1 < *p2;
  };

  std::function<bool(const std::unique_ptr<int>&,std::unique_ptr<int>&)> f2 = [](
    const std::unique_ptr<int>& p1,const std::unique_ptr<int>& p2) {
    return *p1 < *p2;
  };
```





## Item6 Use the explicitly typed initializer idiom when auto deduces undesied types

auto 和std::vector<bool>



## Item7 Distinguish between () and {} when creating objects









Tips 
\1. {}初始化是最广泛的初始化语法，它可以阻止窄化转换，并且避免了C++最复杂的语法解析 
\2. 在构造函数做函数重载的时候，{}会优先匹配带有`std::initializer_list`参数的版本，即使其他构造函数看起来更匹配 
\3. 对与std::vector两个参数的构造函数来说，其{}和()两种初始化方式有很大的不同 
\4. 在模版中对于{}和()初始化如何进行选择是一个挑战

## Item8 Prefer nullptr to 0 and NULL



Tips 
\1. 优先使用nullptr替换0和NULL 
\2. 避免同时重载带有整型参数和指针类型的参数

## Item9 Prefer alias declarations to typedefs



Tips 
\1. typedef 不支持模版化，但是using的别名声明可以 
\2. 模版别名避免了传统的typedef带来的`::type`后缀，以及在类型引用的时候需要的typename前缀 
\3. C++14给所有的C++11模版类型萃取提供了别名



## Item10 Prefer scoped enums to unscoped enums





Tips 
\1. C++98种的枚举众所周知是无作用域限制的 
\2. C++11中的枚举类是有作用域限制的，不能进行隐式的类型转换需要使用C++的类型cast进行转换 
\3. 无论是枚举类还是传统的枚举类型都支持指定底层的存储，对于枚举类来说默认的底层存储类型是int，而传统的枚举类型其底层存储是未知的，需要在编译器进行选择 
\4. 枚举类总是可以进行前向声明的，而枚举类型则不行，必须是在明确指定其底层存储的时候才能进行前向声明



## Item11 Prefer deleted functions to private undefined ones





Tips 
\1. 优先使用delete来删除函数替换放在私有作用域中未定义的 
\2. 任何函数都可以被删除，包括非成员函数，模版实例化等





## Item12 Declare overriding function override







Tips 
\1. 对于要重写的函数添加override关键字，让编译器负责检查 
\2. 成员函数的引用标识符可以识别出(*this)的不同，是左值类型，还是右值类型



## Item13 Prefer const_iterators to iterators









## 相关参考

[](https://blog.csdn.net/oncealong/article/details/83344119)