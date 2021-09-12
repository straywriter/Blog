---
title: OpenGL GLSL语法基础
top: false
mathjax: true
date: 2021-03-18 13:45:33
categories:
- 计算机图形学
---



-----

## 简介

**`GLSL`**（**OpenGL Shading Language**） 全称 **OpenGL** 着色语言，是用来在 **OpenGL** 中着色编程的语言，也即开发人员写的短小的自定义程序，他们是在图形卡的 **GPU**上执行的，代替了固定的渲染管线的一部分，使渲染管线中不同层次具有可编程性。 **`GLSL`** 其使用 **C** 语言作为基础高阶着色语言，避免了使用汇编语言或硬件规格语言的复杂性。






## 基础语法

### 注释

> 单行注释：// 多行注释：/* */

**变量命名**

GLSL的变量命名方式与C语言类似。变量的名称可以使用字母，数字以及下划线，但变量名不能以数字开头，还有变量名不能以gl_作为前缀，这个是GLSL保留的前缀，用于GLSL的内部变量。当然还有一些GLSL保留的名称是不能够作为变量的名称的。

### 变量命名

**`GLSL`** 的变量命名方式与 **C** 语言类似，可使用字母，数字以及下划线，不能以数字开头。还需要注意的是，变量名不能以 **`gl_`** 作为前缀，这个是 **`GLSL`** 保留的前缀，用于 **`GLSL`** 的内部变量。

### 表达式

**运算符**

| 优先级(越小越高) | 运算符        | 说明                                                         | 结合性 |
| :--------------- | :------------ | :----------------------------------------------------------- | :----- |
| 1                | ()            | 聚组:a*(b+c)                                                 | N/A    |
| 2                | [] () . ++ -- | 数组下标__[],方法参数__fun(arg1,arg2,arg3),属性访问**a.b**,自增/减后缀**a++ a--** | L - R  |
| 3                | ++ -- + - !   | 自增/减前缀**++a --a**,正负号(一般正号不写)a ,-a,取反**!false** | R - L  |
| 4                | * /           | 乘除数学运算                                                 | L - R  |
| 5                | + -           | 加减数学运算                                                 | L - R  |
| 7                | < > <= >=     | 关系运算符                                                   | L - R  |
| 8                | == !=         | 相等性运算符                                                 | L - R  |
| 12               | &&            | 逻辑与                                                       | L - R  |
| 13               | ^^            | 逻辑排他或(用处基本等于!=)                                   | L - R  |
| 14               | II            | 逻辑或                                                       | L - R  |
| 15               | **? :**       | 三目运算符                                                   | L - R  |
| 16               | = += -= *= /= | 赋值与复合赋值                                               | L - R  |
| 17               | ,             | 顺序分配运算                                                 | L - R  |

**左值与右值:**

```
左值:表示一个储存位置,可以是变量,也可以是表达式,但表达式最后的结果必须是一个储存位置.

右值:表示一个值, 可以是一个变量或者表达式再或者纯粹的值.

操作符的优先级：决定含有多个操作符的表达式的求值顺序，每个操作的优先级不同.

操作符的结合性：决定相同优先级的操作符是从左到右计算，还是从右到左计算。
```

**操作符**

GLSL语言的操作符与C语言相似。如下表（操作符的优先级从高到低排列）

| 操作符         | 描述                                                 |
| :------------- | :--------------------------------------------------- |
| ()             | 用于表达式组合，函数调用，构造                       |
| []             | 数组下标，向量或矩阵的选择器                         |
| .              | 结构体和向量的成员选择                               |
| ++ --          | 前缀或后缀的自增自减操作符                           |
| + – !          | 一元操作符，表示正 负 逻辑非                         |
| * /            | 乘 除操作符                                          |
| + -            | 二元操作符 表示加 减操作                             |
| <> <= >= == != | 小于，大于，小于等于， 大于等于，等于，不等于 判断符 |
| && \|\| ^^     | 逻辑与 ，或， 异或                                   |
| ?:             | 条件判断符                                           |
| = += –= *= /=  | 赋值操作符                                           |
| ,              | 表示序列                                             |

> 求地址的& 和 解引用的 * 操作符不再GLSL中出现，因为GLSL不能直接操作地址。
>
> 类型转换操作也是不允许的。
>
> 位操作符(&,|,^,~, <<, >> ,&=, |=, ^=, <<=, >>=)是GLSL保留的操作符，将来可能会被使用。
>
> 求模操作（%，%=)也是保留的。

------

**数组访问**

