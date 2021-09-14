---
title: PPM、PGM、PBM图像格式
top: false
mathjax: true
date: 2020-01-01 13:45:33
categories:
- 工具
---

-----



## PPM、PGM、PBM图像格式

## 背景

PPM[^ppm]、PGM[^pgm]、PBM[^pbm]格式由Jef Poskanzer在20世纪80年代发明，为了便于通过电子邮件，用**ASCII**码表示单色位图，能够承受一般的文本格式的变动。

第一个处理PBM格式的工具库是**Pbmplus**。它由这个格式的发明人Jef Poskanzer开发，在1988年发布。主要包含Jef编写的将PBM转化为已存在的其他图像格式的工具。在1988年末，Jef开发出PGM、PPM格式以及相关工具，并加入Pbmplus中。Pbmplus的最终发布日期是1991年12月10日。

在1993年，Netpbm[^2]库开始开发，用来替代不再维护的Pbmplus。它是Pbmplus的简单的重新包装，附加全世界开发者提供的额外功能和修订，可能是目前用的最普遍的处理PBM、PGM和PPM格式的工具库。[^1]



## 文件格式

这三种格式在颜色的表示上有差异。PBM是单色，PGM是灰度图，PPM使用RGB颜色。

每个文件的开头两个字节（ASCII码）作为文件描述子，指出具体格式和编码形式。具体见下表。

| 文件描述子 |  类型  |  编码  |
| :--------: | :----: | :----: |
|    `P1`    |  位图  | ASCII  |
|    `P2`    | 灰度图 | ASCII  |
|    `P3`    | 像素图 | ASCII  |
|    `P4`    |  位图  | 二进制 |
|    `P5`    | 灰度图 | 二进制 |
|    `P6`    | 像素图 | 二进制 |

基于ASCII的格式使人可读，并且能够很容易的移植到其他格式。但是二进制格式更有效，不仅因为他节约空间，而且因为他更容易被解析（因为很少有空格）

当使用二进制格式的时候，PBM每像素使用一个比特空间，PGM每个像素使用8个比特空间，PPM每像素使用24比特空间（8比特红色、8比特绿色、8比特蓝色）。



## PPM

PPM图像格式分为两部分，分别为头部分和图像数据部分。

- **头部分：**

  由3部分组成，通过换行或空格进行分割，一般PPM的标准是空格。
  第1部分：P3或P6，指明PPM的编码格式，
  第2部分：图像的宽度和高度，通过ASCII表示，
  第3部分：最大像素值，0-255字节表示。

- **图像数据部分：**

  ASCII格式：按RGB的顺序排列，RGB中间用空格隔开，图片每一行用回车隔开。
  Binary格式：PPM用24bits代表每一个像素，红绿蓝分别占用8bits。

`#` 开头表示注释

**示例：**

```markdown
P3
4 4
15
0 0 0 0 0 0 0 0 0 15 0 15
0 0 0 0 15 7 0 0 0 0 0 0
0 0 0 0 0 0 0 15 7 0 0 0
15 0 15 0 0 0 0 0 0 0 0 0
```



## PGM

PGM图像格式分为两部分，分别为头部分和图像数据部分。

- **头部分：**

  由3部分组成，通过换行或空格进行分割，一般PGM的标准是空格。
  第1部分：P2或P5，指明PPM的编码格式，
  第2部分：图像的宽度和高度，通过ASCII表示，
  第3部分：最大灰度值（Maxval），还是ASCII十进制。必须小于65536，并且大于零。

- **图像数据部分：**

  ASCII格式：
  Binary格式：

`#` 开头表示注释





**示例：**

```markdown
P2
# feep.pgm
24 7
15
0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
0  3  3  3  3  0  0  7  7  7  7  0  0 11 11 11 11  0  0 15 15 15 15  0
0  3  0  0  0  0  0  7  0  0  0  0  0 11  0  0  0  0  0 15  0  0 15  0
0  3  3  3  0  0  0  7  7  7  0  0  0 11 11 11  0  0  0 15 15 15 15  0
0  3  0  0  0  0  0  7  0  0  0  0  0 11  0  0  0  0  0 15  0  0  0  0
0  3  0  0  0  0  0  7  7  7  7  0  0 11 11 11 11  0  0 15  0  0  0  0
0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0  0
```





## PBM

PBM图像格式分为两部分，分别为头部分和图像数据部分。

- **头部分：**

  由2部分组成，通过换行或空格进行分割，一般PBM的标准是空格。
  第1部分：P1或P4，指明PBM的编码格式，
  第2部分：图像的宽度和高度，通过ASCII表示，

- **图像数据部分：**

  ASCII格式：
  Binary格式：

`#` 开头表示注释

- 图像数据部分中的每个像素都由一个包含ASCII“ 1”或“ 0”的字节表示，分别表示黑色和白色。在一行的末尾没有填充位。

- 每行不得超过70个字符。

**示例：**

```markdown
P1
# This is an example bitmap of the letter "J"
6 10
0 0 0 0 1 0
0 0 0 0 1 0
0 0 0 0 1 0
0 0 0 0 1 0
0 0 0 0 1 0
0 0 0 0 1 0
1 0 0 0 1 0
0 1 1 1 0 0
0 0 0 0 0 0
```



## ppm软件及在线网站

**网站：**

[ppm-jpg](https://convertio.co/zh/ppm-jpg/)

[转换PPM](https://onlineconvertfree.com/zh/convert/ppm/)

[ppm_png](https://www.alltoall.net/ppm_png/)

[convert-to-ppm](https://jinaconvert.com/cn/convert-to-ppm.php)

**软件：**

[ImageMagick ](https://legacy.imagemagick.org/)

## 相关文章

[libnetpbm](http://netpbm.sourceforge.net/doc/libnetpbm.html)

[ppm file](http://paulbourke.net/dataformats/ppm/)

[PBM, PGM, PNM, and PPM File Format Summary](https://www.fileformat.info/format/pbm/egff.htm)

[PBM, PGM, and PPM files](http://web.eecs.utk.edu/~ssmit285/guide/img/index.html)

[The PNM Format (sourceforge.net)](http://netpbm.sourceforge.net/doc/pnm.html)



***

[^1]: [PBM格式 - 维基百科，自由的百科全书 (wikipedia.org)](https://zh.wikipedia.org/zh-hans/PBM格式)
[^2]: https://en.wikipedia.org/wiki/Netpbm
[^ppm]: http://netpbm.sourceforge.net/doc/ppm.html
[^pbm]: http://netpbm.sourceforge.net/doc/pbm.html
[^pgm]: http://netpbm.sourceforge.net/doc/pgm.html
[^Netpbm history]: https://web.archive.org/web/20090218192135/http://netpbm.sourceforge.net/history.html
[^Netpbm]: https://en.wikipedia.org/wiki/Netpbm

