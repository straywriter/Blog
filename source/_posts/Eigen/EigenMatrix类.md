---
title: Eigen Matrix类
top: false
mathjax: true
categories:
- Eigen
---

-----



x

## Eigen 矩阵

### Matrix类 定义

```cpp
Matrix<typename Scalar,
             int RowsAtCompileTime,
             int ColsAtCompileTime,
             int Options=0,
             int MaxRowsAtCompileTime = RowsAtCompileTime,
             int MaxColsAtCompileTime = ColsAtCompileTime>
```

- Scalar 元素类型
- RowsAtCompileTime 行
- ColsAtCompileTime 列
- Options 比特标志位
- MaxRowsAtCompileTime和MaxColsAtCompileTime表示在编译阶段矩阵的上限。



> Eigen的使用在官网上有详细的介绍，这里对我学习过程中用到的基本操作进行介绍。首先是矩阵的定义。
> 在矩阵类的模板参数共有6个。一般情况下我们只需要关注前三个参数即可。前三个模板参数如下所示：
>
> ```
> Matrix<typename Scalar,int RowsAtCompileTime,int ColsAtCompileTime>
> ```
>
>  
>
> 1. Scalar参数为矩阵元素的类型，该参数可以是int,float,double等。
> 2. RowsAtCompileTime和ColsAtCompileTime是矩阵的行数和列数。

### Eigen 常用类型

```cpp
typedef Matrix< std::complex<double> , 2 , 2 > Matrix2cd
typedef Matrix< std::complex<float> , 2 , 2 > Matrix2cf
typedef Matrix< double , 2 , 2 > Matrix2d
typedef Matrix< float , 2 , 2 > Matrix2f
typedef Matrix< int , 2 , 2 > Matrix2i
typedef Matrix< std::complex<double> , 3 , 3 > Matrix3cd
typedef Matrix< std::complex<float> , 3 , 3 > Matrix3cf
typedef Matrix< double , 3 , 3 > Matrix3d
typedef Matrix< float , 3 , 3 > Matrix3f
typedef Matrix< int , 3 , 3 > Matrix3i
typedef Matrix< std::complex<double> , 4 , 4 > Matrix4cd
typedef Matrix< std::complex<float> , 4 , 4 > Matrix4cf
typedef Matrix< double , 4 , 4 > Matrix4d
typedef Matrix< float , 4 , 4 > Matrix4f
typedef Matrix< int , 4 , 4 > Matrix4i
typedef Matrix< std::complex<double> , Dynamic , Dynamic > MatrixXcd
typedef Matrix< std::complex<float> , Dynamic , Dynamic > MatrixXcf
typedef Matrix< double , Dynamic , Dynamic > MatrixXd
typedef Matrix< float , Dynamic , Dynamic > MatrixXf
typedef Matrix< int , Dynamic , Dynamic > MatrixXi
typedef Matrix< std::complex<double> , 1, 2 > RowVector2cd
typedef Matrix< std::complex<float> , 1, 2 > RowVector2cf
typedef Matrix< double , 1, 2 > RowVector2d
typedef Matrix< float , 1, 2 > RowVector2f
typedef Matrix< int , 1, 2 > RowVector2i
typedef Matrix< std::complex<double> , 1, 3 > RowVector3cd
typedef Matrix< std::complex<float> , 1, 3 > RowVector3cf
typedef Matrix< double , 1, 3 > RowVector3d
typedef Matrix< float , 1, 3 > RowVector3f
typedef Matrix< int , 1, 3 > RowVector3i
typedef Matrix< std::complex<double> , 1, 4 > RowVector4cd
typedef Matrix< std::complex<float> , 1, 4 > RowVector4cf
typedef Matrix< double , 1, 4 > RowVector4d
typedef Matrix< float , 1, 4 > RowVector4f
typedef Matrix< int , 1, 4 > RowVector4i
typedef Matrix< std::complex<double> , 1, Dynamic > RowVectorXcd
typedef Matrix< std::complex<float> , 1, Dynamic > RowVectorXcf
typedef Matrix< double , 1, Dynamic > RowVectorXd
typedef Matrix< float , 1, Dynamic > RowVectorXf
typedef Matrix< int , 1, Dynamic > RowVectorXi
typedef Matrix< std::complex<double> , 2 , 1> Vector2cd
typedef Matrix< std::complex<float> , 2 , 1> Vector2cf
typedef Matrix< double , 2 , 1> Vector2d
typedef Matrix< float , 2 , 1> Vector2f
typedef Matrix< int , 2 , 1> Vector2i
typedef Matrix< std::complex<double> , 3 , 1> Vector3cd
typedef Matrix< std::complex<float> , 3 , 1> Vector3cf
typedef Matrix< double , 3 , 1> Vector3d
typedef Matrix< float , 3 , 1> Vector3f
typedef Matrix< int , 3 , 1> Vector3i
typedef Matrix< std::complex<double> , 4 , 1> Vector4cd
typedef Matrix< std::complex<float> , 4 , 1> Vector4cf
typedef Matrix< double , 4 , 1> Vector4d
typedef Matrix< float , 4 , 1> Vector4f
typedef Matrix< int , 4 , 1> Vector4i
typedef Matrix< std::complex<double> , Dynamic , 1> VectorXcd
typedef Matrix< std::complex<float> , Dynamic , 1> VectorXcf
typedef Matrix< double , Dynamic , 1> VectorXd
typedef Matrix< float , Dynamic , 1> VectorXf
typedef Matrix< int , Dynamic , 1> VectorXi
```



