---
title: C++ Boost string algorithm 使用
top: false
mathjax: true
categories:
- C++
---

-----



## Case Conversion

| **Algorithm  name** | **Description**                | **Functions**                  |
| ------------------- | ------------------------------ | ------------------------------ |
| to_upper            | Convert a string to upper case | `to_upper_copy()` `to_upper()` |
| to_lower            | Convert a string to lower case | `to_lower_copy()` `to_lower()` |



## Trimming

| **Algorithm  name** | **Description**                                  | **Functions**                                                |
| ------------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| trim_left           | Remove leading spaces from  a string             | `trim_left_copy_if()` `trim_left_if()` `trim_left_copy()` `trim_left()` |
| trim_right          | Remove trailing spaces from a string             | `trim_right_copy_if()` `trim_right_if()` `trim_right_copy()` `trim_right()` |
| trim                | Remove leading and trailing spaces from a string | `trim_copy_if()` `trim_if()` `trikm_copy()` `trim()`         |



## Predicates

| **Algorithm  name**     | **Description**                                              | **Functions**                                            |
| ----------------------- | ------------------------------------------------------------ | -------------------------------------------------------- |
| starts_with             | Check if a string is a prefix of the other one               | `starts_with()` `istarts_with()`                         |
| ends_with               | Check if  a string is a suffix of the other one              | `ends_with()` `iends_with()`                             |
| contains                | Check if a string is contained of the other one              | `contains()` `icontains()`                               |
| equals                  | Check if two strings are equal                               | `equals()` `iequals()`                                   |
| lexicographical_compare | Check if  a string is lexicographically less then another one | `lexicographical_compare()` `ilexicographical_compare()` |
| all                     | Check if all elements of a string satisfy the given predicate | `all()`                                                  |



## Find algorithms

| **Algorithm  name** | **Description**                                              | **Functions**                  |
| ------------------- | ------------------------------------------------------------ | ------------------------------ |
| find_first          | Find the first occurrence of a string in the input           | `find_first()` `ifind_first()` |
| find_last           | Find the  last occurrence of a string in the input           | `find_last()` `ifind_last()`   |
| find_nth            | Find the nth  (zero-indexed) occurrence of a string in the input | `find_nth()`  `ifind_nth()`    |
| find_head           | Retrieve the head of a  string                               | `find_head()`                  |
| find_tail           | Retrieve the tail of a  string                               | `find_tail()`                  |
| find_token          | Find first matching token  in the string                     | `find_token()`                 |
| find_regex          | Use the  regular expression to search the string             | `find_regex()`                 |
| find                | Generic find algorithm                                       | `find()`                       |

## Erase/Replace

| **Algorithm  name** | **Description**                                              | **Functions**                                                |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| replace/erase_first | Replace/Erase  the first occurrence of a string in the input | `replace_first()` `replace_first_copy()` `ireplace_first()` `ireplace_first_copy()` `erase_first()` `erase_first_copy()` `ierase_first()` `ierase_first_copy()` |
| replace/erase_last  | Replace/Erase the last occurrence of a string in the input   | `replace_last()` `replace_last_copy()` `ireplace_last()` `ireplace_last_copy()` `erase_last()` `erase_last_copy()` `ierase_last()` `ierase_last_copy()` |
| replace/erase_nth   | Replace/Erase the nth (zero-indexed) oc- currence of a string in the  input | `replace_nth()` `replace_nth_copy()` `ireplace_nth()` `ireplace_nth_copy()` `erase_nth()` `erase_nth_copy()` `ierase_nth()` `ierase_nth_copy()` |
| replace/erase_all   | Replace/Erase the all occurrences of a string in the input   | `replace_all()` `replace_all_copy()` `ireplace_all()` `ireplace_all_copy()` `erase_all()` `erase_all_copy()` `ierase_all()` `ierase_all_copy()` |
| replace/erase_head  | Replace/Erase the head of  the input                         | replace_head() replace_head_copy() erase_head() erase_head_copy() |
| replace/erase_tail  | Replace/Erase the tail of  the input                         | `replace_tail() replace_tail_copy() erase_tail()` `erase_tail_copy()` |
| replace/erase_regex | Replace/Erase a substring matching the given regular expression | `replace_regex()` `replace_regex_copy()` `erase_regex()` `erase_regex_copy()` |



| **Algorithm  name**     | **Description**                                              | **Functions**                                                |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| replace/erase_regex_all | Replace/Erase all substrings matching the  given regular expression | `replace_all_regex()` `replace_all_regex_copy()` `erase_all_regex()` `erase_all_regex_copy()` |
| find_format             | Generic replace algorithm                                    | `find_format()` `find_format_copy()` `find_format_all()` `find_format_all_copy()` |



## Split

