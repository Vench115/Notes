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

我们可以用更加细节的(sophisticated)动作来扩展这个列表，来描述更加


## The symbolic deduction engine



## Algebraic reasoning



## Deductive database implementation



## Traceback to find minimal proofs



## Traceback for geometric-rule deduction