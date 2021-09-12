---
title: obscure（晦涩的）C++功能
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- C++
---

-----



#  

方括号访问

备用运算符

预处理重定义关键字

placement new

变量声明分支









## 方括号访问

通过`ptr[3]`来访问数组的元素实际上只是的缩写`*(ptr + 3)`。可以等效地用`*(3 + ptr)`和编写`3[ptr]`，这证明是完全有效的代码。



## 最烦人的解析

“最令人烦恼的分析”是Scott Meyers创造的一个术语，表示C ++声明语法中的歧义会导致违反直觉的行为：

```
// Is this:
// 1) A variable of type std::string initialized to a std::string()?
// 2) The declaration of a function that returns a std::string and has one argument,
//    which is a pointer to a function with no arguments that returns a std::string?
std::string foo(std::string());

// Is this:
// 1) A variable of type int initialized to int(x)?
// 2) The declaration of a function that returns an int and has one argument,
//    which is an int named x?
int bar(int(x));
```

C ++标准在两种情况下都需要第二种解释，即使第一种解释是直观的。程序员可以通过将变量的初始值括在括号中来消除歧义：

```
// Parentheses resolve the ambiguity
std::string foo((std::string()));
int bar((int(x)));
```



在第二种情况下产生歧义的原因`int y = 3;`是`int(y) = 3;`。

### 备用运算符

令牌 `and`， `and_eq`， `bitand`， `bitor`， `compl`， `not`， `not_eq`， `or`， `or_eq`， `xor`， `xor_eq`， `<%`， `%>`， `<:`，和 `:>` 可以用来代替符号 `&&`， `&=`， `&`， `|`， `~`， `!`， `!=`， `||`， `|=`， `^`， `^=`， `{`， `}`， `[`，和 `]`。它们使您可以在缺少必要符号的键盘上键入操作符。

### 重新定义关键字

通过预处理器重新定义关键字在技术上应该会导致错误，但实际上工具允许这样做。这使您可以很有趣地引入诸如`#define true false`或`#define else`。但是，有时它是合法有用的。例如，如果您使用的是大型库，则需要绕过C ++访问保护机制，而不是修补库，您可以在添加库头之前关闭访问保护，而无需添加库头。请记住，之后再重新开启保护！

```
#define  class  struct 
#define  private  public 
#define  protected  public 

#include  “ library.h” 

#undef  class 
#undef  private 
#undef  protected
```

请注意，这可能不一定有效，具体取决于您的编译器。C ++仅要求实例变量在未由访问说明符分隔的情况下顺序排列，因此编译器可以通过对访问说明符组重新排序来自由更改内存布局。例如，允许编译器移动所有私有成员，以便它们追随所有公共成员。另一个潜在的问题是名称处理。Microsoft的C ++编译器将访问说明符纳入其名称处理方案，因此更改访问说明符将破坏与现有已编译代码的兼容性。



### placement new

new放置是`new`操作员的另一种语法，该语法在已分配的对象上就位运行，假定该对象具有正确的大小并具有正确的对齐方式。这涉及设置vtable并调用构造函数。

```
#include <iostream>
using namespace std;

struct Test {
  int data;
  Test() { cout << "Test::Test()" << endl; }
  ~Test() { cout << "Test::~Test()" << endl; }
};

int main() {
  // Must allocate our own memory
  Test *ptr = (Test *)malloc(sizeof(Test));

  // Use placement new
  new (ptr) Test;

  // Must call the destructor ourselves
  ptr->~Test();

  // Must release the memory ourselves
  free(ptr);

  return 0;
}
```

为性能关键型方案编写自定义分配器时，将使用newplacement。例如，平板分配器从一个大的内存块开始，并使用new放置在块中顺序分配对象。这样可以避免内存碎片和malloc引起的堆遍历的开销。



### 变量声明分支

C ++包含一个语法简写，用于同时声明一个变量并分支其值。它看起来像一个变量声明，并且可以在`if`or`while`语句的条件下进行。

```
struct Event { virtual ~Event() {} };
struct MouseEvent : Event { int x, y; };
struct KeyboardEvent : Event { int key; };

void log(Event *event) {
  if (MouseEvent *mouse = dynamic_cast<MouseEvent *>(event))
    std::cout << "MouseEvent " << mouse->x << " " << mouse->y << std::endl;

  else if (KeyboardEvent *keyboard = dynamic_cast<KeyboardEvent *>(event))
    std::cout << "KeyboardEvent " << keyboard->key << std::endl;

  else
    std::cout << "Event" << std::endl;
}
```

### 成员函数上的引用限定符

C ++ 11允许成员函数在将用于`this`使用ref限定符的对象的值类型上重载。ref限定符与cv限定符位于相同的位置，并根据对象`this`是左值还是右值来影响重载分辨率：