| **Algorithm  name** | **Description**                                              | **Functions**                           |
| ------------------- | ------------------------------------------------------------ | --------------------------------------- |
| find_all            | Find/Extract all matching substrings in the input            | find_all() ifind_all() find_all_regex() |
| split               | Split input into parts                                       | split() split_regex()                   |
| iter_find           | Iteratively apply the finder to the input to find all matching  substrings | iter_find()                             |
| iter_split          | Use the finder  to find matching substrings in the input and  use them as separators to split the input into parts | iter_split()                            |



## Join

| **Algorithm  name** | **Description**                                              | **Functions** |
| ------------------- | ------------------------------------------------------------ | ------------- |
| join                | Join all elements in a container into a single string        | join          |
| join_if             | Join all elements in a container that satis- fies the condition into a  single string | join_if()     |



##  Finders and Formatters

 **Finders**

| **Finder**   | **Description**                                              | **Generators**   |
| ------------ | ------------------------------------------------------------ | ---------------- |
| first_finder | Search for the first match of the string in an input         | `first_finder()` |
| last_finder  | Search for the last match of the string in an input          | `last_finder()`  |
| nth_finder   | Search for the nth (zero-indexed) match of the string in an input | `nth_finder()`   |
| head_finder  | Retrieve the head of an input                                | `head_finder()`  |
| tail_finder  | Retrieve the tail of an input                                | `tail_finder()`  |
| token_finder | Search for a matching  token in an input                     | `token_finder()` |
| range_finder | Do no search, always returns the given range                 | `range_finder()` |
| regex_finder | Search for a substring matching the given regex              | `regex_finder()` |



**Formatters**

| **Formatter**      | **Description**                                              | **Generators**         |
| ------------------ | ------------------------------------------------------------ | ---------------------- |
| const_formatter    | Constant formatter. Always return the specified string       | `const_formatter()`    |
| identity_formatter | Identity formatter. Return unmodified in- put input          | `identity_formatter()` |
| empty_formatter    | Null formatter. Always return an empty string                | `empty_formatter()`    |
| regex_formatter    | Regex formatter. Format regex match us- ing the specification in the  format string | `regex_formatter()`    |

## Iterators

**Find Iterators**

| **Iterator  name** | **Description**                                              | **Iterator class**              |
| ------------------ | ------------------------------------------------------------ | ------------------------------- |
| find_iterator      | Iterates through matching substrings in the input            | [find_iterator](#_bookmark128)  |
| split_iterator     | Iterates through gaps between matching substrings in the input | [split_iterator](#_bookmark138) |

## Classification

**Predicates**

| **Predicate  name** | **Description**                              | **Generator**   |
| ------------------- | -------------------------------------------- | --------------- |
| is_classified       | Generic ctype mask based classification      | is_classified() |
| is_space            | Recognize spaces                             | is_space()      |
| is_alnum            | Recognize alphanumeric characters            | is_alnum()      |
| is_alpha            | Recognize letters                            | is_alpha()      |
| is_cntrl            | Recognize control characters                 | is_cntrl()      |
| is_digit            | Recognize decimal digits                     | is_digit()      |
| is_graph            | Recognize graphical  characters              | is_graph()      |
| is_lower            | Recognize lower case  characters             | is_lower()      |
| is_print            | Recognize printable  characters              | is_print()      |
| is_punct            | Recognize punctuation characters             | is_punct()      |
| is_upper            | Recognize uppercase characters               | is_upper()      |
| is_xdigit           | Recognize hexadecimal digits                 | is_xdigit()     |
| is_any_of           | Recognize any  of a sequence of characters   | is_any_of()     |
| is_from_range       | Recognize characters inside a min..max range | is_from_range() |





## 示例







## 相关参考

[boost 处理字符串_dodo_check的博客-CSDN博客](https://blog.csdn.net/dodo_check/article/details/8219456)

[boost的字符串处理函数——string algorithm - 天方 - 博客园 (cnblogs.com)](https://www.cnblogs.com/TianFang/archive/2013/02/04/2891538.html)



[Chapter 5. Boost.StringAlgorithms (theboostcpplibraries.com)](https://theboostcpplibraries.com/boost.stringalgorithms)

[Chapter 21. Boost String Algorithms Library - 1.46.1](https://www.boost.org/doc/libs/1_46_1/doc/html/string_algo.html)

[C++ Boost String Algorithms Library - GeeksforGeeks](https://www.geeksforgeeks.org/c-boost-string-algorithms-library/)

[Chapter 32. Boost String Algorithms Library - 1.61.0](https://www.boost.org/doc/libs/1_61_0/doc/html/string_algo.html)

[boostorg/algorithm: Boost.org algorithm module (github.com)](https://github.com/boostorg/algorithm)

[Boost String Algorithms Library (ethz.ch)](https://scicomp.ethz.ch/public/manual/Boost/1.55.0/string_algo.pdf)