数组的下标从0开始。合理的范围是[0, size - 1]。跟C语言一样。如果数组访问越界了，那行为是未定义的。如果着色器的编译器在编译时知道数组访问越界了，就会提示编译失败。

```
vec4 myColor, ambient, diffuse[6], specular[6];

myColor = ambient + diffuse[4] + specular[4];
```



-----

### 语句

**流控制**

glsl的流控制和c语言非常相似,这里不必再做过多说明,唯一不同的是片段着色器中有一种特殊的控制流`discard`. 使用discard会退出片段着色器，不执行后面的片段着色操作。片段也不会写入帧缓冲区。

```
for (l = 0; l < numLights; l++)
{
    if (!lightExists[l]);
        continue;
    color += light[l];
}
...

while (i < num)
{
    sum += color[i];
    i++;
}
...

do{
    color += light[lightNum];
    lightNum--;
}while (lightNum > 0)

...

if (true)
    discard;
```

**discard**

片段着色器中有一种特殊的控制流成为discard。使用discard会退出片段着色器，不执行后面的片段着色操作。片段也不会写入帧缓冲区。

```
if (ture)
	discard;
```

**分量访问**

| 分量访问符 | 符号描述             |
| :--------- | :------------------- |
| (x,y,z,w)  | 与位置相关的分量     |
| (r,g,b,a)  | 与颜色相关的分量     |
| (s,t,p,q)  | 与纹理坐标相关的分量 |

### GLSL函数

glsl允许在程序的最外部声明函数.函数不能嵌套,不能递归调用,且必须声明返回值类型(无返回值时声明为void) 在其他方面glsl函数与c函数非常类似.

```
vec4 getPosition(){ 
    vec4 v4 = vec4(0.,0.,0.,1.);
    return v4;
}

void doubleSize(inout float size){
    size= size*2.0  ;
}
void main() {
    float psize= 10.0;
    doubleSize(psize);
    gl_Position = getPosition();
    gl_PointSize = psize;
}
```





### GLSL预编译指令

以 # 开头的是预编译指令,常用的有:

```
#define #undef #if #ifdef #ifndef #else
#elif #endif #error #pragma #extension #version #line
```

比如 **#version 100** 他的意思是规定当前shader使用 GLSL ES 1.00标准进行编译,如果使用这条预编译指令,则他必须出现在程序的最开始位置.

**内置的宏:**

`__LINE__` : 当前源码中的行号.

`__VERSION__` : 一个整数,指示当前的glsl版本 比如 100 ps: 100 = v1.00

`GL_ES` : 如果当前是在 OPGL ES 环境中运行则 GL_ES 被设置成1,一般用来检查当前环境是不是 OPENGL ES.

`GL_FRAGMENT_PRECISION_HIGH` : 如果当前系统glsl的片元着色器支持高浮点精度,则设置为1.一般用于检查着色器精度.

实例:

1.如何通过判断系统环境,来选择合适的精度:

```
#ifdef GL_ES //
#ifdef GL_FRAGMENT_PRECISION_HIGH
precision highp float;
#else
precision mediump float;
#endif
#endif
```

2.自定义宏:

```
#define NUM 100
#if NUM==100
#endif
```



## 数据类型

### 基础数据类型

|   类型    |                             描述                             |
| :-------: | :----------------------------------------------------------: |
| **void**  | 跟 **C** 语言的 **void** 类似，表示空类型。作为函数的返回类型，表示这个函数不返回值。 |
| **bool**  | **布尔类型**，**true** 或 **false**，以及可以产生布尔型的表达式。 |
|  **int**  |                        **有符号整型**                        |
| **uint**  |                        **无符号整形**                        |
| **float** |                          **浮点型**                          |

### 纹理采样类型

|        类型         |                             描述                             |
| :-----------------: | :----------------------------------------------------------: |
|    **sampler1D**    | 用于内建的纹理函数中引用指定的 **1D纹理的句柄**。只可以作为一致变量或者函数参数使用 |
|    **sampler2D**    |                       **二维纹理句柄**                       |
|    **sampler3D**    |                       **三维纹理句柄**                       |
|   **samplerCube**   |                    **cube map 纹理句柄**                     |
| **sampler1DShadow** |                     **一维深度纹理句柄**                     |
| **sampler2DShadow** |                     **二维深度纹理句柄**                     |

### 聚合类型（向量和矩阵类型）

**向量类型**