```
#include <iostream>

struct Foo {
  void foo() & { std::cout << "lvalue" << std::endl; }
  void foo() && { std::cout << "rvalue" << std::endl; }
};

int main() {
  Foo foo;
  foo.foo(); // Prints "lvalue"
  Foo().foo(); // Prints "rvalue"
  return 0;
}
```



### 图灵完整模板元编程

C ++模板用于编译时元编程，这意味着可以生成其他程序的程序。模板系统是为简单的类型替换而设计的，但是在C ++标准化过程中偶然发现，模板实际上足够强大，可以执行任意计算，尽管非常笨拙且效率低下。计算是通过模板专门化完成的：



```
// Recursive template for general case
template <int N>
struct factorial {
  enum { value = N * factorial<N - 1>::value };
};

// Template specialization for base case
template <>
struct factorial<0> {
  enum { value = 1 };
};

enum { result = factorial<5>::value }; // 5 * 4 * 3 * 2 * 1 == 120
```



可以将C ++模板视为一种功能编程语言，因为它们使用递归而不是迭代，并且不包含任何可变状态。您可以创建一个包含类型via`typedef`的变量和一个包含`int`via的变量`enum`。数据结构本身嵌入在类型中：

```
// Compile-time list of integers
template <int D, typename N>
struct node {
  enum { data = D };
  typedef N next;
};
struct end {};

// Compile-time sum function
template <typename L>
struct sum {
  enum { value = L::data + sum<typename L::next>::value };
};
template <>
struct sum<end> {
  enum { value = 0 };
};

// Data structures are embedded in types
typedef node<1, node<2, node<3, end> > > list123;
enum { total = sum<list123>::value }; // 1 + 2 + 3 == 6
```



尽管这些示例毫无用处，但是模板元编程可以实现一些有用的功能，例如能够操纵类型列表。但是，由C ++模板形成的编程语言具有可怕的可用性，因此请尽量少用。由于难以理解的冗长和隐秘的编译器错误消息，模板代码难以阅读，编译缓慢且非常难以调试。



### 指针成员运算符

指向成员的指针运算符使您可以在类的任何实例上描述指向特定成员的指针。有两个指向成员的指针运算符，分别`.*`用于值和`->*`指针：

```
#include <iostream>
using namespace std;

struct Test {
  int num;
  void func() {}
};

// Notice the extra "Test::" in the pointer type
int Test::*ptr_num = &Test::num;
void (Test::*ptr_func)() = &Test::func;

int main() {
  Test t;
  Test *pt = new Test;

  // Call the stored member function
  (t.*ptr_func)();
  (pt->*ptr_func)();

  // Set the variable in the stored member slot
  t.*ptr_num = 1;
  pt->*ptr_num = 2;

  delete pt;
  return 0;
}
```