## 矩阵构建



经过上面的介绍以后，我们应该能定义一些基本的矩阵了。如：

```
Matrix3f a;   //定义一个float类型3×3固定矩阵a
MatrixXf b;   //定义一个float类型动态矩阵b(0×0)
Matrix<int,Dynamic,3> b;  //定义一个int类型动态矩阵(0×3)
```

对应动态矩阵，我们也可以在构造的时候给出矩阵所占用的空间，比如：

```
MatrixXf a(10,15);  //定义float类型10×15动态矩阵
VectorXf b(30); //定义float类型30×1动态矩阵(列向量)
```

为了保持一致性，我们也可以使用上面构造函数的形式定义一个固定的矩阵，即`Matrix3f a(3,3)`也是允许的。

上面矩阵在构造的过程中并没有初始化，Eigen还为一些小的(列)向量提供了可以初始化的构造函数。如：

```
Vector2d a(5.0,6.0);
Vector3d b(5.0,6.0,7.0);
Vector4d c(5.0,6.0,7.0,8.0);
```



## 矩阵元素访问



Eigen提供了矩阵元素的访问形式和matlab中矩阵的访问形式非常相似，最大的不同是matlab中元素从1开始，而Eigen的矩阵中元素是从0开始访问。对于矩阵，第一个参数为行索引，第二个参数为列索引。而对于向量只需要给出一个索引即可。

> ```
> #include <iostream>
> #include "Eigen\Core"
> 
> //import most common Eigen types
> using namespace Eigen;
> 
> int main()
> {
>     MatrixXd m(2,2);
>     m(0,0) = 3;
>     m(1,0) = 2.5;
>     m(0,1) = -1;
>     m(1,1) = m(1,0) + m(0,1);
>     
>     std::cout<<"Hear is the matrix m:\n"<<m<<std::endl;
>     VectorXd v(2);
>     v(0) = 4;
>     v(1) = v(0) - 1;
>     std::cout<<"Here is the vector v:\n"<<v<<std::endl;
> }
> ```

输出结果如下：

> ```
> Hear is the matrix m:
> 3  -1
> 2.5 1.5
> Here is the vector v:
> 4
> 3
> ```