|       类型        |               描述                |
| :---------------: | :-------------------------------: |
|  vec2,vec3,vec4   |    2分量、3分量和4分量浮点向量    |
| ivec2,ivec3,ivec4 |    2分量、3分量和4分量整数向量    |
| uvec2,uvec3,uvec4 | 2分量、3分量和4分量无符号整数向量 |
| bvec2,vbec3,bvec4 |    2分量、3分量和4分量布尔向量    |

 **A、向量声明--4分量的float 类型向量**

```
vec4 V1;
```

 **B、声明向量并对其进行构造**

```
vec4 V2 = vec4(1,2,3,4);
```

 **C、向量运算**

```
vec4 v;
vec4 vOldPos = vec4(1,2,3,4);
vec4 vOffset = vec4(1,2,3,4);
//注意：接下来假设所有参与运算的变量已定义并赋值。
v = vOldPos + vOffset;
v = vNewPos;
v += vec4(10,10,10,10);
v = vOldPos * vOffset;
v *= 5;
```

 **D、向量元素的获取（成分选择）**

向量中单独的成分可以通过 **{x,y,z,w}, {r,g,b,a}** 或者 **{s,t,p,q}** 的记法来表示。这些不同的记法用于 **顶点**，**颜色**，**纹理坐标**。在成分选择中，你不可以混合使用这些记法。其中 **{s,t,p,q}** 中的 **p** 替换了**纹理**的 **r** 坐标，因为与颜色 **r** 重复了。下面是用法举例： 例如有向量 **v1** 和 **v2**:

```
vec3 v1 = {0.5, 0.35, 0.7};
vec4 v2 = {0.1, 0.2, 0.3, 0.4};
```

可以通过 **{x,y,z,w}, {r,g,b,a}** 或者 **{s,t,p,q}** 来取出向量中的元素值。 通过 **x,y,z,w**：

```
v2.x = 3.0f;
v2.xy = vec2(3.0f,4.0f);
v2.xyz = vec3(3,0f,4,0f,5.0f);
```

通过 **r,g,b,a**：

```
v2.r = 3.0f;
v2.rgba = vec4(1.0f,1.0f,1.0f,1.0f);
```

通过 **s,t,q,r**：

```
v2.stqr = vec2(1.0f, 0.0f, 0.0f, 1.0f);
```

错误示例：

```
float myQ = v1.q;// 出错，数组越界访问，q代表第四个元素
float myRY = v1.ry; // 不合法，混合使用记法
```

向量还支持一次性对所有分量操作

```
v1.x = v2.x +5.0f; 
v1.y = v2.y +4.0f; 
v1.z = v2.z +3.0f;
v1.xyz = v2.xyz + vec3(5.0f,4.0f,3.0f);
```

------

**（2）矩阵类型**

|      类型      |                     描述                     |
| :------------: | :------------------------------------------: |
| mat2 或 mat2x2 |             2x2的浮点数矩阵类型              |
| mat3 或 mat3x3 |             3x3的浮点数矩阵类型              |
| mat4 或 mat4x4 |             4x4的浮点数矩阵类型              |
|     mat2x3     | 2列3行的浮点矩阵（OpenGL的矩阵是列主顺序的） |
|     mat2x4     |               2列4行的浮点矩阵               |
|     mat3x2     |               3列2行的浮点矩阵               |
|     mat3x4     |               3列4行的浮点矩阵               |
|     mat4x2     |               4列2行的浮点矩阵               |
|     mat4x3     |               4列3行的浮点矩阵               |

**创建矩阵：**

```
mat4 m1,m2,m3;
```

**构造单元矩阵：**

```
mat4 m2 = mat4(1.0f,0.0f,0.0f,0.0f
                    0.0f,1.0f,0.0f,0.0f,
                    0.0f,0.0f,1.0f,0.0f,
                    0.0f,0.0f,0.0f,1.0f);
```

**或者**

```
mat4 m4 = mat4(1.0f);
```

### 向量矩阵运算

1、不同类型 float 与 int 间的运算:**

**float** 与 **int** 之间进行运算，需要进行一次显示转换，以下表达式都是正确的:

```
int a = int(2.0);
float a = float(2);

int a = int(2.0)*2 + 1;
float a = float(2)*6.0+2.3;
复制代码
```

------

**2、float 与 vec(向量)、mat(矩阵) 的运算:**

> - **逐分量运算** **vec，mat** 这些类型其实是由 **float** 复合而成的，当它们与**float** 运算时，其实就是在每一个分量上分别与 **float** 进行运算，这就是所谓的 **逐分量运算**。**GLSL** 里，大部分涉及 **vec，mat** 的运算都是逐分量运算，但也并不全是。下文中就会讲到特例。 **逐分量运算** 是线性的，这就是说 vec 与 float 的运算结果是还是 **vec**。