此功能实际上非常有用，特别是对于编写库。例如，[Boost :: Python](http://www.boost.org/libs/python/)（用于将C ++绑定到Python对象的库）在包装对象时使用成员指针轻松引用成员：

```
#include <iostream>
#include <boost/python.hpp>
using namespace boost::python;

struct World {
  std::string msg;
  void greet() { std::cout << msg << std::endl; }
};

BOOST_PYTHON_MODULE(hello) {
  class_<World>("World")
    .def_readwrite("msg", &World::msg)
    .def("greet", &World::greet);
}
```



使用成员函数指针时，请记住它们不同于常规函数指针。成员函数指针和常规函数指针之间的转换将不起作用。例如，Microsoft编译器中的成员函数使用称为thiscall的优化调用约定，该调用约定将`this`参数放入`ecx`寄存器中，而普通函数使用在堆栈上传递所有参数的调用约定。

同样，成员函数指针的大小可能比常规指针大四倍。编译器可能需要存储函数体的地址，正确基数的偏移量（多重继承），vtable中另一个偏移量的索引（虚拟继承），甚至对象本身内部vtable的偏移量（对于向前声明的类型）。

```
#include <iostream>

struct A {};
struct B : virtual A {};
struct C {};
struct D : A, C {};
struct E;

int main() {
  std::cout << sizeof(void (A::*)()) << std::endl;
  std::cout << sizeof(void (B::*)()) << std::endl;
  std::cout << sizeof(void (D::*)()) << std::endl;
  std::cout << sizeof(void (E::*)()) << std::endl;
  return ​0;
}

// 32-bit Visual C++ 2008:  A = 4, B = 8, D = 12, E = 16
// 32-bit GCC 4.2.1:        A = 8, B = 8, D = 8,  E = 8
// 32-bit Digital Mars C++: A = 4, B = 4, D = 4,  E = 4
```

Digital Mars编译器中的所有成员函数指针都具有相同的大小，这是由于其巧妙的设计会生成“ thunk”函数来应用正确的偏移量，而不是将偏移量存储在指针本身中。



### 实例上的静态方法

除了从类型中调用静态方法外，C ++还允许您从实例调用静态方法。这使您可以将实例方法更改为静态方法，而无需更新任何调用站点。



```
struct Foo {
  static void foo() {}
};

// These are equivalent
Foo::foo();
Foo().foo();
```



### Overloading ++ and --

C ++的设计使自定义运算符的功能名称就是运算符本身，在大多数情况下都可以正常工作。例如，一元`-`和二元`-`运算符（取反和减法）可以通过参数计数来区分。这对于一元递增和递减运算符不起作用，因为它们似乎都需要完全相同的签名。C ++语言有一个丑陋的办法可以解决此问题：后缀`++`和`--`运算符必须将虚拟`int`参数作为标志，编译器才能知道要使用后缀运算符（是的，只有该类型`int`有效）。

```
struct Number {
  Number &operator ++ (); // Generate a prefix ++ operator
  Number operator ++ (int); // Generate a postfix ++ operator
};
```



### 运算符重载和评估顺序

重载`,`（逗号）`||`或`&&`操作符非常令人困惑，因为它破坏了正常的评估规则。通常，逗号运算符保证在左侧开始评估之前将对整个左侧进行评估，并且`||`和`&&`运算符具有短路行为，仅在必要时才评估右侧。但是，这些运算符的重载版本只是函数调用，函数调用以未指定的顺序评估其参数。

重载这些运算符只是滥用C ++语法的一种方式。举例来说，我为您提供了不需要括号的Python样式的print语句的C ++实现：

```
#include <iostream>

namespace __hidden__ {
  struct print {
    bool space;
    print() : space(false) {}
    ~print() { std::cout << std::endl; }

    template <typename T>
    print &operator , (const T &t) {
      if (space) std::cout << ' ';
      else space = true;
      std::cout << t;
      return *this;
    }
  };
}

#define print __hidden__::print(),

int main() {
  int a = 1, b = 2;
  print "this is a test";
  print "the sum of", a, "and", b, "is", a + b;
  return 0;
}
```



### 用作模板参数

众所周知，模板参数可以是特定的整数，但也可以是特定的函数。这使编译器可以在实例化的模板代码中内联调用该特定函数，以提高执行效率。在下面的示例中，该函数`memoize`将一个函数用作模板参数，并且仅针对新的参数值调用该函数（从高速缓存中记住旧的参数值）。

```
#include <map>

template <int (*f)(int)>
int memoize(int x) {
  static std::map<int, int> cache;
  std::map<int, int>::iterator y = cache.find(x);
  if (y != cache.end()) return y->second;
  return cache[x] = f(x);
}

int fib(int n) {
  if (n < 2) return n;
  return memoize<fib>(n - 1) + memoize<fib>(n - 2);
}
```



### 模板模板参数

模板参数实际上可以本身具有模板参数。这样，您可以在实例化模板时传递没有模板参数的模板化类型。假设我们有以下代码：

```
template <typename T>
struct Cache { ... };

template <typename T>
struct NetworkStore { ... };

template <typename T>
struct MemoryStore { ... };

template <typename Store, typename T>
struct CachedStore {
  Store store;
  Cache<T> cache;
};

CachedStore<NetworkStore<int>, int> a;
CachedStore<MemoryStore<int>, int> b;
```

CachedStore将保存某种数据类型的缓存放在存储相同数据类型的存储区的前面。但是，我们必须`int`在实例化CachedStore时重复数据类型（在上面的代码中），一次用于商店本身，一次用于CachedStore，并且不能保证数据类型是一致的。我们确实只想指定一次数据类型，以便我们可以强制执行此不变式，但是如果忽略类型参数列表，则会导致编译错误：

```
// These do not compile because NetworkStore and MemoryStore are missing type parameters
CachedStore<NetworkStore, int> c;
CachedStore<MemoryStore, int> d;
```

模板模板参数让我们获得所需的语法。请注意，您需要对`class`本身具有模板参数的模板参数使用关键字。

```
template <template <typename> class Store, typename T>
struct CachedStore2 {
  Store<T> store;
  Cache<T> cache;
};

CachedStore2<NetworkStore, int> e;
CachedStore2<MemoryStore, int> f;
```



### Function try blocks

存在函数try块来捕获在评估构造函数的初始化器列表时抛出的错误。您不能在初始化列表周围包装普通的try-catch块，因为它存在于函数体之外。为了解决这个问题，C ++允许使用try-catch块作为函数的主体：

```
int f() { throw 0; }

// Here there is no way to catch the error thrown by f()
struct A {
  int a;
  A::A() : a(f()) {}
};

// The value thrown from f() can be caught if a try-catch block is used as
// the function body and the initializer list is moved after the try keyword
struct B {
  int b;
  B::B() try : b(f()) {
  } catch(int e) {
  }
};
```

奇怪的是，这种语法不仅限于构造函数，而且可用于所有函数定义。

## 相关参考

### [Obscure C++ Features](http://madebyevan.com/obscure-cpp-features/)

