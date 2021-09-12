---
title: Latex基础学习
top: false
mathjax: true
categories:
- Latex
---

-----





# Latex 公式

求和 积分

- 行内公式与行间公式
- 数学结构的输入
- 数学符号的输入
- 多行公式排版

### 1.显示方法

- 嵌入文字显示：`$...$` eg: `$\sum^n_{i=1}x^i=1$` ==> ∑i=1nxi=1
- 单行显示： `$$...$$` eg: `$ $\sum^n_{i=1}x^i=1$ $` ==>∑i=1nxi=1
- 带序号显示：`\begin{equation}...\end{equation}` eg: `\begin{equation}\sum^n_{i=1}x^i=1\end{equation}` ==>(1)∑i=1nxi=1`\begin{equation}\x+y=z\end{equation}` ==>(2)x+y=z

### 2.通用显示规则

- `^` => 上标
- `_` => 下标
- `{}` => 包括
- `~` => 空格
- `\` => 转移符号

### 3.增强型显示规则

- 求根 eg: `$\sqrt{x+y}$`==> x+y eg: `$\sqrt[3]{x+y}$`==> x+y3
- 分数形式 eg: `$\frac{a+1}{b+2}$` ==> a+1b+2 eg: `${a+1}\over{b+2}$` ==> a+1b+2
- 求和 eg: `$\sum$`==> ∑
- 求积 eg: `$\prod^n_{i=1}i+1=1$` ==> ∏i=1ni+1=1
- 求积分 eg: `$\int x+1$` ==> ∫x+1
- 求积分 eg: `$\iint x+1$` ==> ∬x+1
- 并集 eg: `$A\bigcup B$` ==> A⋃B
- 并集 eg: `$A\cup B$` ==> A∪B
- 交集 eg: `$A\cap B$` ==> A∩B
- 交集 eg: `$A\bigcap B$` ==> A⋂B
- 箭头 eg: `$A\to B$` ==> A→B
- eg: `$\lim_{0\to 100}$` ==> lim0→100
- eg: `$\ln_{0\to 100}$` ==> ln0→100
- eg: `$\sin x$` ==> sin⁡x
- eg: `$\cos x$` ==> cos⁡x
- 8m空格
- 4m空格
- 乘号 eg: `$a \times b$`=a×b
- 除号 eg: `$a\div b$`=a÷b
- {} 花括号显示 eg: `$\\{ hello \\}$` ==> {hello} *注意：在这里我测试的Sublime Text3编写输出的结果， 如果在其他MarkDown编辑器可能要使用单反斜杠*
- 小括号增强 eg: `$\left( hello\right)$` ==> (hello)
- 中括号增强 eg: `$\left[hello\right]$` ==> [hello]
- 绝对值增强 eg: `$\left|hello\right|$` ==> |hello|
- 尖括号 eg: `$\langle hello\rangle$` ==> ⟨hello⟩
- 向上取整 eg: `$\lceil hello\rceil$` ==> ⌈hello⌉
- 向下取整 eg: `$\lfloor hllo\rfloor$` ==> ⌊hllo⌋

### 4.数学符号表一

| 符号 | 显示方法         | 符号 | 显示方法           | 符号 | 显示方法        |
| :--- | :--------------- | :--- | :----------------- | :--- | :-------------- |
| ±    | `\pm`            | ∓    | `\mp`              | ×    | `\times`        |
| ÷    | `\div`           | ∗    | `\ast`             | ⋆    | `\star`         |
| ∘    | `\circ`          | ∙    | `\bullet`          | ⋅    | `\cdot`         |
| ⊕    | `\oplus`         | ⊘    | `\oslash`          | †    | `\dagger`       |
| +    | `+`              | −    | `-`                | ∩    | `\cap`          |
| ∪    | `\cup`           | ‡    | `\ddagger`         | ⋄    | `\diamond`      |
| ⊎    | `\uplus`         | ⊓    | `\sqcap`           | ⊔    | `\sqcup`        |
| ∨    | `\vee`           | ∧    | `\wedge`           | ∖    | `\setminus`     |
| ≀    | `\wr`            | ⊖    | `\ominus`          | ⊙    | `\odot`         |
| △    | `\bigtriangleup` | ▽    | `\bigtriangledown` | ◃    | `\triangleleft` |
| ▹    | `\triangleright` | ⊲    | `\lhd`             | ⊳    | `\rhd`          |
| ⊴    | `\unlhd`         | ⊵    | `\unrhd`           | ⊗    | `\otimes`       |
| ◯    | `\bigcirc`       | ⨿    | `\amalg`           | ∑    | `\sum`          |
| ∏    | `\prod`          | ∐    | `\coprod`          | ∫    | `\int`          |
| ∮    | `\oint`          | ⋂    | `\bigcap`          | ⋃    | `\bigcup`       |
| ⨆    | `\bigsqcup`      | ⋁    | `\bigvee`          | ⋀    | `\bigwedge`     |
| ⨀    | `\bigodot`       | ⨂    | `\bigotimes`       | ⨁    | `\bigoplus`     |
| ⨄    | `\biguplus`      | ≤    | `\leq`             | ≥    | `\geq`          |
| ≡    | `\equiv`         | ≺    | `\prec`            | ≻    | `\cucc`         |
| ∼    | `\sim`           | ⪯    | `\preceq`          | ⪰    | `\succeq`       |
| ≃    | `\simeq`         | ≪    | `\ll`              | ≫    | `\gg`           |
| ≍    | `\asymp`         | ⊂    | `\subset`          | ⊃    | `\supset`       |
| ≈    | `\approx`        | ⊆    | `\subseteq`        | ⊇    | `\subseteq`     |
| ≅    | `\cong`          | ⊏    | `\sqsubset`        | ⊐    | `\sqsupset`     |
| ≠    | `\neq`           | ⊑    | `\sqsubseteq`      | ⊒    | `\sqsupseteq`   |
| ≐    | `\doteq`         | ∈    | `\in`              | ∋    | `\ni`           |
| ∉    | `\notin`         | ⊢    | `\vdash`           | ⊣    | `\dashv`        |
| :    | `:`              | ⊨    | `\models`          | ⊥    | `\perp`         |
| ∣    | `\mid`           | ∥    | `\parallel`        | ⋈    | `\bowtie`       |
| ⋈    | `\Join`          | ⌣    | `\smile`           | ⌢    | `\frown`        |
| ∝    | `\propto`        | <    | `<`                | >    | `>`             |

### 5.数学符号表二

| 符号   | 显示方法  | 符号    | 显示方法  | 符号    | 显示方法  |
| :----- | :-------- | :------ | :-------- | :------ | :-------- |
| arccos | `\arccos` | arcsin  | `\arcsin` | arctan  | `\arctan` |
| arg    | `\arg`    | cos     | `\cos`    | cosh    | `\cosh`   |
| cot    | `\cot`    | coth    | `\coth`   | csc     | `\csc`    |
| deg    | `\deg`    | det     | `\det`    | dim     | `\dim`    |
| exp    | `\exp`    | gcd     | `\gcd`    | hom     | `\hom`    |
| inf    | `\inf`    | ker     | `\ker`    | lg      | `\lg`     |
| lim    | `\lim`    | lim inf | `\liminf` | lim sup | `\limsup` |
| ln     | `\ln`     | log     | `\log`    | max     | `\max`    |
| min    | `\min`    | Pr      | `\pr`     | sec     | `\sec`    |
| sin    | `\sin`    | sinh    | `\sinh`   | sup     | `\sup`    |
| tan    | `\tan`    | tanh    | `\tanh`   | ——      | ——        |

### 5.箭头符号表

| 符号 | 显示方法              | 符号 | 显示方法            | 符号 | 显示方法              |
| :--- | :-------------------- | :--- | :------------------ | :--- | :-------------------- |
| ←    | `\leftarrow`          | ⟵    | `\longleftarrow`    | ⇐    | `\Leftarrow`          |
| ⟸    | `\Longleftarrow`      | →    | `\rightarrow`       | ⟶    | `\longrightarrow`     |
| ⇒    | `\Rightarrow`         | ⟹    | `\Longrightarrow`   | ↔    | `\leftrightarrow`     |
| ⟷    | `\longleftrightarrow` | ⇔    | `\Leftrightarrow`   | ⟺    | `\Longleftrightarrow` |
| ↦    | `\mapsto`             | ⟼    | `\longmapsto`       | ↩    | `\hookleftarrow`      |
| ↪    | `\hookrightarrow`     | ↼    | `\leftharpoonup`    | ⇀    | `\rightharpoonup`     |
| ↽    | `\leftharpoondown`    | ⇁    | `\rightharpoondown` | ⇌    | `\rightleftharpoons`  |
| ⇝    | `\leadsto`            | ↑    | `\uparrow`          | ↓    | `\downarrow`          |
| ⇑    | `\Uparrow`            | ⇓    | `\Downarrow`        | ↕    | `\updownarrow`        |
| ⇕    | `\Updownarrow`        | ↖    | `\nwarrow`          | ↗    | `\nearrow`            |
| ↙    | `\swarrow`            | ↘    | `\searrow`          | ——   | ——                    |

### 6.特殊符号表

| 符号 | 显示方法     | 符号 | 显示方法     | 符号  | 显示方法       |
| :--- | :----------- | :--- | :----------- | :---- | :------------- |
| §§   | `\S`         | ℵ    | `\aleph`     | LATEX | `\LaTeX`       |
| …    | `\ldots`     | ⋱    | `\ddots`     | ⋯     | `\cdots`       |
| ⋮    | `\vdots`     | ı    | `\imath`     | ȷ     | `\jmath`       |
| ℓ    | `\ell`       | ℘    | `\wp`        | ℜ     | `\Re`          |
| ℑ    | `\Im`        | ◊    | `\Diamond`   | ♢     | `\diamondsuit` |
| ′    | `\prime`     | ∅    | `\emptyset`  | ∇     | `\nabla`       |
| √    | `\surd`      | ⊤    | `\top`       | ⊥     | `\bot`         |
| ∖    | `\backslash` | ∂    | `\partial`   | ∞     | `\infty`       |
| △    | `\triangle`  | ♡    | `\heartsuit` | Ⓢ     | `\circledS`    |
| ℏ    | `\hbar`      | ∀    | `\forall`    | ∃     | `\exists`      |
| ¬    | `\neg`       | ♭    | `\flat`      | ♮     | `\natural`     |
| ♯    | `\sharp`     | ∠    | `\angle`     | ℧     | `\mho`         |
| ◻    | `\Box`       | ♣    | `\clubsuits` | ♠     | `\spadesuit`   |

### 7.希腊字母(Greek Letters)显示表

| 拉丁字母 | 小写显示方法 | 大写显示方法 | 大写字母 |
| :------- | :----------- | :----------- | :------- |
| α        | `\alpha`     | ——           | ——       |
| β        | `\beta`      | ——           | ——       |
| γ        | `\gamma`     | `\Gamma`     | Γ        |
| δ        | `\delta`     | `\Delta`     | Δ        |
| ϵ        | `\epsilon`   | ——           | ——       |
| ζ        | `\zeta`      | ——           | ——       |
| η        | `\eta`       | ——           | ——       |
| θ        | `\theta`     | `\Theta`     | Θ        |
| ι        | `\iota`      | ——           | ——       |
| κ        | `\kappa`     | ——           | ——       |
| λ        | `\lambda`    | `\lambda`    | λ        |
| μ        | `\mu`        | ——           | ——       |
| ν        | `\nu`        | ——           | ——       |
| ξ        | `\xi`        | `\Xi`        | Ξ        |
| ο        | `\omicron`   | ——           | ——       |
| π        | `\pi`        | `\Pi`        | Π        |
| ρ        | `\rho`       | ——           | ——       |
| σ        | `\sigma`     | `\Sigma`     | Σ        |
| τ        | `\tau`       | ——           | ——       |
| υ        | `\upsilon`   | `\Upsilon`   | Υ        |
| ϕ        | `\phi`       | `\Phi`       | Φ        |
| χ        | `\chi`       | ——           | ——       |
| ψ        | `\psi`       | `\Psi`       | Ψ        |
| ω        | `\omega`     | `\Omega`     | Ω        |

*注意：将小写字母的首字母大写表示为大写拉丁字母*

# 相关链接

[https://lixingcong.github.io/2016/04/04/LaTex-intro/](https://lixingcong.github.io/2016/04/04/LaTex-intro/)

[LaTeX 第五课：数学公式排版](https://zhuanlan.zhihu.com/p/24502400)

[LaTex 公式语法示例](https://www.jianshu.com/p/0d442af1541e)

[Latex数学公式表](https://blog.csdn.net/u011826404/article/details/70215074)

[MarkDown 插入数学公式实验大集合](https://juejin.im/post/5a6721bd518825733201c4a2#heading-17)

[https://lixingcong.github.io/2016/04/04/LaTex-intro/](https://lixingcong.github.io/2016/04/04/LaTex-intro/)

[https://xjay.net/201902/latex-syntax-for-math-equation/](https://xjay.net/201902/latex-syntax-for-math-equation/)

[http://www.mohu.org/info/lshort-cn.pdf](http://www.mohu.org/info/lshort-cn.pdf)

[http://mohu.org/info/symbols/symbols.htm](http://mohu.org/info/symbols/symbols.htm)

[https://www.luogu.com.cn/blog/IowaBattleship/latex-gong-shi-tai-quan](https://www.luogu.com.cn/blog/IowaBattleship/latex-gong-shi-tai-quan) | LaTeX数学公式大全 - Iowa_BattleShip 的博客 - 洛谷博客 

[http://mohu.org/info/symbols/symbols.htm](http://mohu.org/info/symbols/symbols.htm) | mohu.org/info/symbols/symbols.htm

 [http://www.mohu.org/info/lshort-cn.pdf](http://www.mohu.org/info/lshort-cn.pdf) | lshort-cn.pdf 

[https://xjay.net/201902/latex-syntax-for-math-equation/](https://xjay.net/201902/latex-syntax-for-math-equation/) | LaTeX 数学公式语法 | Vault 101 

[https://lixingcong.github.io/2016/04/04/LaTex-intro/](https://lixingcong.github.io/2016/04/04/LaTex-intro/) | LaTex数学公式语法 | Lixingcong 

[https://www.jianshu.com/p/0d442af1541e](https://www.jianshu.com/p/0d442af1541e) | LaTex 公式语法示例 - 简书 

[https://blog.csdn.net/u011826404/article/details/70215074](https://blog.csdn.net/u011826404/article/details/70215074) | Latex数学公式表_网络_VAY-长跑-CSDN博客 

[https://juejin.im/post/5a6721bd518825733201c4a2#heading-17](https://juejin.im/post/5a6721bd518825733201c4a2#heading-17) | MarkDown 插入数学公式实验大集合 - 掘金 

[https://www.cnblogs.com/houkai/p/3399646.html](https://www.cnblogs.com/houkai/p/3399646.html) | LATEX数学公式基本语法 - 侯凯 - 博客园 

[https://pandoc.herokuapp.com/](https://pandoc.herokuapp.com/) | Markx 

[https://hands133.github.io/2018/06/11/hexo-%E4%B8%AD%E6%B8%B2%E6%9F%93-LaTeX-%E6%95%B0%E5%AD%A6%E5%85%AC%E5%BC%8F/](https://hands133.github.io/2018/06/11/hexo-%E4%B8%AD%E6%B8%B2%E6%9F%93-LaTeX-%E6%95%B0%E5%AD%A6%E5%85%AC%E5%BC%8F/) | hexo 中渲染 LaTeX 数学公式 | 十兽鉴 (Mason)

[https://www.jianshu.com/p/d7c4cf8dc62d](https://www.jianshu.com/p/d7c4cf8dc62d)

[https://www.jianshu.com/p/9bf16599e220](https://www.jianshu.com/p/9bf16599e220)

[https://www.jianshu.com/p/97ec8a3739f6](https://www.jianshu.com/p/97ec8a3739f6)