**int** 与 **vec，mat** 之间是不可运算的，因为 **vec** 和 **mat** 中的每一个分量都是 **float** 类型的，无法与 **int** 进行逐分量计算。

下面枚举了几种 **float** 与 **vec，mat** 运算的情况：

```
vec3 a  = vec3(1.0, 2.0, 3.0);
mat3 m  = mat3(1.0);
float s = 10.0;

vec3 b  = s * a; // vec3(10.0, 20.0, 30.0)
vec3 c  = a * s; // vec3(10.0, 20.0, 30.0)
mat3 m2 = s * m; // = mat3(10.0)
mat3 m3 = m * s; // = mat3(10.0)
复制代码
```

**3、vec(向量) 与 vec(向量)运算：**

两向量间的运算首先要保证操作数的阶数都相同，否则不能计算。例如: **`vec3\*vec2`** 和 **`vec4+vec3`** 等等都是不行的。

它们的计算方式是两操作数在同位置上的分量分别进行运算，其本质还是逐分量进行的，这和上面所说的 **float** 类型的逐分量运算可能有一点点差异，相同的是 **vec** 与 **vec** 运算结果还是 **vec**，且阶数不变。

```
vec3 a = vec3(1.0, 2.0, 3.0);
vec3 b = vec3(0.1, 0.2, 0.3);
vec3 c = a + b; // = vec3(1.1, 2.2, 3.3);
vec3 d = a * b; // = vec3(0.1, 0.4, 0.9);
```

AB=[a1,1a1,2a2,1a2,2][b1,1b1,2b2,1b2,2]=[a1,1b1,1+a1,2b2,1a1,1b1,2+a1,2b2,2a2,1b1,1+a2,2b2,1a2,1b1,2+a2,2b2,2]

**4、vec(向量) 与 mat(矩阵):**

要保证操作数的阶数相同，且 **vec** 与 **mat** 间只存在乘法运算。 它们的计算方式和线性代数中的矩阵乘法相同，**不是逐分量运算**。

```
vec2 v = vec2(10., 20.);
mat2 m = mat2(1., 2.,  3., 4.);
vec2 w = m * v; // = vec2(1. * 10. + 3. * 20., 2. * 10. + 4. * 20.)

vec2 v = vec2(10., 20.);
mat2 m = mat2(1., 2.,  3., 4.);
vec2 w = v * m; // = vec2(1. * 10. + 2. * 20., 3. * 10. + 4. * 20.)
```

**5、mat(矩阵) 与 mat(矩阵):**

要保证操作数的阶数相同。

在 **mat** 与 **mat** 的运算中，除了乘法是线性代数中的矩阵乘法外，其余的运算仍为**逐分量运算**。简单说就是只有乘法是特殊的，其余都和 **vec** 与 **vec** 运算类似。

```
mat2 a = mat2(1., 2.,  3., 4.);
mat2 b = mat2(10., 20.,  30., 40.);
mat2 c = a * b; // mat2(1.*10.+3.*20.,2.*10.+4.*20.,1.* 30.+3.*40.,2.* 30.+4.*40.);

mat2 d = a+b;// mat2(1.+10.,2.+20.,3.+30.,4.+40);
```







### 结构体

结构体可以组合基本类型和数组来形成用户自定义的类型。在定义一个结构体的同时，你可以定义一个结构体实例。或者后面再定义。

```
struct fogStruct {
 vec4 color;
 float start;
 float end;
 vec3 points[3]; // 固定大小的数组是合法的
} fogVar;
```

可以通过 **=** 为结构体赋值，或者使用 **==，!=** 来判断两个结构体是否相等。

```
fogVar = fogStruct(vec4(1.0,0.0,0.0,1.0),0.5,2.0);
vec4 color = fogVar.color;
float start = fogVar.start;
```



### 构造函数

glsl中变量可以在声明的时候初始化,`float pSize = 10.0` 也可以先声明然后等需要的时候在进行赋值.

聚合类型对象如(向量,矩阵,数组,结构) 需要使用其构造函数来进行初始化. `vec4 color = vec4(0.0, 1.0, 0.0, 1.0);`

```
//一般类型
float pSize = 10.0;
float pSize1;
pSize1=10.0;
...

//复合类型
vec4 color = vec4(0.0, 1.0, 0.0, 1.0);
vec4 color1;
color1 =vec4(0.0, 1.0, 0.0, 1.0);
...

//结构
struct light {
    float intensity;
    vec3 position;
};
light lightVar = light(3.0, vec3(1.0, 2.0, 3.0));

//数组
const float c[3] = float[3](5.0, 7.2, 1.1);
```

