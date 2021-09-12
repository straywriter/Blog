---
title: C++ 标准库源代码剖析 Type Traits
top: false
mathjax: true
date: 2021-03-22 21:59:11
categories:
- C++
---

-----

## 简介

**Trait**在英语中特指*a particular quality of your personality*，大致相当于汉语中的**逼格**的意思。那C++的**逼格又是什么呢？C++之父[Bjarne Stroustrup](https://link.jianshu.com/?t=http%3A%2F%2Fwww.stroustrup.com%2Findex.html) 对此的解释是：

> Think of a trait as a small object whose main purpose is to carry information used by another object or algorithm to determine *policy* or *implementation* details.

翻译成人话就是：

> *trait* 是一个小型对象，它的主要目的就是携带信息，而这些信息会被其它的对象或算法使用，用来决定某个 *policy* 或*implementation* 的细节

Trait*在标准库中大量运用，比如我们熟悉的C++ 98 STL中的*iterator trait*、*char trait*等。C++ 11又增加了*type trait。



## C++ 11 Type Traits



| 基础类型分类                                                 |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [is_void](https://zh.cppreference.com/w/cpp/types/is_void)(C++11) | 检查类型是否为 void (类模板)                                 |
| [is_null_pointer](https://zh.cppreference.com/w/cpp/types/is_null_pointer)(C++14) | 检查类型是否为 [std::nullptr_t](http://zh.cppreference.com/w/cpp/types/nullptr_t) (类模板) |
| [is_integral](https://zh.cppreference.com/w/cpp/types/is_integral)(C++11) | 检查类型是否为整型 (类模板)                                  |
| [is_floating_point](https://zh.cppreference.com/w/cpp/types/is_floating_point)(C++11) | 检查类型是否是浮点类型 (类模板)                              |
| [is_array](https://zh.cppreference.com/w/cpp/types/is_array)(C++11) | 检查类型是否是数组类型 (类模板)                              |
| [is_enum](https://zh.cppreference.com/w/cpp/types/is_enum)(C++11) | 检查类型是否是枚举类型 (类模板)                              |
| [is_union](https://zh.cppreference.com/w/cpp/types/is_union)(C++11) | 检查类型是否为联合体类型 (类模板)                            |
| [is_class](https://zh.cppreference.com/w/cpp/types/is_class)(C++11) | 检查类型是否非联合类类型 (类模板)                            |
| [is_function](https://zh.cppreference.com/w/cpp/types/is_function)(C++11) | 检查是否为函数类型 (类模板)                                  |
| [is_pointer](https://zh.cppreference.com/w/cpp/types/is_pointer)(C++11) | 检查类型是否为指针类型 (类模板)                              |
| [is_lvalue_reference](https://zh.cppreference.com/w/cpp/types/is_lvalue_reference)(C++11) | 检查类型是否为*左值引用* (类模板)                            |
| [is_rvalue_reference](https://zh.cppreference.com/w/cpp/types/is_rvalue_reference)(C++11) | 检查类型是否为*右值引用* (类模板)                            |
| [is_member_object_pointer](https://zh.cppreference.com/w/cpp/types/is_member_object_pointer)(C++11) | 检查类型是否为指向非静态成员对象的指针 (类模板)              |
| [is_member_function_pointer](https://zh.cppreference.com/w/cpp/types/is_member_function_pointer)(C++11) | 检查类型是否为指向非静态成员函数的指针 (类模板)              |
| 复合类型分类                                                 |                                                              |
| [is_fundamental](https://zh.cppreference.com/w/cpp/types/is_fundamental)(C++11) | 检查是否是基础类型 (类模板)                                  |
| [is_arithmetic](https://zh.cppreference.com/w/cpp/types/is_arithmetic)(C++11) | 检查类型是否为算术类型 (类模板)                              |
| [is_scalar](https://zh.cppreference.com/w/cpp/types/is_scalar)(C++11) | 检查类型是否为标量类型 (类模板)                              |
| [is_object](https://zh.cppreference.com/w/cpp/types/is_object)(C++11) | 检查是否是对象类型 (类模板)                                  |
| [is_compound](https://zh.cppreference.com/w/cpp/types/is_compound)(C++11) | 检查是否为复合类型 (类模板)                                  |
| [is_reference](https://zh.cppreference.com/w/cpp/types/is_reference)(C++11) | 检查类型是否为*左值引用*或*右值引用* (类模板)                |
| [is_member_pointer](https://zh.cppreference.com/w/cpp/types/is_member_pointer)(C++11) | 检查类型是否为指向非静态成员函数或对象的指针类型 (类模板)    |
| 类型的性质                                                   |                                                              |
| [is_const](https://zh.cppreference.com/w/cpp/types/is_const)(C++11) | 检查类型是否为 const 限定 (类模板)                           |
| [is_volatile](https://zh.cppreference.com/w/cpp/types/is_volatile)(C++11) | 检查类型是否为 volatile 限定 (类模板)                        |
| [is_trivial](https://zh.cppreference.com/w/cpp/types/is_trivial)(C++11) | 检查类型是否平凡 (类模板)                                    |
| [is_trivially_copyable](https://zh.cppreference.com/w/cpp/types/is_trivially_copyable)(C++11) | 检查类型是否可平凡复制 (类模板)                              |
| [is_standard_layout](https://zh.cppreference.com/w/cpp/types/is_standard_layout)(C++11) | 检查是否是一个标准布局类型 (类模板)                          |
| [is_pod](https://zh.cppreference.com/w/cpp/types/is_pod)(C++11)(C++20 中弃用) | 检查类型是否为简旧数据（POD）类型 (类模板)                   |
| [is_literal_type](https://zh.cppreference.com/w/cpp/types/is_literal_type)(C++11)(C++17 中弃用)(C++20 中移除) | 检查类型是否为字面类型 (类模板)                              |
| [has_unique_object_representations](https://zh.cppreference.com/w/cpp/types/has_unique_object_representations)(C++17) | 检查是否该类型对象的每一位都对其值有贡献 (类模板)            |
| [is_empty](https://zh.cppreference.com/w/cpp/types/is_empty)(C++11) | 检查类型是否为类（但非联合体）类型且无非静态数据成员 (类模板) |
| [is_polymorphic](https://zh.cppreference.com/w/cpp/types/is_polymorphic)(C++11) | 检查类型是否为多态类类型 (类模板)                            |
| [is_abstract](https://zh.cppreference.com/w/cpp/types/is_abstract)(C++11) | 检查类型是否为抽象类类型 (类模板)                            |
| [is_final](https://zh.cppreference.com/w/cpp/types/is_final)(C++14) | 检查类型是否为 `final` 类类型 (类模板)                       |
| [is_aggregate](https://zh.cppreference.com/w/cpp/types/is_aggregate)(C++17) | 检查类型是否聚合类型 (类模板)                                |
| [is_signed](https://zh.cppreference.com/w/cpp/types/is_signed)(C++11) | 检查类型是否为有符号算术类型 (类模板)                        |
| [is_unsigned](https://zh.cppreference.com/w/cpp/types/is_unsigned)(C++11) | 检查类型是否为无符号算术类型 (类模板)                        |
| [is_bounded_array](https://zh.cppreference.com/w/cpp/types/is_bounded_array)(C++20) | 检查类型是否为有已知边界的数组类型 (类模板)                  |
| [is_unbounded_array](https://zh.cppreference.com/w/cpp/types/is_unbounded_array)(C++20) | 检查类型是否为有未知边界的数组类型 (类模板)                  |
| [is_scoped_enum](https://zh.cppreference.com/w/cpp/types/is_scoped_enum)(C++23) | 检查类型是否为有作用域枚举类型 (类模板)                      |







| 受支持操作                                                   |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [is_constructibleis_trivially_constructibleis_nothrow_constructible](https://zh.cppreference.com/w/cpp/types/is_constructible)(C++11)(C++11)(C++11) | 检查类型是否带有针对特定实参的构造函数 (类模板)              |
| [is_default_constructibleis_trivially_default_constructibleis_nothrow_default_constructible](https://zh.cppreference.com/w/cpp/types/is_default_constructible)(C++11)(C++11)(C++11) | 检查类型是否有默认构造函数 (类模板)                          |
| [is_copy_constructibleis_trivially_copy_constructibleis_nothrow_copy_constructible](https://zh.cppreference.com/w/cpp/types/is_copy_constructible)(C++11)(C++11)(C++11) | 检查类型是否拥有复制构造函数 (类模板)                        |
| [is_move_constructibleis_trivially_move_constructibleis_nothrow_move_constructible](https://zh.cppreference.com/w/cpp/types/is_move_constructible)(C++11)(C++11)(C++11) | 检查类型是否能从右值引用构造 (类模板)                        |
| [is_assignableis_trivially_assignableis_nothrow_assignable](https://zh.cppreference.com/w/cpp/types/is_assignable)(C++11)(C++11)(C++11) | 检查类型是否拥有针对特定实参的赋值运算符 (类模板)            |
| [is_copy_assignableis_trivially_copy_assignableis_nothrow_copy_assignable](https://zh.cppreference.com/w/cpp/types/is_copy_assignable)(C++11)(C++11)(C++11) | 检查类型是否拥有复制赋值运算符 (类模板)                      |
| [is_move_assignableis_trivially_move_assignableis_nothrow_move_assignable](https://zh.cppreference.com/w/cpp/types/is_move_assignable)(C++11)(C++11)(C++11) | 检查类型是否有拥有移动赋值运算符 (类模板)                    |
| [is_destructibleis_trivially_destructibleis_nothrow_destructible](https://zh.cppreference.com/w/cpp/types/is_destructible)(C++11)(C++11)(C++11) | 检查类型是否拥有未被弃置的析构函数 (类模板)                  |
| [has_virtual_destructor](https://zh.cppreference.com/w/cpp/types/has_virtual_destructor)(C++11) | 检查类型是否拥有虚析构函数 (类模板)                          |
| [is_swappable_withis_swappableis_nothrow_swappable_withis_nothrow_swappable](https://zh.cppreference.com/w/cpp/types/is_swappable)(C++17)(C++17)(C++17)(C++17) | 检查一个类型的对象是否能与同类型或不同类型的对象交换 (类模板) |

| 性质查询                                                     |                                       |
| ------------------------------------------------------------ | ------------------------------------- |
| [alignment_of](https://zh.cppreference.com/w/cpp/types/alignment_of)(C++11) | 获取类型的对齐要求 (类模板)           |
| [rank](https://zh.cppreference.com/w/cpp/types/rank)(C++11)  | 获取数组类型的维数 (类模板)           |
| [extent](https://zh.cppreference.com/w/cpp/types/extent)(C++11) | 获取数组类型在指定维度的大小 (类模板) |



## std::integral_constant

- integral_constant是一个**用于包装目的的类**。

```
template <class T, T _Val>
struct integral_constant 
{
	static constexpr T value = _Val;
	using value_type = T;
	using type = integral_constant;
	constexpr operator value_type() const noexcept 
	{
		return value;
	}
	constexpr value_type operator()() const noexcept
	{
		return value;
	}
};
```



```
cout << std::integral_constant<int,15>::value << endl;
cout << std::integral_constant<bool, true>::value << endl;
15
1
```

- **通过integral_constant的包装，把 !std::is_union<B>::value 这个值（0）包装成了一个类型**

  ：

  - std::integral_constant<bool, !std::is_union<B>::value>

- **在很多需要用到类型的场合（比如函数返回类型中）就可以使用这个类型**。

- 注意：!std::is_union<B>::value值在编译时就能确定。

### is_void





### is_const

`is_const`检查一个类型声明有没有`const`修饰符

```c
template<class T>
struct is_const : public false_type {};

// 针对const类型的特化版本
template<class T>
struct is_const<const T> : public true_type {};
```

这个没啥难度，无非就是个模板特化罢了。

###  is_class



```
template <class _Tp> struct  is_class
    : public integral_constant<bool, __is_class(_Tp)> {};
```



```
template <class _Tp, _Tp __v>
struct  integral_constant
{
  static  const _Tp      value = __v;
  typedef _Tp               value_type;
  typedef integral_constant type;
  
   operator value_type() const  {return value;}

  
  constexpr value_type operator ()() const  {return value;}
#endif
};
```





```
template<class T, T v>
    struct integral_constant{
    static constexpr T value = v;
    typedef T value_type;
    typedef integral_constant type;
    constexpr operator value_type() const noexcept {
        return value;
    }
};

namespace detail {
    template <class T> char test(int T::*);   //this line
    struct two{
        char c[2];
    };
    template <class T> two test(...);         //this line
}

//Not concerned about the is_union<T> implementation right now
template <class T>
struct is_class : std::integral_constant<bool, sizeof(detail::test<T>(0))==1 
                                                   && !std::is_union<T>::value> {};
```



## C++ 14 Type Traits



## C++ 17 Type Traits





## C++ 20 Type Traits