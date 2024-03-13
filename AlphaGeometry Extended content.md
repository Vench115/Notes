# Methods

## Geometry representation

通用的形式化语言形式化语言，比如Lean，那么目前仍然需要大量的准备工作(groundwork)来描述大多的IMO几何题目。作者没有直接解决这个挑战，因为它需要深厚的专业知识，以及大量的在定理证明方法论之外的研究。

为了规避这个障碍，作者转而采用了一个更加专业的(specialized)的语言，用于GEX, JGEX, MMP/Geometer 以及 Geologic中，为带有类人的、非退化性的、以及拓扑假设，提供逻辑和图形环境的一系列工作。

这个语言的例子展示在Fig. 1 d,f。 由于更加严谨的形式化，整个IMO几何问题中的75%能够适用于这种表示方法。在这类几何环境中，每个证明步骤都能够被逻辑以及数值验证的，而且能够被一个人类读者验证，就好像它是IMO竞赛选手写的一样，多亏了这个语言高度接近自然语言的语法。

为了覆盖解释性(expressive)更强的代数(algebraic)和算数(arithmetic)推理，作者也增加了整数、分数、以及几何常量到这个语言中。作者并没有更加进一步地得到几何表示的完善解法，因为它是一个完全不同，而且极具挑战性的研究主题，这需要数学形式化社区中大量的研究(investment)。

## Sampling consistent theorem premises

采样固定的定理前提

作者开发了一个图像构造语言(constructive diagram builder language)，它与 JGEX 中使用的类似，是用于每次在前提下构造一个对象(object)，而不是随意地(freely)采样很多包含不同对象的前提(premises)，因此可以避免生成前提的自相矛盾的几何。

Extended Data Table 1中又一个完善的，关于构造动作(actions)的列表。这些动作包括构造操作，构造可以以一个特定的方式创建与其他对象相关的点，也就是共线的、内心/旁心(incentre/excentre)等等。同样的构造还可以将一个数字作为它的参数，例如：“构造点X使得给定一个数字α，令∠ABX=α“。

我们可以用更加复杂巧妙的(sophisticated)动作来扩展这个列表，来描述更具解释性的几何情况(scenarios)的几何，这就能同时提升综合数据的多样性，以及测试集的覆盖程度(coverage)。

在ref. 32中可以找到一个更加普遍、更具解释性的图像构建语言。但是作者使用的是一个更简单的语言，这个(形式化)语言已经足够来描述IMO-AG-30中的问题了，而且也能与符号引擎DD工作得很好。

## The symbolic deduction engine

符号推理引擎的核心功能(functionality)是推理出新的真命题(true statements)，在给定了定理前提的情况下。引擎能够通过几何规则，比如“如果X，则Y”这样的规则，来进行推理(deduction)，其中X和Y都是几何命题(statements)的集合，例如“A,B,C是共线的”。

由于这个原因，作者使用了结构化的DD(Deductive Database)的方法，因为这种方法能够在仅仅几秒钟内找到推理闭包(deduction closure)，而且是在一个标准的、未部署加速器的(non-accelerator)硬件上。

为了进一步增强推理能力，作者也赋予(built into)AlphaGeometry通过AR(Algebraic Rules 代数)进行推理的能力。AR可以让证明中包含角度/比例/距离的追踪(chasing)。详细的AR例子在Extended Data Table 2展示。

这样的证明步骤在几何证明中是普遍存在的(ubiquitous)，然而并没有被几何规则所覆盖。作者扩展了在GeoLogic中实现的高斯消元过程(Gaussian elimination process)，为了能够在几秒之内，找到符合所有可能的线性算子(operators)。

作者的符号推理引擎是DD和AR的精密的融合(intricate integration)，作者也就用它来扩展已知真命题的联合闭包(closure)，直到扩展停止(halt)。这个过程通常在几秒钟之内，最多到几分钟之内就停止了，而且是在一个标准的不使用加速器的硬件上。

## Algebraic reasoning

代数推理

## 1