像`m(index)`这种访问形式并不仅限于向量之中，对于矩阵也可以这样访问。这一点和matlab相同，我们知道在matlab中定义一个矩阵a(3,4)，如果我访问a(5)相当于访问a(2,2),这是因为在matlab中矩阵是按列存储的。这里比较灵活，默认矩阵元素也是按列存储的，但是我们也可以通过矩阵模板构造参数Options=RowMajor改变存储方式(这个参数是我们还没有提到的矩阵构造参数的第4个参数)。



## 矩阵的逗号初始化





## 块操作

块是`matrix`或`array`中的矩形子块。



```c
// 方法1
.block(i, j, p, q)    //起点(i, j)，块大小(p, q)，构建一个动态尺寸的block
.block<p, q>(i, j)  // 构建一个固定尺寸的block
```

- `matrix.row(i)`: 矩阵第i行
- `matrix.col(j)`: 矩阵第j列
- 角相关操作

| operater |      dynamic-size block      |        fixed_size block        |
| :------: | :--------------------------: | :----------------------------: |
|  左上角  |  matrix.topLeftCorner(p,q)   |  matrix.topLeftCorner<p,q>()   |
|  左下角  | matrix.bottomLeftCorner(p,q) | matrix.bottomLeftCorner<p,q>() |
|  右上角  |  matrix.topRightCorner(p,q)  |  matrix.topRightCorner<p,q>()  |
|  右下角  |             你猜             |              你猜              |
|  前q行   |      matrix.topRows(q)       |      matrix.topRows<q>()       |
|  后q行   |     matrix.bottomRows(q)     |     matrix.bottomRows<q>()     |
|  左p列   |      matrix.leftCols(p)      |      matrix.leftCols<p>()      |
|  右p列   |     matrix.rightCols(p)      |     matrix.rightCols<p>()      |

- `Vector`的块操作

|     operater     | dynamic_size block  |   fixed_size block   |
| :--------------: | :-----------------: | :------------------: |
|      前n个       |   vector.head(n)    |   vector.head<n>()   |
|      后n个       |   vector.tail(n)    |   vector.tail<n>()   |
| 从i开始的n个元素 | vector.segment(i,n) | vector.segment<n>(i) |



## 矩阵运算







```cpp
MatrixXcf a = MatrixXcf::Random(3,3);
a.transpose();  # 转置
a.conjugate();  # 共轭
a.adjoint();       # 共轭转置（伴随矩阵）
# 对于实数矩阵，conjugate不执行任何操作，adjoint等价于transpose
a.transposeInPlace() #原地转置

Vector3d v(1,2,3);
Vector3d w(4,5,6);
v.dot(w);    # 点积
v.cross(w);  # 叉积

Matrix2d a;
a << 1, 2, 3, 4;
a.sum();      # 所有元素求和
a.prod();      # 所有元素乘积
a.mean();    # 所有元素求平均
a.minCoeff();    # 所有元素中最小元素
a.maxCoeff();   # 所有元素中最大元素
a.trace();      # 迹，对角元素的和
# minCoeff和maxCoeff还可以返回结果元素的位置信息
int i, j;
a.minCoeff(&i, &j);
```





## 矩阵的大小



## 矩阵赋值和大小变换





## 固定矩阵和动态矩阵









## 相关参考

[Eigen: Eigen::Matrix< _Scalar, _Rows, _Cols, _Options, _MaxRows, _MaxCols > Class Template Reference](https://eigen.tuxfamily.org/dox/classEigen_1_1Matrix.html)

[Eigen: The Matrix class](https://eigen.tuxfamily.org/dox/group__TutorialMatrixClass.html)





[Eigen Library for Matrix Algebra in C++ | QuantStart](https://www.quantstart.com/articles/Eigen-Library-for-Matrix-Algebra-in-C/)

[Eigen库使用指南 - 简书 (jianshu.com)](https://www.jianshu.com/p/931dff3b1b21)

[Eigen学习笔记2：C++矩阵运算库Eigen介绍 - 爱国呐 - 博客园 (cnblogs.com)](https://www.cnblogs.com/aiguona/p/9413748.html)