### 数组

**`GLSL`** 中只可以使用一维的数组。数组的类型可以是一切基本类型或者结构体。下面的几种数组声明是合法的：

```
float floatArray[4];
vec4 vecArray[2];
float a[4] = float[](1.0,2.0,3.0,4.0);
vec2 c[2] = vec2[2](vec2(1.0,2.0),vec2(3.0,4.0));
```

数组类型内建了一个`length()`函数，可以返回数组的长度。

```
lightPositions.length() // 返回数组的长度
```

### 类型转换

**glsl可以使用构造函数进行显式类型转换,各值如下:**

```
bool t= true;
bool f = false;

int a = int(t); //true转换为1或1.0
int a1 = int(f);//false转换为0或0.0

float b = float(t);
float b1 = float(f);

bool c = bool(0);//0或0.0转换为false
bool c1 = bool(1);//非0转换为true

bool d = bool(0.0);
bool d1 = bool(1.0);
```

**glsl中,没有隐式类型转换,原则上glsl要求任何表达式左右两侧(l-value),(r-value)的类型必须一致 也就是说以下表达式都是错误的:**

```
int a =2.0; //错误,r-value为float 而 lvalue 为int.
int a =1.0+2;
float a =2;
float a =2.0+1;
bool a = 0; 
vec3 a = vec3(1.0, 2.0, 3.0) * 2;
```

------

### 精度限定

glsl在进行光栅化着色的时候,会产生大量的浮点数运算,这些运算可能是当前设备所不能承受的,所以glsl提供了3种浮点数精度,我们可以根据不同的设备来使用合适的精度.

在变量前面加上 `highp` `mediump` `lowp` 即可完成对该变量的精度声明.

```
lowp float color;
varying mediump vec2 Coord;
lowp ivec2 foo(lowp mat3);
highp mat4 m;
```

我们一般在片元着色器(fragment shader)最开始的地方加上 `precision mediump float;` 便设定了默认的精度.这样所有没有显式表明精度的变量 都会按照设定好的默认精度来处理.

**如何确定精度:**

变量的精度首先是由精度限定符决定的,如果没有精度限定符,则要寻找其右侧表达式中,已经确定精度的变量,一旦找到,那么整个表达式都将在该精度下运行.如果找到多个, 则选择精度较高的那种,如果一个都找不到,则使用默认或更大的精度类型.

```
uniform highp float h1;
highp float h2 = 2.3 * 4.7; //运算过程和结果都 是高精度
mediump float m;
m = 3.7 * h1 * h2; //运算过程 是高精度
h2 = m * h1; //运算过程 是高精度
m = h2 – h1; //运算过程 是高精度
h2 = m + m; //运算过程和结果都 是中等精度
void f(highp float p); // 形参 p 是高精度
f(3.3); //传入的 3.3是高精度
```

**invariant关键字:**

由于shader在编译时会进行一些内部优化,可能会导致同样的运算在不同shader里结果不一定精确相等.这会引起一些问题,尤其是vertx shader向fragmeng shader传值的时候. 所以我们需要使用`invariant` 关键字来显式要求计算结果必须精确一致. 当然我们也可使用 `#pragma STDGL invariant(all)`来命令所有输出变量必须精确一致, 但这样会限制编译器优化程度,降低性能.

```
#pragma STDGL invariant(all) //所有输出变量为 invariant
invariant varying texCoord; //varying在传递数据的时候声明为invariant
```

**限定符的顺序:**

当需要用到多个限定符的时候要遵循以下顺序:

1.在一般变量中: invariant > storage > precision

2.在参数中: storage > parameter > precision

我们来举例说明:

```
invariant varying lowp float color; // invariant > storage > precision

void doubleSize(const in lowp float s){ //storage > parameter > precision
    float s1=s;
}
```

### 修饰符

**1、变量存储限定符**

