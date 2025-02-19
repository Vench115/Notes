[[2502.03544] Gold-medalist Performance in Solving Olympiad Geometry with AlphaGeometry2](https://arxiv.org/abs/2502.03544)

[AlphaGeometry2: Deepmind AI outperforms math Olympians at geometry tasks](https://the-decoder.com/alphageometry2-deepmind-ai-outperforms-math-olympians-at-geometry-tasks/)

# Abstract

作者团队推出了Alpha Geometry 2，一个由[[Alpha Geometry]]提出的Alpha Geometry的极大改进版本，目前，该版本已经在求解奥林匹克级别的几何问题中，超越了平均金牌选手的水平。为了达到该目标，作者首先扩展了原本的AG语言，来求解(tackle)更难的问题，包括对象的运动，以及包括角度、比例、和距离的线性方程组问题。这一步，以及其他内容的增加，极大地改进了AG语言在IMO 2000-2024几何问题上的覆盖率，从66%提升到了88%。

AG2的搜索过程也有了很大的改进，通过使用Gemini的架构，来达到更好的语言建模(language modeling)，以及一个全新的知识共享(knowledge-sharing)机制，集合了多重搜索树。

除了进一步加强符号引擎和综合数据生成，作者也大力地加速了AG2对过去25年里所有几何问题的整体求解率，从先前的54%提升到了84%。

AG2也是在IMO 2024中达到银牌等级的求解系统的一部分。最后，同样重要的是，作者团队报告了整个过程，针对使用AG2作为一个完全自动化系统的一部分，来直接从自然语言输入中可靠地(reliably)求解几何问题。

# Introduction

## Background

IMO对于全世界的高中学生来说，都是一个享誉盛名的数学竞赛。IMO问题以它们的高难度而被人所熟知，而且求解这些问题需要对于数学概念的深度理解，以及创造性地应用它们的能力。几何，四大IMO问题范畴之一，是几类问题中形式最统一的一个，因此也最容易理解(approachable)。这类问题也非常适合基本的推理研究。

## Approaches

自动化求解几何问题，主要有两种技术路径。一种，是用代数方法“痛击”(bashing)问题，利用比如“吴法”；面积法；或者Grobner基方法。另一种，则依赖于合成技巧(synthetic techniques)例如推理数据库、或全角法。作者关注于后者，作为一种更加与人类似的方法路径，也适合于将研究知识转移到其他领域。在作者先前的工作中，作者提出了AlphaGeometry(AG1)，一个神经-符号系统，展现了完全统治(mastering)该领域的一大步，达到了在2000-2024全部IMO几何问题上54%的求解率。AG1结合了一个语言模型和一个符号引擎，来有效地解决这些极具挑战性的问题。

##  Limitations

尽管它取得了成功，AG1在几个关键领域还是展现出了局限性。它的性能被它领域专用语言的范围、符号引擎的效率、以及初始的语言模型的能力所限制。因此，当考虑到最近从2000年至今的所有IMO几何问题，AG1仅能达到54%的求解率。

## Improvements

该文章则提出了AlphaGeometry2(AG2)，是对AG1的大幅升级，突破了这些限制，而且增强了性能。AG2利用(leverages)一个更强大的，基于Gemini的语言模型，它是在一个更大更多样的数据集上进行训练的。

作者也提出了一个显著更快，且更加鲁棒的符号引擎，也加入了很多优化，例如：一个简化的规则集合，以及更强的对双点的处理能力。

不仅如此，作者团队扩展了领域语言，以覆盖范围更广的几何概念，包括轨迹定理(locus theorems)以及线性方程。

为了进一步改善性能，作者开发了一个全新的搜索算法，能够探索更宽范围的辅助构造策略，并运用一个知识共享的机制，来扩展并加速搜索过程。

最终，作者在构建一个完全自动化的而且可靠的系统上取得了进展，该系统能够以自然语言的方式求解几何问题。为了实现该目标，作者使用Gemini将问题从自然语言翻译成AG的语言，并且实现了一个新的自动化图生成算法。

## AG2 Key Improvements

- Expanded Domain Language(扩展的领域专用语言)：覆盖轨迹型的定理、线性方程、以及非构造性的(non-constructive)问题声明。
- Stronger and faster Symbolic Engine(更强更快的符号引擎)：优化的规则集合，新增的对于双点的处理，以及更快的用C++的实现。
- Advanced Novel Search Algorithm：利用多重的搜索树，且利用知识共享。
- Enhanced Language Model：利用Gemini架构，在一个更大更多样的数据集上训练。

# More general domain language

首次提出是在AG1中，AG1使用一个简单的领域专用的语言，包含了九个基本的“谓词”，在Table 1中列出。当这些谓词足够覆盖所有2000-2024 IMO中66%的几何问题时，AG1的语言并不允许讨论线性方程、点/线/圆的移动、或一些常见问题，比如“求角度……”。下表展示了作者的AG2如何解决这些和其他挑战。

| Name             | Meaning                                                       |
| :--------------- | :------------------------------------------------------------ |
| cong a b c d     | $AB = CD$                                                     |
| perp a b c d     | $AB \perp CD$                                                 |
| $\dots$          | $\dots$                                                       |
| aconst a b c d x | Angle Between AB and CD is equal to x, where $x \in [0, 180)$ |
| rconst a b c d y | AB:CD = y where y is a constant                               |

上述Table 1是AG1的谓词。

首先，AG2新增了两个谓词来允许问题类型：“求x”：

1. `acompute a b c d` 表示：求解AB和CD之间的角度。
2. `rcompute a b c d` 表示：求解AB/CD的比例。

在一些几何问题中，包括出现在IMO 2024中的那道题目，存在几何量的线性方程(角度，距离)，而这些是AG1不能捕捉到的。为了表达这些想法，AG2增加了下述的三个谓词：

1. `distmeq a1 b1 a2 b2 ... an bn t1 t2 ... tn y` 表示：$t_1 \log(A_1 B_1) + t_2 \log(A_2 B_2) + \dots + t_n \log(A_n B_n) + y = 0$
2. `distseq a1 b1 a2 b2 ... an bn t1 t2 ... tn` 表示：$t_1 A_1 B_1 + t_2 A_2 B_2 + \dots + t_n A_n B_n = 0$
3. `angeq a1 b1 a2 b2 ... an bn t1 t2 ... tn y` 表示：$t_1 d(A_1 B_1) + t_2 d(A_2 B_2) + \dots + t_n d(A_n B_n) + y = 0$，其中 $d(AB)$ 是无向线段AB和水平线之间的角度。

另一类AG1不支持的类别是，所谓的轨迹问题，主要描述的是如点、线、圆的对象的运动。AG2通过一个新的谓词语法捕捉这种运动。Table 2列出了11个轨迹的例子，以及相关的谓词和它们的语法。此处，作者利用了新的标记 `*` 来充当固定点的占位符。

进一步地，在AG2的证明中，作者引入了明确地谓词，来表示拓扑 / 非退化条件的几何图检查。

1. `sameclock a b c d e f` 表示：方向 $A \to B \to C$ 与 $D \to E \to F$ 有同样的顺时针方向。
2. `noverlap a b` 表示：A和B是不同的点。
3. `lessthan a b c d` 表示：$AB<CD$ ，是一个在SSA的三角同余定理中使用的声明。

AG2也能通过引入新的谓词，来证明点是相同的，`overlap a b` (点A和B是重合的点)，其中任何涉及A点的谓词也能够被用于B点，反之亦然。在推理闭包中，重合点可以通过成为同一个圆的圆心来定义；因此，作者引入了另一个谓词 `cyclic_with_center` 来捕捉这种情况。此处，`cyclic_with_center a1 a2 ... an x` 表示 $a_1 = a_2 = \dots = a_x$ 是圆的圆心，直到 $a_{x+1 \dots a_n}$ (以免x=0，它也等价于 `cyclic`)。

>Notice that, when describing a problem, AG1 uses at most 2 predicates to define a point, i.e. each point is defined as the intersection between at most two objects (line or circle).

注意到，当描述一个问题时，AG1使用最多2个谓词来定义一个点，也就是说，每个点被定义为至多两个对象(线或圆)的交点。

>This limits AG1 to only constructive problems - problems where all points can be straightforwardly constructed by following their definition order and taking the intersection of two well-defined objects.

这限制了AG1仅能解决构造型问题，这些问题中所有的点都是由他们定义顺序，以及用两个已经完整定义好的对象的交点来定义。

在AG2中，作者放宽了这个约束，来覆盖更多的问题，那些问题中能够由最少三个谓词来定义，使得图形的构造不再平凡(non-trivial)。作者用于自动化该过程的方法将在下一节讨论。

在该部分中描述的所有更新，提高了AG语言的覆盖率，根据2000-2024年所有IMO的几何问题，从66%到88%。剩余的12%包含3D几何、不等式、非线性方程，以及可数的但很多的点(也就是那些含有n个点的问题，而n是一个任意的正整数)。所有被AG1和AG2覆盖或没覆盖的问题，都可以在Figure 8中找到。那些没有被覆盖的问题都被标为“Not attempted”。

# Automated problem formalization and diagram generation

## Automated formalization

AG以及其他类似的神经-符号系统的一个很大的弱点，是需要将输入的问题，从自然语言人工翻译成一种领域内专用的语言。例如，一道简单的以自然语言描述的几何问题：“给定一个三角形ABC，边AB=AC，证明角B等于角C。”，会被翻译成：`triangle a b c; a b = a c ? eqangle b a b c c b c a` ，这才是AG专用的语言。

将这个过程自动化之后，就是所谓的形式化(formalization)，这是一个非常热门的研究领域。这相比于人类语言之间的翻译，是一个复杂得多的问题。翻译只是为了保持原意，而形式化则频繁地需要将原问题重新公式化(re-formulating)为一种可以替换的形式，而且有时候还需要剔除原本题目声明中那些存在二义性的细微差别(disambiguating the nuances)。

自动形式化(auto-formalization)，因而需要极强的背景知识，以及本身就需要的问题求解能力。考虑到最近的基础模型开始展现出这种能力，作者也就使用了一个这类的模型，Gemini，来自动为AG完成形式化的工作。

作者开始是手工将几十个几何问题翻译成AG语言，然后利用这些例子来撰写一个少样本提示词(few-shot prompt)，要求Gemini将一道给定的几何问题，从自然语言翻译成AG语言。作者使用该提示词问询Gemini五次，然后再次调用Gemini，要求将这些结果合并为一个最终的答案。通过这种方法，作者得以形式化39道可形式化的IMO 2000-2024几何问题中的30道。对于更加简单的几何问题，它的表现非常稳定，而且几乎不会犯错。

## Automated diagram generation

另一个作者提出的主要管线中的人工部分，则是图形生成。在AG1中，每个点都由Table 1中至多两个基本的谓词所定义，因此问题都是构造性地定义的，而且图形也能被自动地生成。

>In AG2, we allow one or multiple points being defined simultaneously by an arbitrary number of predicates, allowing us to also cover non-constructive problems.

在AG2中，作者允许一个或多个点被任意数量的谓词同时定义，这就允许我们也能够覆盖那些非构造性的问题。

考虑一个非构造性问题的声明：“令ABC是一个含有内心I的三角形，使得$IA = 2IB$”，此处的I点不仅被定义为了内心，也就是两条内角平分线的交点，还被第三个谓词“$IA = 2IB$”所定义，而且并没有通用的能够构建这四个点的策略。既然AG2覆盖了那些非构造性的问题，图形构建变成了主干中一个重要的部分，而且通常需要人为干预。与Kruger (2021)的工作类似，作者提出了一下的算法，在给定非构造性问题要求的情况下，自动生成图形。

令 $\hat x \in R^{2n}$ 是一个向量，表示所有点的所有坐标。作者

# Stronger and faster symbolic engine

所谓符号引擎，是AG的核心组件。作者称之为DDAR，也就是推理数据库(Deductive Database)和算数推理(Arithmetic Reasoning)。它是一个推理闭包的算法，也就是在给定一个核心初始结论(facts)集合的情况下，得到的推理结论(facts)的集合。DDAR