在几何定理证明的研究领域中，对于代数推理还没有一个完善的解决方法(treatment)。例如，在iGeoTutor中，Z3(ref. 33)被用来解决算数推断(arithmetic inferences)，但是代数运算(manipulations)还没有被触及(covered)。

DD(ref. 17)通过在一些有限的推理规则中解释代数推理来解决这个问题，因此，这种方法就不能解决更复杂的运算了，也就造成算数推论没有被解决(covered)。

目前，最普遍的解决方法是类似于ref. 34中的一个过程，但只能用于角的定理发现(angle-only theorem discovery)，而且只能在GeoLogic中实现角和比例。

作者扩展了这个设计(formulation)来副高所有关于点之间的角、比例和距离的推理，也包含了带有几何图形常量，比如 $\pi$ 或者 $\frac 1 2$ 的算数推理。在Extended Data Table 2中有非常具体的代数推理的例子。

## 2

从更高的维度来说，作者首先将输入的线性方程转化为他们系数的矩阵。特别地，作者创建了一个系数矩阵 $A \in R^{M \times N}$ ，其中N是变元(variables)的个数，M是输入方程的个数。

在几何中，任何如同形式 $a-b=c-d \Leftrightarrow a-b-c+d=0$ 。例如，角度等式 $\angle ABC = \angle XYZ$ ，可以被表示为 $s(AB) - s(BC) = s(XY) - s(YZ)$ ，其中 $s(AB)$ 是AB和x方向之间的角度，以 $\pi$ 为模。

同样地，比例 $AB:CD = EF:GH$ 可以被表示为 $\log {(AB)} - \log {(CD)} = \log {(EF)} - \log {(GH)}$ ，其中 $\log{(AB)}$ 是线段AB长度的对数。对于距离，每个变量都是一个(点，线)对，代表具体线段上的一个具体的点。

## 3 (化最简型矩阵)

因为所有的等式都是 ”$a-b-c+d=0$“ 的形式，作者用每个等式填充矩阵的每一行，用 ”$+1,-1,-1,+1$“ 的值来填充与变量 $a,b,c,d$ 相关的列。在A上运行高斯消元，并返回一个每一列第一个元素都是1的新矩阵，本质上代表了每个变量都是一个剩余元素的线性组合。

举个例子来说，作者有 "$a-b=b-c, d-c=a-d, b-c=c-e$" 作为输入的等式，使用高斯消元过程，就可以返回如下的结果。

$$
begin matrix
$$


## 4

从这个结果中，作者可以确切地(deterministically)而且彻底地推理出所有新的等式，只要通过检查：$如果x_1 = x_2 或 x_1 - x_2 = x_2 - x_3 或 x_1 - x_2 = x_3 - x_4$ ，其中 $\{ x_1, x_2, x_3, x_4\}$ 是所有变量的任意4-置换(4-permutation)。

在上述的高斯消元中，例如，AR会从三个输入的等式中推理出 $b=d$ 。为了解决几何常量，例如 ”$0.5 \pi$“ 或者 ”$\frac 5 {12}$“，作者将 ”$\pi$“ 和 ”1“ 作为默认的变量记入所有的系数矩阵。

## Deductive database implementation

推理数据库实现

不同于原先的(original)DD的实现，作者使用了图数据结构来捕捉(capture)几何的对称信息(symmetries)，而不是使用标准的(canonical)字符串的形式。由于使用了图的数据结构，作者可以捕捉到不仅仅是函数参数(function arguments)的对称置换，还可以捕捉到等式的传递性(transitivity)、共线性(collinearity)、以及周期性(concyclicity)。

这种图的数据结构将它自己融入(bakes itself)进了一些推理规则中，可以在用于DD的几何规则列表中明显地看到。这些从原始列表里获得的推理规则，因此没有被用在探索的任何地方，而是隐晦地使用了，而且在有需要的时候就能够明确地详细说明，只要当最终地证明被序列化成文本时。


## Traceback to find minimal proofs

回溯来找到最小的证明

## Traceback for geometric-rule deduction

为了几何规则推理的回溯