|        限定符        |                             描述                             |
| :------------------: | :----------------------------------------------------------: |
|                      | （默认的可省略）只是普通的本地变量，可读可写，外部不可见，外部不可访问 |
|      **const**       |        常量值必须在声明时初始化，它是只读的不可修改的        |
|     **varying**      | 顶点着色器的输出，主要负责在 **vertex** 和 **fragment** 之间传递变量。例如颜色或者纹理坐标，（插值后的数据）作为片段着色器的只读输入数据。必须是全局范围声明的全局变量。可以是浮点数类型的标量，向量，矩阵。不能是数组或者结构体。 |
|     **uniform**      | 一致变量。在着色器执行期间一致变量的值是不变的。与 **const** 常量不同的是，这个值在编译时期是未知的是由着色器外部初始化的。一致变量在顶点着色器和片段着色器之间是共享的。它也只能在全局范围进行声明。 |
|    **attribute**     | 表示只读的顶点数据，只用在顶点着色器中。数据来自当前的顶点状态或者顶点数组。它必须是全局范围声明的，不能再函数内部。一个 **attribute** 可以是浮点数类型的标量，向量，或者矩阵。不可以是数组或则结构体 |
| **centorid varying** | 在没有多重采样的情况下，与 **varying** 是一样的意思。在多重采样时，**centorid varying** 在光栅化的图形内部进行求值而不是在片段中心的固定位置求值。 |
|    **invariant**     | （不变量）用于表示顶点着色器的输出和任何匹配片段着色器的输入，在不同的着色器中计算产生的值必须是一致的。所有的数据流和控制流，写入一个 **invariant** 变量的是一致的。编译器为了保证结果是完全一致的，需要放弃那些可能会导致不一致值的潜在的优化。除非必要，不要使用这个修饰符。在多通道渲染中避免 **z-fighting** 可能会使用到。 |

**2、函数参数限定符**

**`GLSL`** 允许自定义函数，但参数默认是以值形式（**`in`** 限定符）传入的，也就是说任何变量在传入时都会被拷贝一份，若想以引用方式传参，需要增加函数参数限定符。

|  限定符   |                             描述                             |
| :-------: | :----------------------------------------------------------: |
|  **in**   | 用在函数的参数中，表示这个参数是输入的，在函数中改变这个值，并不会影响对调用的函数产生副作用。（相当于C语言的传值），这个是函数参数默认的修饰符 |
|  **out**  |    用在函数的参数中，表示该参数是输出参数，值是会改变的。    |
| **inout** |    用在函数的参数，表示这个参数即是输入参数也是输出参数。    |

其中使用 **inout** 方式传递的参数便与其他 **OOP** 语言中的引用传递类似，参数可读写，函数内对参数的修改会影响到传入参数本身。 **eg：**

```
vec4 getPosition(out vec4 p){ 
    p = vec4(0.,0.,0.,1.);
    return v4;
}

void doubleSize(inout float size){
    size= size * 3.0  ;
}
```



## 内置变量和函数

### 内置变量

内置变量可以与固定函数功能进行交互。在使用前不需要声明。

**顶点着色器可用的内置变量**

| 名称                   | 类型  | 描述                                                         |
| :--------------------- | :---- | :----------------------------------------------------------- |
| gl_Color               | vec4  | 输入属性-表示顶点的主颜色                                    |
| gl_SecondaryColor      | vec4  | 输入属性-表示顶点的辅助颜色                                  |
| gl_Normal              | vec3  | 输入属性-表示顶点的法线值                                    |
| gl_Vertex              | vec4  | 输入属性-表示物体空间的顶点位置                              |
| gl_MultiTexCoordn      | vec4  | 输入属性-表示顶点的第n个纹理的坐标                           |
| gl_FogCoord            | float | 输入属性-表示顶点的雾坐标                                    |
| gl_Position            | vec4  | 输出属性-变换后的顶点的位置，用于后面的固定的裁剪等操作。所有的顶点着色器都必须写这个值。 |
| gl_ClipVertex          | vec4  | 输出坐标，用于用户裁剪平面的裁剪                             |
| gl_PointSize           | float | 点的大小                                                     |
| gl_FrontColor          | vec4  | 正面的主颜色的varying输出                                    |
| gl_BackColor           | vec4  | 背面主颜色的varying输出                                      |
| gl_FrontSecondaryColor | vec4  | 正面的辅助颜色的varying输出                                  |
| gl_BackSecondaryColor  | vec4  | 背面的辅助颜色的varying输出                                  |
| gl_TexCoord[]          | vec4  | 纹理坐标的数组varying输出                                    |
| gl_FogFragCoord        | float | 雾坐标的varying输出                                          |

**片段着色器的内置变量**

| 名称              | 类型  | 描述                                                         |
| :---------------- | :---- | :----------------------------------------------------------- |
| gl_Color          | vec4  | 包含主颜色的插值只读输入                                     |
| gl_SecondaryColor | vec4  | 包含辅助颜色的插值只读输入                                   |
| gl_TexCoord[]     | vec4  | 包含纹理坐标数组的插值只读输入                               |
| gl_FogFragCoord   | float | 包含雾坐标的插值只读输入                                     |
| gl_FragCoord      | vec4  | 只读输入，窗口的x,y,z和1/w                                   |
| gl_FrontFacing    | bool  | 只读输入，如果是窗口正面图元的一部分，则这个值为true         |
| gl_PointCoord     | vec2  | 点精灵的二维空间坐标范围在(0.0, 0.0)到(1.0, 1.0)之间，仅用于点图元和点精灵开启的情况下。 |
| gl_FragData[]     | vec4  | 使用glDrawBuffers输出的数据数组。不能与gl_FragColor结合使用。 |
| gl_FragColor      | vec4  | 输出的颜色用于随后的像素操作                                 |
| gl_FragDepth      | float | 输出的深度用于随后的像素操作，如果这个值没有被写，则使用固定功能管线的深度值代替 |

### 内置函数

glsl提供了非常丰富的函数库,供我们使用,这些功能都是非常有用且会经常用到的. 这些函数按功能区分大改可以分成7类:

**通用函数:**

下文中的 类型 T可以是 float, vec2, vec3, vec4,且可以逐分量操作.

| 方法                                                         | 说明                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| T abs(T x)                                                   | 返回x的绝对值                                                |
| T sign(T x)                                                  | 比较x与0的值,大于,等于,小于 分别返回 1.0 ,0.0,-1.0           |
| T floor(T x)                                                 | 返回<=x的最大整数                                            |
| T ceil(T x)                                                  | 返回>=等于x的最小整数                                        |
| T fract(T x)                                                 | 获取x的小数部分                                              |
| T mod(T x, T y) T mod(T x, float y)                          | 取x,y的余数                                                  |
| T min(T x, T y) T min(T x, float y)                          | 取x,y的最小值                                                |
| T max(T x, T y) T max(T x, float y)                          | 取x,y的最大值                                                |
| T clamp(T x, T minVal, T maxVal) T clamp(T x, float minVal,float maxVal) | min(max(x, minVal), maxVal),返回值被限定在 minVal,maxVal之间 |
| T mix(T x, T y, T a) T mix(T x, T y, float a)                | 取x,y的线性混合,x*(1-a)+y*a                                  |
| T step(T edge, T x) T step(float edge, T x)                  | 如果 x<edge 返回 0.0 否则返回1.0                             |
| T smoothstep(T edge0, T edge1, T x) T smoothstep(float edge0,float edge1, T x) | 如果x<edge0 返回 0.0 如果x>edge1返回1.0, 否则返回Hermite插值 |

**角度&三角函数:**

下文中的 类型 T可以是 float, vec2, vec3, vec4,且可以逐分量操作.

| 方法                                | 说明                    |
| :---------------------------------- | :---------------------- |
| T radians(T degrees)                | 角度转弧度              |
| T degrees(T radians)                | 弧度转角度              |
| T sin(T angle)                      | 正弦函数,角度是弧度     |
| T cos(T angle)                      | 余弦函数,角度是弧度     |
| T tan(T angle)                      | 正切函数,角度是弧度     |
| T asin(T x)                         | 反正弦函数,返回值是弧度 |
| T acos(T x)                         | 反余弦函数,返回值是弧度 |
| T atan(T y, T x) T atan(T y_over_x) | 反正切函数,返回值是弧度 |

**指数函数:**

下文中的 类型 T可以是 float, vec2, vec3, vec4,且可以逐分量操作.

| 方法               | 说明                        |
| :----------------- | :-------------------------- |
| T pow(T x, T y)    | 返回x的y次幂 xy             |
| T exp(T x)         | 返回x的自然指数幂 ex        |
| T log(T x)         | 返回x的自然对数 ln          |
| T exp2(T x)        | 返回2的x次幂 2x             |
| T log2(T x)        | 返回2为底的对数 log2        |
| T sqrt(T x)        | 开根号 √x                   |
| T inversesqrt(T x) | 先开根号,在取倒数,就是 1/√x |

**几何函数:**

下文中的 类型 T可以是 float, vec2, vec3, vec4,且可以逐分量操作.

| 方法                            | 说明                                                         |
| :------------------------------ | :----------------------------------------------------------- |
| float length(T x)               | 返回矢量x的长度                                              |
| float distance(T p0, T p1)      | 返回p0 p1两点的距离                                          |
| float dot(T x, T y)             | 返回x y的点积                                                |
| vec3 cross(vec3 x, vec3 y)      | 返回x y的叉积                                                |
| T normalize(T x)                | 对x进行归一化,保持向量方向不变但长度变为1                    |
| T faceforward(T N, T I, T Nref) | 根据 矢量 N 与Nref 调整法向量                                |
| T reflect(T I, T N)             | 返回 I - 2 * dot(N,I) * N, 结果是入射矢量 I 关于法向量N的 镜面反射矢量 |
| T refract(T I, T N, float eta)  | 返回入射矢量I关于法向量N的折射矢量,折射率为eta               |

**矩阵函数:**

mat可以为任意类型矩阵.

| 方法                             | 说明                          |
| :------------------------------- | :---------------------------- |
| mat matrixCompMult(mat x, mat y) | 将矩阵 x 和 y的元素逐分量相乘 |

**向量函数:**

下文中的 类型 T可以是 vec2, vec3, vec4, 且可以逐分量操作.

bvec指的是由bool类型组成的一个向量:

```
vec3 v3= vec3(0.,0.,0.);
vec3 v3_1= vec3(1.,1.,1.);
bvec3 aa= lessThan(v3,v3_1); //bvec3(true,true,true)
```

| 方法                                                  | 说明                                     |
| :---------------------------------------------------- | :--------------------------------------- |
| bvec lessThan(T x, T y)                               | 逐分量比较x < y,将结果写入bvec对应位置   |
| bvec lessThanEqual(T x, T y)                          | 逐分量比较 x <= y,将结果写入bvec对应位置 |
| bvec greaterThan(T x, T y)                            | 逐分量比较 x > y,将结果写入bvec对应位置  |
| bvec greaterThanEqual(T x, T y)                       | 逐分量比较 x >= y,将结果写入bvec对应位置 |
| bvec equal(T x, T y) bvec equal(bvec x, bvec y)       | 逐分量比较 x == y,将结果写入bvec对应位置 |
| bvec notEqual(T x, T y) bvec notEqual(bvec x, bvec y) | 逐分量比较 x!= y,将结果写入bvec对应位置  |
| bool any(bvec x)                                      | 如果x的任意一个分量是true,则结果为true   |
| bool all(bvec x)                                      | 如果x的所有分量是true,则结果为true       |
| bvec not(bvec x)                                      | bool矢量的逐分量取反                     |

**纹理查询函数:**

图像纹理有两种 一种是平面2d纹理,另一种是盒纹理,针对不同的纹理类型有不同访问方法.

纹理查询的最终目的是从sampler中提取指定坐标的颜色信息. 函数中带有Cube字样的是指 需要传入盒状纹理. 带有Proj字样的是指带投影的版本.

以下函数只在vertex shader中可用:

```
vec4 texture2DLod(sampler2D sampler, vec2 coord, float lod);
vec4 texture2DProjLod(sampler2D sampler, vec3 coord, float lod);
vec4 texture2DProjLod(sampler2D sampler, vec4 coord, float lod);
vec4 textureCubeLod(samplerCube sampler, vec3 coord, float lod);
```

以下函数只在fragment shader中可用:

```
vec4 texture2D(sampler2D sampler, vec2 coord, float bias);
vec4 texture2DProj(sampler2D sampler, vec3 coord, float bias);
vec4 texture2DProj(sampler2D sampler, vec4 coord, float bias);
vec4 textureCube(samplerCube sampler, vec3 coord, float bias);
```

在 vertex shader 与 fragment shader 中都可用:

```
vec4 texture2D(sampler2D sampler, vec2 coord);
vec4 texture2DProj(sampler2D sampler, vec3 coord);
vec4 texture2DProj(sampler2D sampler, vec4 coord);
vec4 textureCube(samplerCube sampler, vec3 coord);
```





-----

# 相关链接

[GLSL-Card](https://github.com/wshxbqq/GLSL-Card)

https://www.khronos.org/registry/OpenGL/specs/gl/GLSLangSpec.1.20.pdf

https://www.khronos.org/registry/OpenGL-Refpages/gl4/

[GLSL基础](https://www.jianshu.com/p/66b10062bd67)

[着色语言 GLSL 语法介绍](https://juejin.im/post/5d91af05f265da5b9f7c5943)

[1、作者吃代码的兔子窝的《初探 GLSL 着色器（一）》](https://www.wangshaoxing.com/blog/2018-11-06-shader-lession-1.html)

[3、作者吃代码的兔子窝的《初探 GLSL 着色器（二）》](

[3、作者吃代码的兔子窝的《初探 GLSL 着色器（二）》](https://www.wangshaoxing.com/blog/2018-11-06-shader-lession-1.html)

